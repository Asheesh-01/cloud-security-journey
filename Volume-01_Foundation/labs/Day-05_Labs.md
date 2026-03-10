# Day 05 — Labs

**Topic:** What is a Computer
**Environment:** Kali Linux VM

---

## Lab 1 — Data vs Information

Commands:
echo "Data: 3 failed logins from 192.168.1.105"
echo "Information: IP 192.168.1.105 attempting brute force - investigate"
last | head -5

What echo does:
Prints text to screen. First command shows raw data.
Second shows same data converted to information with context.

What last | head -5 does:
Shows last 5 login records. Each line is raw data.
Your job as analyst — who logged in, when, from where,
is anything suspicious. That is converting data to information.

---

## Lab 2 — Hardware Detection

Commands:
lscpu
lspci | head -20
lsusb

What lscpu does:
Lists CPU hardware information. Shows CPU model, number of
cores, threads, clock speed, cache sizes.

What lspci does:
Lists all hardware devices connected via PCI bus.
Network cards, GPU, storage controllers appear here.
| head -20 limits to first 20 lines.

What lsusb does:
Lists all USB devices currently connected to system.
Unexpected USB device on a server = red flag.

---

## Lab 3 — CPU Monitoring

Commands:
lscpu
cat /proc/cpuinfo | head -30
top

What lscpu does:
Shows CPU summary. Model, cores, threads, clock speed.

What cat /proc/cpuinfo does:
Reads CPU information from Linux kernel virtual filesystem.
/proc is not a real directory — kernel creates it to expose
system information. Each block is one CPU core.

What top does:
Real time process monitor. Shows every running process and
how much CPU and RAM each one is consuming right now.
Updates every 3 seconds. Press q to exit.
%CPU column — how much CPU each process uses.
Process at 50% or higher on idle system is suspicious.

---

## Lab 4 — RAM Monitoring

Commands:
free -h
cat /proc/meminfo | head -20
ps aux --sort=-%mem | head -10

What free -h does:
Shows RAM usage summary. Total, used, free, available, swap.
-h means human readable format.
Swap — when RAM is full OS moves some contents to disk.
High swap usage means system is struggling.

What cat /proc/meminfo does:
Reads memory information directly from kernel.
MemTotal — total RAM.
MemFree — completely unused RAM.
MemAvailable — RAM actually available for new processes.

What ps aux --sort=-%mem | head -10 does:
Lists top 10 processes consuming most RAM.
--sort=-%mem sorts by memory usage, highest first.
%MEM column — percentage of total RAM this process uses.
RSS column — actual RAM in kilobytes.
Unknown process at top of list = red flag.

---

## Lab 5 — Disk Monitoring

Commands:
df -h
lsblk
du -sh /var/log 2>/dev/null
ls -lh /var/log/ | head -15

What df -h does:
Shows every mounted partition, total size, used, free,
usage percentage.
Use% above 80% = warning. Above 90% = critical.
Above 95% = logs may stop writing.

What lsblk does:
Lists block devices in tree format. Shows physical disks
and their partitions. sda = first disk. sda1 = first
partition on that disk.

What du -sh /var/log does:
Shows total disk space consumed by all log files.
-s means summary total only. -h means human readable.

What ls -lh /var/log does:
Lists log files with sizes. -l long format. -h human readable.
auth.log — every authentication event.
syslog — general system events.
kern.log — kernel events.

---

## Lab 6 — GPU Detection

Commands:
lspci | grep -i vga
lspci | grep -i nvidia
lspci | grep -i amd

Output:
00:02.0 VGA compatible controller: VMware SVGA II Adapter
No Nvidia detected.
No AMD detected.

What it means:
VirtualBox VM uses VMware SVGA II virtual GPU.
Maps to real Intel integrated graphics on host laptop.
On a server — unexpected GPU listed here = possible
unauthorized crypto mining hardware.

---

## Lab 7 — Motherboard and BIOS Information

Commands:
sudo dmidecode -t baseboard 2>/dev/null | head -20
sudo dmidecode -t memory 2>/dev/null | head -30
sudo dmidecode -t bios 2>/dev/null
dmesg | grep -i "secure boot" 2>/dev/null
dmesg | grep -i uefi

What dmidecode does:
Reads hardware information stored in DMI table.
DMI — Desktop Management Interface — standard for storing
hardware information.
-t baseboard — motherboard information.
-t memory — RAM slot information.
-t bios — BIOS or UEFI information.

What dmesg does:
Reads kernel ring buffer — log of messages from Linux kernel
during boot. grep filters for specific terms.

Output analysis:
BIOS Vendor — innotek GmbH — VirtualBox virtual BIOS.
Version — VirtualBox.
Release Date — 12/01/2006.
Secure Boot — empty output — not active in VM. Normal.
UEFI — empty output — VM running in legacy BIOS mode.
Real Ubuntu laptop runs in UEFI mode with Secure Boot.
```

---

