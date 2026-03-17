# Day 11 — Topic 3: What is an Operating System (Subtopic 7 - Device Drivers)

**Date:** Day 11 of Cloud Security Journey
**Volume:** 01 — Computer and Internet Foundations
**GitHub:** github.com/Asheesh-01

---

## Subtopic 7 — Device Drivers

### What it is

A device driver is a piece of software that acts as a translator between the operating system kernel and a specific hardware device. The kernel speaks one general language. Every hardware device speaks its own specific language. The driver sits in the middle and translates both ways.

### Why Device Drivers Exist

The kernel needs to support hundreds of different keyboards, WiFi chips, GPUs, USB devices. If the kernel knew how to talk to every single one directly — the kernel would be millions of lines of hardware-specific code. Unmaintainable.

Solution — the kernel defines a standard interface. Hardware manufacturers write drivers that follow this interface. The kernel calls the driver. The driver talks to the hardware. The kernel never needs to know the hardware details.

### Where Drivers Live

Device drivers run in kernel space — Ring 0 — full hardware access — same privilege level as the kernel itself. This is why a badly written driver can crash your entire system.

### Three Types of Device Drivers

**Character device drivers** — handle devices that send or receive data one character (one byte) at a time. Keyboards, mice, serial ports, terminals. Appear as files in /dev — /dev/tty1, /dev/input/mice.

**Block device drivers** — handle devices that store data in fixed size blocks. Hard drives, SSDs, USB storage. Appear as /dev/sda, /dev/sdb. Data read and written in 4096 byte blocks.

**Network device drivers** — handle network interfaces. WiFi chips, Ethernet cards. Do not appear as files in /dev — appear as network interfaces eth0, wlan0, lo.

### Real Practical Example — You Plug in a USB Keyboard

Step 1 — you plug USB keyboard into port. USB controller chip detects new device connected.

Step 2 — USB controller sends an interrupt signal to the CPU. Interrupt means stop current work, something needs attention. CPU pauses and calls kernel interrupt handler.

Step 3 — kernel USB subsystem asks keyboard: who are you? Keyboard responds with vendor ID and product ID — vendor 046d (Logitech) product c31c (specific model).

Step 4 — kernel searches driver registry. Finds matching driver — HID (Human Interface Device) keyboard driver. Loads it into kernel space memory.

Step 5 — driver initializes keyboard. Sets up communication protocol. Tells keyboard: send me a signal every time a key is pressed.

Step 6 — you press letter 'a'. Keyboard hardware detects keypress. Sends USB packet to USB controller.

Step 7 — USB controller sends interrupt to CPU.

Step 8 — CPU calls keyboard driver interrupt handler. Driver reads USB packet. Translates: key code 0x04 pressed = letter 'a' on US keyboard layout.

Step 9 — driver passes translated keypress to kernel input subsystem.

Step 10 — kernel passes it to terminal application. Character 'a' appears on screen.

Total time: 1 to 8 milliseconds. All 10 steps in that time.

Without the driver — step 4 fails. Keyboard detected but completely non-functional.

### Kernel Modules

A kernel module (LKM — Loadable Kernel Module) is a driver that can be loaded and unloaded without rebooting. Like a plugin for the kernel. lsmod shows all loaded modules. Each module runs in Ring 0 with full kernel privileges.

### Key Terms

- **Device driver** — translator between kernel and specific hardware device
- **Kernel module / LKM** — Loadable Kernel Module, driver loaded as plugin without reboot
- **Ring 0** — highest CPU privilege level, full hardware access, where drivers run
- **Interrupt** — signal from hardware to CPU meaning stop and handle this event
- **HID** — Human Interface Device, standard protocol for keyboards and mice
- **Vendor ID / Product ID** — unique numbers identifying hardware manufacturer and model
- **Character device** — device handling data byte by byte, appears in /dev
- **Block device** — device handling data in fixed blocks, /dev/sda etc
- **Network device** — network interface, appears as eth0, wlan0 etc
- **lsmod** — command listing all currently loaded kernel modules
- **dmesg** — kernel message buffer, log of all kernel events since boot
- **uname -r** — shows current kernel version
- **ABI** — Application Binary Interface, compatibility contract between kernel and modules
- **Rootkit driver** — malicious kernel module replacing legitimate driver
- **Keylogger** — malware intercepting keypresses at driver level before encryption
- **e1000** — Intel gigabit Ethernet network driver
- **vboxsf** — VirtualBox Shared Folders driver, third party module
- **ehci** — Enhanced Host Controller Interface, USB 2.0 controller driver
- **hid_generic** — generic HID driver handling standard keyboards and mice

### Security Angles

**Kernel level access = highest risk.** Drivers run in Ring 0. A vulnerability in a driver is a vulnerability in the kernel. Attackers write malicious drivers called rootkit drivers that hide processes, hide files, intercept passwords.

**Keylogger** — malicious keyboard driver intercepts keypresses at Step 8. Before passing to application, silently copies to file or sends over network. Operates at kernel level — below any application security.

**Stuxnet 2010** — used malicious printer driver to load into kernel space on Windows. Exploited driver loading mechanism to gain Ring 0 access. Damaged physical centrifuges in Iranian nuclear facility.

**Forensic use of dmesg** — during incident response, dmesg reveals exactly what devices were connected and when. USB drive plugged at 3 AM shows here with vendor ID and product ID identifying exact device model.

**lsmod for rootkit detection** — rootkits load as kernel modules. Forensic investigators compare lsmod output against known good module list. Unexpected module = investigate immediately. Advanced rootkits hide from lsmod — only detectable by comparing /proc/modules with raw kernel memory.