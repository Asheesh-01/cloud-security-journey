# Day 49 — Labs: Boot Process

**Environment:** Kali Linux VM

---

## Lab 1 — Check Boot Mode
```bash
[ -d /sys/firmware/efi ] && echo "UEFI mode" || echo "Legacy BIOS mode"
```
- `/sys/firmware/efi` exists only in UEFI mode
- `[ -d ]` — tests directory existence
- Determines whether Secure Boot is possible on this system

---

## Lab 2 — Secure Boot Status
```bash
mokutil --sb-state 2>/dev/null || echo "Not available"
```
- `mokutil` — Machine Owner Key utility for Secure Boot
- `--sb-state` — enabled or disabled
- VirtualBox may show disabled or not available

---

## Lab 3 — UEFI Boot Entries
```bash
efibootmgr 2>/dev/null || echo "Not in UEFI mode"
```
- Shows all boot entries registered in UEFI NVRAM
- Boot order, active entries, device paths
- Unexpected entries = possible bootkit investigation

---

## Lab 4 — GRUB Configuration
```bash
cat /boot/grub/grub.cfg | head -40
```
- Compiled GRUB configuration
- Kernel entries, initrd paths, kernel command-line parameters
- Check for unexpected entries or parameters

---

## Lab 5 — Actual Kernel Boot Parameters
```bash
cat /proc/cmdline
```
- Parameters actually passed to kernel by GRUB at boot
- Security-relevant: ro, quiet, splash, security module flags
- `init=/bin/bash` here = someone bypassed normal boot

---

## Lab 6 — PID 1 Verification
```bash
ps aux | grep "^root.*1 " | head -5
systemctl --version | head -2
```
- PID 1 must be systemd in normal operation
- Unexpected PID 1 = critical security finding
- `systemctl --version` confirms systemd is running

---

## Lab 7 — Boot Target and Active Targets
```bash
systemctl get-default
systemctl list-units --type=target --state=active
```
- `get-default` — graphical.target or multi-user.target
- Active targets show current boot state
- Unexpected active targets = investigate

---

## Lab 8 — Boot Files in /boot
```bash
ls -lh /boot/
ls -lh /boot/initrd.img-$(uname -r) 2>/dev/null || ls -lh /boot/initramfs* 2>/dev/null
```
- Lists all files in /boot — kernel images, initramfs, GRUB files
- Initramfs should match running kernel version (uname -r)
- Extra or unexpected files in /boot = investigate

---

## Lab 9 — Boot Messages in dmesg
```bash
dmesg | grep -i -E "BIOS|UEFI|EFI|grub|initrd|systemd|init" | head -20
```
- Kernel log entries from boot sequence
- Hardware detection, filesystem mount, init startup messages

---

## Lab 10 — TPM Presence
```bash
ls /dev/tpm* 2>/dev/null || echo "No TPM device found"
```
- `/dev/tpm0` present = TPM chip detected and driver loaded
- VirtualBox: typically no TPM unless explicitly configured
- Real hardware: TPM presence enables measured boot and disk encryption auto-unlock