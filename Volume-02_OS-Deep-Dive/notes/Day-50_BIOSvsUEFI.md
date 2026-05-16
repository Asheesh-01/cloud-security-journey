# Day 50 — BIOS vs UEFI Differences

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 1 — OS Architecture
**Subtopic:** 6 — BIOS vs UEFI

---

## What It Is

Both are firmware — code that runs before any OS.
BIOS: legacy 40-year standard. UEFI: modern replacement.
Security controls available depend entirely on which is present.

## Key Terms

**Real mode** — 16-bit CPU mode BIOS runs in. 1MB RAM limit. No memory protection.
**Protected/long mode** — 32/64-bit CPU mode UEFI runs in. Modern hardware support.
**ESP (EFI System Partition)** — FAT32 partition storing bootloader .efi files.
  Mounted at /boot/efi on Linux.
**NVRAM** — non-volatile storage for UEFI boot entries. Survives power loss.
**EFI shell** — pre-OS command environment in UEFI. Access without password = risk.
**Secure Boot databases**:
  db = allowed signing certificates.
  dbx = blacklisted (revoked) signatures.
  KEK = keys that can update db/dbx.
  PK = Platform Key, root of trust (set by hardware manufacturer).
**MOK** — Machine Owner Key. Linux distros use this to sign their bootloaders.
**UEFI drivers** — pre-OS drivers loaded by UEFI. Can be malicious and persistent.

## BIOS vs UEFI Comparison

| Feature | BIOS | UEFI |
|---|---|---|
| CPU mode | 16-bit real | 32/64-bit protected |
| Max RAM | 1MB | Terabytes |
| Max disk | 2TB MBR | 9.4 ZB GPT |
| Partitions | 4 primary (MBR) | 128 primary (GPT) |
| Boot storage | MBR sector 0 | ESP .efi files |
| Boot entries | Fixed hardware order | NVRAM persistent entries |
| Secure Boot | No | Yes |
| Pre-OS environment | Minimal text | EFI shell + graphical |

## Security Differences

**Bootkit vulnerability (BIOS)**: MBR replacement runs before OS, invisible to tools.
**Secure Boot (UEFI)**: cryptographic signature required. Invalid = load refused.
**UEFI firmware rootkits**: write to firmware chip. Survive OS reinstall + disk replacement.
  LoJax (2018) — first in-the-wild UEFI rootkit. Only remediation: reflash or replace motherboard.
**UEFI variable attacks**: OS-level code can write NVRAM variables.
  Malicious variables persist across reboots. Linux restricts efivars access.
**Secure Boot bypasses**:
  BootHole CVE-2020-10713 — GRUB buffer overflow via legitimately signed bootloader.
  BlackLotus 2023 — Secure Boot bypass on Windows 11 via signed vulnerable bootloader.
  dbx blacklist exists to revoke signatures of vulnerable signed binaries.

## Hardening

UEFI administrator password — prevents changing firmware settings.
Disable USB boot — prevents live OS boot attacks.
Enable Secure Boot — enforces bootloader/kernel signature verification.
Keep UEFI firmware updated — patches pre-OS vulnerabilities.
Set kptr_restrict — limits kernel address leakage from UEFI variable interfaces.

## Commands

```bash
ls /sys/firmware/efi/efivars/ | head -20      # UEFI variables (UEFI mode only)
ls -lh /boot/efi/EFI/ 2>/dev/null             # ESP contents
sudo efibootmgr -v 2>/dev/null                # UEFI boot entries verbose
sudo mokutil --list-enrolled 2>/dev/null       # enrolled Secure Boot keys
sudo dmidecode -t bios 2>/dev/null | grep -E "Vendor|Version|Characteristic" | head -10
ls /sys/firmware/efi/efivars/ | grep -i -E "SecureBoot|PK|KEK|db" | head -10
sudo fwupdmgr get-devices 2>/dev/null | head -20  # UEFI firmware update status
```