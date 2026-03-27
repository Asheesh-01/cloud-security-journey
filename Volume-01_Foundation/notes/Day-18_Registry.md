# Day 18 — Topic 5: Windows OS Basics
# Subtopic 5 — Windows Registry

---

## What is the Windows Registry

The Windows Registry is a centralized database built into Windows that stores all configuration information for the entire operating system and every installed application. Everything Windows needs to know to run — hardware settings, user preferences, installed software, security policies, startup programs, service configurations — all of it lives in the Registry.

The Registry is read thousands of times per second while Windows is running. Every time you open an application, change a setting, plug in a device, or log in — the Registry is consulted.

For security: the Registry is the most common place attackers hide malware so it survives reboots. Specific Registry keys tell Windows what to run at startup. Write malware path into those keys and it runs every single time — automatically, silently, before the user sees anything.

---

## Key Terms

| Term | Meaning |
|---|---|
| Registry | Hierarchical database built into Windows. Stores configuration data in a tree structure of keys and values. |
| Hive | Top-level section of the Registry. Like a drive letter. 5 main hives. |
| Key | Container inside the Registry. Like a folder. Can contain subkeys and values. |
| Subkey | A key inside another key. Like a subfolder. |
| Value | Actual data stored in a key. Like a file inside a folder. Has a name, type, and data. |
| REG_SZ | Plain text string. Most common type. Used for file paths. |
| REG_DWORD | 32-bit number. Used for on/off settings — 0 = off, 1 = on. |
| REG_BINARY | Raw binary data. Used for complex settings. |
| REG_EXPAND_SZ | String with expandable environment variables. Example: %SystemRoot%\system32\file.exe |
| REG_MULTI_SZ | Multiple strings in one value. Used for lists. |
| Run key | Registry key telling Windows which programs to start at login. Most commonly abused persistence location. |
| Hive file | Physical file on disk where Registry data is stored. SAM, SYSTEM, SOFTWARE, SECURITY in C:\Windows\System32\config |
| regedit | Registry Editor GUI. regedit.exe in System32. |
| reg | Command-line tool for querying and modifying the Registry. |

---

## The 5 Hives

| Hive | Short Name | What It Stores |
|---|---|---|
| HKEY_LOCAL_MACHINE | HKLM | Everything about the machine — hardware, installed software, services, security policy. Applies to all users. |
| HKEY_CURRENT_USER | HKCU | Everything about the currently logged-in user — desktop settings, user-specific software config. |
| HKEY_CLASSES_ROOT | HKCR | File type associations and COM registrations. What program opens which file type. |
| HKEY_USERS | HKU | All user hives loaded on this machine. HKCU is a pointer into HKU for the current user. |
| HKEY_CURRENT_CONFIG | HKCC | Current hardware profile. Rarely targeted. |

---

## Most Important Registry Locations for Security

### Persistence — Run Keys
```
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
```

- **Run** — programs listed here start every time user logs in
- **RunOnce** — programs run once then entry is deleted automatically
- **HKLM Run** — requires Administrator to write
- **HKCU Run** — requires only user-level access — malware uses this without elevation

### Services
```
HKLM\SYSTEM\CurrentControlSet\Services
```
Every service registered here with binary path, start type, account.

### LSA Security Settings
```
HKLM\SYSTEM\CurrentControlSet\Control\Lsa
```
Authentication settings, credential policies, security packages. Attackers modify here to weaken authentication or inject malicious DLLs into lsass.

### File Associations
```
HKCR\.exe
HKCR\exefile\shell\open\command
```
Controls what happens when any .exe is double-clicked. Attackers hijack this to run malware before every executable launch.

### Windows Defender
```
HKLM\SOFTWARE\Microsoft\Windows Defender
```
DisableAntiSpyware, DisableRealtimeMonitoring — malware sets these to 1 to disable protection.

---

## What Your Machine Showed — Clean Baseline

### HKLM Run (all users):
```
SecurityHealth    REG_EXPAND_SZ    %windir%\system32\SecurityHealthSystray.exe
VBoxTray          REG_EXPAND_SZ    %SystemRoot%\system32\VBoxTray.exe
```
SecurityHealth = Windows Security tray icon. VBoxTray = VirtualBox Guest Additions. Both in System32. Both legitimate.

### HKCU Run (current user):
```
OneDrive    REG_SZ    "C:\Users\vboxuser\AppData\Local\Microsoft\OneDrive\OneDrive.exe" /background
```
OneDrive in AppData — legitimate because it is a per-user application. Always verify digital signature when Run key points to AppData.

### HKCU RunOnce:
Four OneDrive cleanup entries — delete old update files and old version folders. Legitimate. RunOnce entries delete themselves after running once.

### Defender Status:
```
DisableAntiSpyware = 0x0   → Defender active
DisableRealtimeMonitoring  → value does not exist → real-time monitoring ON (default)
```
Absence of DisableRealtimeMonitoring = secure state. Malware creates it and sets to 1 to disable scanning.

### LSA Key — Critical Values:
```
NoLmHash = 0x1              → LM hashes NOT stored — LM hashing disabled — secure
LimitBlankPasswordUse = 0x1 → blank password accounts cannot login over network — secure
restrictanonymoussam = 0x1  → anonymous users cannot list accounts — secure
SecureBoot = 0x1            → Secure Boot active
Authentication Packages = msv1_0  → only legitimate auth package loaded into lsass
```

**NoLmHash = 1 is critical.** If 0 — machine stores LM hashes — passwords crackable in seconds.

**Authentication Packages = msv1_0 only.** Any additional entry here means malicious DLL injected into lsass. This is rootkit territory.

### .exe file association:
```
(Default) = exefile
Content Type = application/x-msdownload
```
Normal. exefile tells Windows to execute .exe files directly. If attackers modify this, every .exe launch runs malware first.

### Services count:
```
677 registered entries
```
Baseline for this machine. Any increase = something was installed. Worth investigating what and when.

---

## Simulated Malware Persistence — What We Proved

### Adding persistence (one command, no elevation needed):
```cmd
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v TestMalware /t REG_SZ /d "C:\Users\Public\fake_malware.exe" /f
```
- `/v TestMalware` — value name
- `/t REG_SZ` — plain string type
- `/d "C:\Users\Public\fake_malware.exe"` — the data — path executed at every login
- `/f` — force write, no confirmation

**Result:** One command, regular user, no UAC, no warning — persistence installed. Would survive every reboot until found and removed.

### Verifying it was written:
```
OneDrive      REG_SZ    ...OneDrive.exe /background
TestMalware   REG_SZ    C:\Users\Public\fake_malware.exe
```
TestMalware entry visible alongside OneDrive. If fake_malware.exe existed, it would run at every login.

### Removing it (SOC remediation step):
```cmd
reg delete "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v TestMalware /f
```
- `reg delete` — remove value
- `/v TestMalware` — target specific value name
- `/f` — force, no confirmation

After deletion — only OneDrive remains. Clean.

---

## Real Attack — NotPetya 2017

NotPetya caused over $10 billion in damage. After spreading through networks using stolen credentials from lsass memory, it modified the Registry boot configuration key to run a custom bootloader that encrypted the Master Boot Record before Windows started. Victims could not boot at all. The Registry modification happened at boot-time — before Windows loaded — making recovery without backups impossible.

---

## Four Registry Locations — Check These First in Every Investigation

1. `HKLM\...\Run` — all users autostart
2. `HKCU\...\Run` — current user autostart
3. `HKLM\SYSTEM\CurrentControlSet\Services` — every registered service
4. `HKLM\SYSTEM\CurrentControlSet\Control\Lsa` — authentication and credential settings

---

*Volume 01 — Topic 5 — Subtopic 5 | Day 18 | Exam Season*