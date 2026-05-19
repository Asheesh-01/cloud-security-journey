# Day 61 — Registry Hives

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 5 — Windows Registry
**Subtopic:** 2 — Registry Hives

---

## What It Is

Five top-level Registry sections with distinct purposes, permissions, and disk files.
Understanding each is foundational to Registry-based security work.

## The Five Hives

**HKLM — HKEY_LOCAL_MACHINE**
Machine-wide. Admin required to write most keys.
Subkeys:
  SYSTEM — services, drivers, boot config. Read at very early boot.
    Services: HKLM\SYSTEM\CurrentControlSet\Services
  SOFTWARE — app and OS config. Run keys. Policies. IFEO.
  SAM — local account hashes. SYSTEM access only. Locked while running.
  SECURITY — LSA secrets, cached domain credentials. SYSTEM only.
Disk files: config\SYSTEM, config\SOFTWARE, config\SAM, config\SECURITY.

**HKCU — HKEY_CURRENT_USER**
Current user only. No elevation required to write.
Pointer to HKU\[current user SID].
Key locations: Run key (persistence, no admin), RecentDocs MRU (forensic),
  OpenSavePidlMRU (file access forensic).
Disk file: C:\Users\[username]\NTUSER.DAT.
Malware writes here when user-level only. Still effective for persistence.

**HKCR — HKEY_CLASSES_ROOT**
File associations and COM object registrations.
Virtual — synthesized from HKLM\SOFTWARE\Classes + HKCU\SOFTWARE\Classes.
HKCU\SOFTWARE\Classes takes precedence over HKLM equivalent.
No disk file of its own.

COM hijacking:
Windows checks HKCU\SOFTWARE\Classes\CLSID before HKLM equivalent.
Attacker registers malicious DLL under existing CLSID in HKCU — no admin.
Privileged app loads that CLSID → loads attacker DLL.
Used by multiple APT groups. No elevated access required.

**HKU — HKEY_USERS**
All loaded user profile hives. Each subkey = one SID.
.DEFAULT = default profile (used before login, by services).
S-1-5-18 = SYSTEM. S-1-5-19 = LocalService. S-1-5-20 = NetworkService.
HKCU is a pointer into HKU for current user's SID.
Investigation: examine HKU\[SID]\...\Run for each user simultaneously.

**HKCC — HKEY_CURRENT_CONFIG**
Current hardware profile. Pointer to HKLM\SYSTEM\...\Hardware Profiles\Current.
Rarely security-relevant. Good to recognize.

## Security Summary by Hive

HKLM: highest-value target. Admin required. Services, security controls, SAM.
HKCU: user-level persistence. No admin. First place to check for user malware.
HKCR: COM hijacking attack surface. HKCU portion writable without admin.
HKU: all users at once. Examine all SIDs for persistence.

## Commands (Windows 10 VM)

```cmd
reg query HKLM
reg query HKCU
reg query HKU
reg query "HKU\.DEFAULT\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" 2>nul
reg query "HKCR\.exe"
reg query "HKCR\.ps1"
reg query "HKCU\SOFTWARE\Classes\CLSID" 2>nul
reg query HKCC
```