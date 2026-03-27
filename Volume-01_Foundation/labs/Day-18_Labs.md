# Day 18 — Labs
# Topic 5: Windows OS Basics — Subtopic 5: Windows Registry

---

## Lab Environment
- Host: Ubuntu 24.04 — Acer Swift Go 14
- VM: Windows 10 inside VirtualBox
- Tool: Command Prompt (Administrator)

---

## Lab 1 — HKLM Run Keys (All Users Autostart)

### Command:
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
```

### Output:
```
SecurityHealth    REG_EXPAND_SZ    %windir%\system32\SecurityHealthSystray.exe
VBoxTray          REG_EXPAND_SZ    %SystemRoot%\system32\VBoxTray.exe
```

### What this means:
- **SecurityHealth** — Windows Security tray icon. %windir% = C:\Windows. Lives in System32. Legitimate Microsoft component.
- **VBoxTray** — VirtualBox Guest Additions tray app. In System32. Legitimate. Confirms this is a VM.
- **REG_EXPAND_SZ type** — string containing environment variables that Windows expands before executing.
- Only 2 entries — clean machine. Malware would add a third entry pointing to AppData, Temp, or Public.

---

## Lab 2 — HKCU Run Keys (Current User Autostart)

### Command:
```cmd
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
```

### Output:
```
OneDrive    REG_SZ    "C:\Users\vboxuser\AppData\Local\Microsoft\OneDrive\OneDrive.exe" /background
```

### What this means:
- **OneDrive** in AppData\Local — legitimate for a per-user application.
- **REG_SZ type** — plain string. No environment variables.
- **/background argument** — starts minimized, no splash screen.
- HKCU Run is writable by regular users — no Administrator needed. This is why malware prefers HKCU Run for persistence.

---

## Lab 3 — RunOnce Keys

### Commands:
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce"
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce"
```

### Output:
```
HKLM RunOnce: (empty)

HKCU RunOnce:
Delete Cached Update Binary         REG_SZ    C:\Windows\system32\cmd.exe /q /c del /q "...\OneDriveSetup.exe"
Delete Cached Standalone Update Binary  REG_SZ    C:\Windows\system32\cmd.exe /q /c del /q "...\OneDriveSetup.exe"
Uninstall 26.035.0222.0002          REG_SZ    C:\Windows\system32\cmd.exe /q /c rmdir /s /q "...\26.035.0222.0002"
Uninstall 26.040.0301.0001          REG_SZ    C:\Windows\system32\cmd.exe /q /c rmdir /s /q "...\26.040.0301.0001"
```

### What this means:
- HKLM RunOnce empty — no pending post-reboot tasks for all users.
- HKCU RunOnce has 4 OneDrive cleanup entries — delete old update files and old version folders after OneDrive updated.
- cmd.exe /q /c — quiet mode, run command and exit.
- del /q — delete without confirmation.
- rmdir /s /q — delete folder and all contents, no confirmation.
- RunOnce entries delete themselves after running once — gone after next login.

---

## Lab 4 — Windows Defender Registry Status

### Commands:
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows Defender" /v DisableAntiSpyware
reg query "HKLM\SOFTWARE\Microsoft\Windows Defender" /v DisableRealtimeMonitoring
```

### Output:
```
DisableAntiSpyware    REG_DWORD    0x0

ERROR: The system was unable to find the specified registry key or value.
```

### What this means:
- **DisableAntiSpyware = 0x0** — 0 = false = Defender anti-spyware is active.
- **DisableRealtimeMonitoring does not exist** — when value does not exist, Windows uses default which is 0 = monitoring ON. Absence of this value = secure state.
- Malware sets DisableAntiSpyware to 0x1 and creates DisableRealtimeMonitoring with value 0x1 to turn off protection.
- /v flag — query only a specific named value, not the entire key.

---

## Lab 5 — LSA Security Settings

### Command:
```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa"
```

### Key values from output:
```
NoLmHash                 REG_DWORD    0x1
LimitBlankPasswordUse    REG_DWORD    0x1
restrictanonymoussam     REG_DWORD    0x1
SecureBoot               REG_DWORD    0x1
Authentication Packages  REG_MULTI_SZ    msv1_0
LsaPid                   REG_DWORD    0x2d4
```

### What this means:
- **NoLmHash = 0x1** — LM hashes not stored. LM hashing disabled. Secure. If 0 — passwords crackable in seconds.
- **LimitBlankPasswordUse = 0x1** — blank password accounts cannot login over network. Secure.
- **restrictanonymoussam = 0x1** — anonymous users cannot list accounts. Secure.
- **SecureBoot = 0x1** — Secure Boot active. Prevents unauthorized bootloaders.
- **Authentication Packages = msv1_0** — REG_MULTI_SZ with only one entry. msv1_0 handles NTLM authentication. Only legitimate package loaded into lsass. Any additional unexpected entry = malicious DLL injected into lsass = rootkit.
- **LsaPid = 0x2d4** — PID of lsass.exe = 724 decimal. Windows records lsass PID here.

---

## Lab 6 — File Association for .exe

### Command:
```cmd
reg query "HKCR\.exe"
```

### Output:
```
(Default)    REG_SZ    exefile
Content Type    REG_SZ    application/x-msdownload
```

### What this means:
- **(Default) = exefile** — when Windows sees .exe, looks up exefile key to determine how to open it. exefile = execute directly as a program.
- **Content Type = application/x-msdownload** — MIME type for .exe files.
- If attackers modify (Default) to point elsewhere — every .exe launch runs malware first. Called file association hijacking.

---

## Lab 7 — Services Count

### Command:
```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Services" | find /c "HKEY"
```

### Output:
```
677
```

### What this means:
- 677 registered service entries — running services, stopped services, kernel drivers, all combined.
- Baseline established. Any increase = something was installed. Worth investigating what and when.
- | pipes dir output to find command.
- /c counts matching lines instead of showing them.
- "HKEY" matches every line that starts with HKEY — each line is one service key.

---

## Lab 8 — Simulating Malware Persistence

### Command — add fake malware to Run key:
```cmd
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v TestMalware /t REG_SZ /d "C:\Users\Public\fake_malware.exe" /f
```

### Output:
```
The operation completed successfully.
```

### Command breakdown:
- `reg add` — create or modify a registry value
- `/v TestMalware` — value name
- `/t REG_SZ` — plain string type
- `/d "C:\Users\Public\fake_malware.exe"` — the data — path executed at every login
- `/f` — force write, no confirmation needed

### Verification output:
```
OneDrive      REG_SZ    ...OneDrive.exe /background
TestMalware   REG_SZ    C:\Users\Public\fake_malware.exe
```

### What this proves:
No Administrator needed. No UAC prompt. No warning. One command as regular user — persistence installed. Would survive every reboot. This is exactly how real malware operates.

---

## Lab 9 — Removing Malware Persistence (SOC Remediation)

### Command:
```cmd
reg delete "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v TestMalware /f
```

### Output:
```
The operation completed successfully.
```

### Command breakdown:
- `reg delete` — remove a value
- `/v TestMalware` — target specific value name
- `/f` — force, no confirmation

### Verification — only OneDrive remains:
```
OneDrive    REG_SZ    ...OneDrive.exe /background
```

### What this means:
This is the exact command a SOC analyst runs during remediation — find malicious Run key entry, delete it, verify deletion, then hunt for the executable it was pointing to and remove that too.

---

## Key Findings From Today's Labs

1. HKCU Run writable by regular user — no elevation needed to install persistence
2. Machine has clean Run keys — SecurityHealth, VBoxTray (HKLM), OneDrive (HKCU) — all legitimate
3. NoLmHash = 1 — machine does not store weak LM hashes — secure configuration
4. Authentication Packages = msv1_0 only — no malicious DLLs injected into lsass
5. Defender active — DisableAntiSpyware = 0 — no tampering
6. 677 services registered — established as baseline for this machine
7. Proved persistence installation and removal — one command each direction

---

*Volume 01 — Topic 5 — Subtopic 5 | Day 18 | Exam Season*
*Commit: Day 18 - Windows Registry - Run keys, LSA settings, persistence simulation lab*