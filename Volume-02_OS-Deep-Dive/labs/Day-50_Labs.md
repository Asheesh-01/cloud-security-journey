# Day 50 — Labs: BIOS vs UEFI

**Environment:** Kali Linux VM

---

## Lab 1 — UEFI Variables
```bash
ls /sys/firmware/efi/efivars/ | head -20
```
- Only exists in UEFI mode — directory absent in BIOS mode
- Each file is one UEFI variable (name-{GUID})
- Contains boot entries, Secure Boot keys, firmware settings

---

## Lab 2 — EFI System Partition
```bash
ls -lh /boot/efi/ 2>/dev/null || echo "No EFI partition mounted"
ls -lh /boot/efi/EFI/ 2>/dev/null
```
- `/boot/efi` — ESP mount point
- `/boot/efi/EFI/` — bootloader directories per installed OS
- .efi files are signed bootloader binaries verified by Secure Boot

---

## Lab 3 — GRUB EFI Bootloader
```bash
ls -lh /boot/efi/EFI/kali/ 2>/dev/null || ls -lh /boot/efi/EFI/ 2>/dev/null
```
- The .efi file here is what UEFI loads after signature verification
- Replacing with unsigned binary = Secure Boot blocks boot

---

## Lab 4 — UEFI Boot Entries
```bash
sudo efibootmgr -v 2>/dev/null || echo "Not in UEFI mode"
```
- `-v` — verbose, shows device path to each .efi file
- BootOrder: sequence UEFI tries each entry
- Unexpected entries = possible bootkit investigation

---

## Lab 5 — Enrolled Secure Boot Keys
```bash
sudo mokutil --list-enrolled 2>/dev/null | head -20 || echo "Not available"
```
- Shows certificates enrolled in MOK database
- Linux distribution signing key should appear
- Unexpected certificates = investigate

---

## Lab 6 — DMI/BIOS Information
```bash
sudo dmidecode -t bios 2>/dev/null | grep -E "Vendor|Version|Release|Characteristic" | head -10
```
- `dmidecode` — reads hardware info from DMI table
- `-t bios` — firmware information only
- Shows firmware vendor, version, Secure Boot support status

---

## Lab 7 — Secure Boot UEFI Variables
```bash
ls /sys/firmware/efi/efivars/ 2>/dev/null | grep -i -E "SecureBoot|PK|KEK|db" | head -10
```
- Filters for Secure Boot-specific UEFI variables
- SecureBoot variable = current state (1=enabled)
- PK, KEK, db, dbx = key hierarchy files

---

## Lab 8 — UEFI Firmware Update Status
```bash
sudo fwupdmgr get-devices 2>/dev/null | head -20 || echo "fwupd not available"
```
- Lists devices supporting firmware updates via LVFS
- Outdated UEFI firmware has known vulnerabilities
- Firmware patching = patching pre-OS code
- As important as OS patching but frequently neglected