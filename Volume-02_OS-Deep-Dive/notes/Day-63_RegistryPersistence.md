# Day 63 — Common Registry Persistence Locations

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 5 — Windows Registry
**Subtopic:** 4 — Persistence Locations

---

## Complete Registry Persistence Map

### Run Keys (most common, all malware families)
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run     — all users, admin
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run     — current user, NO admin
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce — all users, admin, once
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce — current user, once
HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run — 32-bit on 64-bit

### Services
HKLM\SYSTEM\CurrentControlSet\Services\[ServiceName]
ImagePath = binary. Start = 2 (auto). ObjectName = account.
Create new subkey or modify existing ImagePath.

### Winlogon
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon
Userinit: must be C:\Windows\system32\userinit.exe, only.
Shell: must be explorer.exe only.
Any addition = runs at every login as part of login process.

### IFEO (Image File Execution Options)
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\[exe]
Debugger value = run this instead of target executable.
Accessibility tools targeted for pre-auth SYSTEM shell.
AV executables targeted for denial of security tools.

### AppInit DLLs
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows\AppInit_DLLs
LoadAppInit_DLLs must be 0 on hardened systems.
If 1 and DLL listed = DLL injected into all GUI processes.
Disabled by default on Win8+ with Secure Boot.

### Boot Execute
HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\BootExecute
Normal value: autocheck autochk *
Additions run before OS fully loads = before most security tools.

### LSA Packages (deepest, SYSTEM required)
HKLM\SYSTEM\CurrentControlSet\Control\Lsa\Authentication Packages
HKLM\SYSTEM\CurrentControlSet\Control\Lsa\Security Packages
DLLs loaded inside lsass.exe at boot.
Mimikatz mimilib.dll uses Security Packages.
Code execution inside authentication process.

### COM Hijacking
HKCU\SOFTWARE\Classes\CLSID\[GUID]\InprocServer32
No admin required. Takes precedence over HKLM CLSID registration.
Privileged app loads attacker DLL instead of legitimate COM object.

## Privilege Hierarchy

No admin: HKCU Run, RunOnce, COM hijacking.
Admin: HKLM Run, Services, Winlogon, IFEO, AppInit, Boot Execute.
SYSTEM: LSA Authentication/Security Packages.

## Detection Commands

```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" 2>nul
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" 2>nul
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce" 2>nul
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce" 2>nul
wmic service get name,pathname,startmode | findstr /V /I "system32\|syswow64\|program files\|windows"
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Userinit
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options" /s 2>nul | findstr "Debugger"
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows" /v AppInit_DLLs
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows" /v LoadAppInit_DLLs
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager" /v BootExecute
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v "Authentication Packages"
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v "Security Packages"
```