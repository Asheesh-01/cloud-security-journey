# Day 60 — What the Windows Registry Is and Why It Exists

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 5 — Windows Registry
**Subtopic:** 1 — What the Registry Is

---

## What It Is

Hierarchical database storing all Windows configuration.
Replaced scattered INI files with one centralized, access-controlled database.
Introduced Windows NT 3.1 (1993).
Three roles: configuration system, forensic artifact store, attack surface.

## Key Terms

**Hive** — top-level Registry section. Stored as files on disk.
**Key** — container (like folder). Can hold subkeys and values.
**Value** — actual data. Has name, data type, and data content.

## Value Data Types

REG_SZ — plain string. Most common. File paths, names, config strings.
REG_EXPAND_SZ — string with environment variables (%SystemRoot%).
  Expands at runtime. Service binary paths often use this.
REG_DWORD — 32-bit integer. Enable/disable flags (0=off, 1=on).
REG_QWORD — 64-bit integer. Less common.
REG_BINARY — raw binary data. Security descriptors, hardware settings.
REG_MULTI_SZ — sequence of strings. Service dependency lists.

## Hive Files on Disk

HKLM\SYSTEM    → C:\Windows\System32\config\SYSTEM
HKLM\SOFTWARE  → C:\Windows\System32\config\SOFTWARE
HKLM\SAM       → C:\Windows\System32\config\SAM (local account hashes)
HKLM\SECURITY  → C:\Windows\System32\config\SECURITY (LSA secrets)
HKCU           → C:\Users\[username]\NTUSER.DAT

All locked while Windows runs.
Offline forensic access: boot live USB → copy files → analyze with
Registry Explorer or RegRipper.

## Security Relevance

**Configuration attack surface:**
DisableAntiSpyware = 1 in Defender policy key → AV disabled.
EnableLUA = 0 in System policy key → UAC disabled.
One Registry write disables security controls without touching executables.

**Forensic artifact store:**
MRU lists (recently opened files), USBSTOR (USB devices ever connected),
network connection history, application execution history, shellbags.

**Persistence database:**
Run keys, service definitions, Winlogon values, IFEO, AppInit DLLs,
COM hijacking, and dozens more persistence mechanisms.

**HKLM vs HKCU trust boundary:**
HKLM: admin required to write. System-wide trust decisions.
HKCU: user owns it. No elevation needed. Still sufficient for persistence.

**Registry virtualization:**
32-bit apps on 64-bit Windows redirected to WOW6432Node.
Malware written as 32-bit may end up in wrong location.
Security checks must inspect both locations.

**Remote Registry:**
Service allowing remote Registry read/write over network.
Should be disabled unless explicitly needed.
Enabled + valid credentials = remote Registry modification without login.

## Commands (Windows 10 VM)

```cmd
regedit
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion"
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /s
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /v ProductName
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /v CurrentVersion
dir C:\Windows\System32\config\
dir /A C:\Users\%USERNAME%\NTUSER.DAT
powershell "Get-Acl -Path 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run' | Format-List"
```