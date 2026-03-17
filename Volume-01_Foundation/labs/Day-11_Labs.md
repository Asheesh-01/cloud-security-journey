# Day 11 — Labs: Device Drivers

---

## Command 1 — ls /dev | head -20
```bash
ls /dev | head -20
```
Output:
```
autofs
block
bsg
btrfs-control
bus
cdrom
char
console
core
cpu_dma_latency
cuse
disk
dma_heap
dri
fb0
fd
full
fuse
hidraw0
hpet
```
What it means:
- /dev = device directory, every hardware device with a loaded driver appears here as a file
- cdrom = CD/DVD ROM drive, symbolic link to sr0 (seen in lsblk earlier)
- console = system console, kernel output goes here before graphical interface loads
- core = symbolic link to /proc/kcore, represents entire physical RAM as a file — forensic memory dump tool reads this
- dri = Direct Rendering Infrastructure, GPU driver interface for games and 3D applications
- fb0 = framebuffer device 0, represents screen as raw pixel buffer
- fuse = Filesystem in Userspace — VirtualBox VM_FOLDER shared folder uses this
- hidraw0 = Human Interface Device raw device 0 — your keyboard/mouse talking through HID protocol
- block = directory containing symbolic links to all block devices organized by type
- Security use: attackers with root can read /dev/sda directly bypassing all filesystem permissions

---

## Command 2 — lsmod | head -20
```bash
lsmod | head -20
```
Output:
```
Module                  Size  Used by
snd_seq_dummy          12288  0
snd_hrtimer            12288  1
snd_seq               114688  7 snd_seq_dummy
snd_seq_device         16384  1 snd_seq
sunrpc                868352  1
qrtr                   57344  2
rfkill                 45056  2
vboxsf                102400  1
binfmt_misc            28672  1
intel_rapl_msr         20480  0
intel_rapl_common      53248  1 intel_rapl_msr
intel_uncore_frequency_common    16384  0
intel_pmc_core        155648  0
pmt_telemetry          16384  1 intel_pmc_core
pmt_discovery          16384  1 pmt_telemetry
pmt_class              20480  2 pmt_telemetry,pmt_discovery
intel_pmc_ssram_telemetry    16384  1 intel_pmc_core
intel_vsec             28672  2 intel_pmc_ssram_telemetry,pmt_telemetry
hid_generic            12288  0
```
What it means:
- Format: Module name | Size in bytes | Used by count and names
- snd_seq 114688 7 = ALSA sound sequencer, 114KB in kernel memory, 7 things depend on it
- vboxsf 102400 1 = VirtualBox Shared Folders driver — third party module, mounts VM_FOLDER
- rfkill 45056 2 = Radio Frequency kill switch, controls WiFi and Bluetooth on/off
- intel_pmc_core 155648 = Intel Power Management Controller, 155KB — largest Intel module
- hid_generic 12288 0 = HID keyboard/mouse driver, loaded and ready, nothing actively calling it
- sunrpc 868352 1 = Sun RPC for NFS network file systems, largest module at 868KB
- Security use: rootkits appear here as unexpected modules — compare against known good list

---

## Command 3 — uname -r
```bash
uname -r
```
Output:
```
6.18.12+kali-amd64
```
What it means:
- 6 = major kernel version (Linux kernel 6, released 2022)
- 18 = minor version, 18th minor release of kernel 6
- 12 = patch level, 12 bug fix patches applied
- +kali = Kali Linux team added their own patches on top of upstream kernel
- amd64 = 64-bit x86 CPU architecture — your Intel Core Ultra 5 125H
- Security use: drivers compiled for 6.18.12 refuse to load on different versions — kernel ABI compatibility

---

## Command 4 — dmesg | grep -i "usb\|driver" | head -20
```bash
dmesg | grep -i "usb\|driver" | head -20
```
Output:
```
[    0.186384] Performance Events: unsupported CPU family 6 model 170 no PMU driver
[    0.203464] acpiphp: ACPI Hot Plug PCI Controller Driver version: 0.5
[    0.283623] Block layer SCSI generic (bsg) driver version 0.4 loaded
[    0.293040] Serial: 8250/16550 driver, 4 ports, IRQ sharing enabled
[    0.770570] e1000: Intel(R) PRO/1000 Network Driver
[    0.783937] ACPI: bus type USB registered
[    0.783958] usbcore: registered new interface driver usbfs
[    0.783962] usbcore: registered new interface driver hub
[    0.783975] usbcore: registered new device driver usb
[    0.806202] ehci-pci 0000:00:0b.0: new USB bus registered, assigned bus number 1
[    0.820495] ehci-pci 0000:00:0b.0: USB 2.0 started, EHCI 1.00
[    0.820535] usb usb1: New USB device found, idVendor=1d6b, idProduct=0002
[    0.820537] usb usb1: New USB device strings: Mfr=3, Product=2, SerialNumber=1
[    0.820538] usb usb1: Product: EHCI Host Controller
[    0.820538] usb usb1: Manufacturer: Linux 6.18.12+kali-amd64 ehci_hcd
[    0.820539] usb usb1: SerialNumber: 0000:00:0b.0
[    0.820620] hub 1-0:1.0: USB hub found
[    1.284626] ohci-pci 0000:00:06.0: new USB bus registered, assigned bus number 2
[    1.341908] usb usb2: New USB device found, idVendor=1d6b, idProduct=0001
```
What it means:
- Numbers in brackets = seconds since boot — [0.186384] = 186 milliseconds after power on
- [0.186384] no PMU driver = kernel tried to load performance monitoring driver for Intel Core Ultra 5 125H (family 6 model 170) — CPU too new, no driver yet, falls back to software mode
- [0.770570] e1000 = Intel gigabit Ethernet network driver loaded at 770ms — this is your VM network card driver
- [0.783937] USB registered = USB subsystem initialized at 783ms
- [0.806202] ehci-pci = USB 2.0 controller driver loaded, bus number 1 assigned
- idVendor=1d6b = Linux Foundation vendor ID — virtual USB root hub (internal USB controller appearing as device)
- idProduct=0002 = USB 2.0 root hub product ID
- [1.284626] ohci-pci = USB 1.1 controller driver loaded, bus number 2
- Security use: forensic timeline — dmesg shows every device connected with exact timestamp — USB plugged at 3 AM shows here with vendor/product ID identifying exact device