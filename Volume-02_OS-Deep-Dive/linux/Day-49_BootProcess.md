# Day 49 — Boot Process

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 1 — OS Architecture
**Subtopic:** 5 — Boot Process

---

## What It Is

Sequence from power-on to running OS. Each stage loads the next.
Foundation of all security — compromise here = control over everything above.

## Key Terms

**Firmware** — first code executing on power-on. Stored in ROM on motherboard.
**BIOS** — legacy firmware. 16-bit mode. 1MB limit. 2TB disk limit. MBR boot.
**UEFI** — modern firmware. 32/64-bit mode. GPT support. Secure Boot. NVRAM boot entries.
**POST** — Power-On Self-Test. Hardware check before any disk read.
**MBR** — 512 bytes at sector 0. Contains partition table + bootstrap. Bootkit target.
**GPT** — modern partition table. 128 partitions. Redundant copies. Used with UEFI.
**GRUB** — Linux bootloader. Loads kernel + initramfs. Config: /boot/grub/grub.cfg.
**vmlinuz** — compressed kernel image in /boot/.
**initramfs** — temporary RAM filesystem for early boot. Stored as /boot/initrd.img.
**systemd** — PID 1. Modern init system. Starts all services. Unit files in /etc/systemd/.
**TPM** — hardware chip recording measurements of boot process. Used by BitLocker/FDE.

## Boot Sequence (UEFI + Linux)

1. Power On → UEFI firmware executes from ROM
2. POST → hardware initialized and verified
3. UEFI Boot Manager → reads NVRAM boot entries, loads bootloader from ESP
   If Secure Boot: verify cryptographic signature before loading
4. GRUB → reads grub.cfg, presents menu, loads vmlinuz + initramfs
5. Kernel → decompresses, initializes subsystems, mounts initramfs
6. Root filesystem → real / partition mounted, initramfs discarded
7. systemd (PID 1) → starts all enabled services in dependency order
8. Login → display manager or getty presents login prompt

## Security Controls

**Secure Boot** — UEFI verifies bootloader/kernel signature before loading.
Prevents bootkits even with root filesystem access.
Requires trusted certificate to sign bootloader.

**TPM** — records hash of each boot stage. If anything changes → measurement changes.
BitLocker and Linux FDE can use TPM to auto-unlock only on trusted boot.

**GRUB password** — prevents boot parameter editing.
Without it: `init=/bin/bash` at GRUB gives root shell.

**UEFI administrator password** — prevents changing UEFI settings (boot order, Secure Boot).

**Boot order** — disable USB boot in UEFI.
Physical access + USB boot + no Secure Boot = full system access.

**Kernel command-line** — `/proc/cmdline` shows actual boot parameters.
`init=/bin/sh` or `single` = root shell bypass.

## Attacks

**Bootkit** — malware infecting MBR. Loads before OS. Invisible to OS security tools.
BIOS/legacy boot only. Secure Boot prevents on UEFI systems.

**Evil maid attack** — attacker with physical access boots from USB live OS.
Mounts installed system, modifies files as root, leaves no trace.
Defense: Secure Boot + UEFI password + disable USB boot + disk encryption.

**Kernel parameter injection** — modify GRUB entry to add malicious parameters.
GRUB password prevents unauthorized editing.

## Commands

```bash
[ -d /sys/firmware/efi ] && echo "UEFI mode" || echo "Legacy BIOS mode"
mokutil --sb-state 2>/dev/null              # Secure Boot status
efibootmgr 2>/dev/null                      # UEFI boot entries
cat /boot/grub/grub.cfg | head -40          # GRUB configuration
cat /proc/cmdline                            # actual kernel boot parameters
ps aux | grep "^root.*1 " | head -5         # PID 1 = should be systemd
systemctl get-default                        # default boot target
ls -lh /boot/                               # kernel and initramfs files
dmesg | grep -i -E "BIOS|UEFI|EFI|grub|initrd|systemd" | head -20
ls /dev/tpm* 2>/dev/null || echo "No TPM"  # TPM presence check
```