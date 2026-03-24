# Day 16 — Labs

**Topic:** Windows OS Basics — Subtopic 3: Windows Folder Structure
**Environment:** Windows 10 VM (VirtualBox)

---

## Lab 1 — List Root of C: Drive Including Hidden Items

**Command:**
```cmd
dir C:\ /A
```

**What each part does:**
- `dir` — lists contents of a directory
- `C:\` — targets the root of the C: drive
- `/A` — shows ALL files including hidden, system, and protected files.
  Without /A, hidden items like ProgramData and $Recycle.Bin are invisible.

**Output received:**
```
03/21/2026  10:23 AM    <DIR>          $Recycle.Bin
03/21/2026  11:50 PM    <DIR>          Boot
05/05/2023  05:21 AM           416,102 bootmgr
12/07/2019  02:08 AM                 1 BOOTNXT
03/21/2026  11:50 PM             8,192 BOOTSECT.BAK
03/21/2026  10:52 PM    <JUNCTION>     Documents and Settings [C:\Users]
03/25/2026  12:41 AM             8,192 DumpStack.log.tmp
03/25/2026  12:41 AM     1,610,612,736 pagefile.sys
12/07/2019  02:14 AM    <DIR>          PerfLogs
03/23/2026  11:30 AM    <DIR>          Program Files
05/05/2023  05:27 AM    <DIR>          Program Files (x86)
03/21/2026  10:25 AM    <DIR>          ProgramData
03/21/2026  10:51 PM    <DIR>          Recovery
03/25/2026  12:41 AM       268,435,456 swapfile.sys
03/21/2026  10:23 AM    <DIR>          System Volume Information
03/21/2026  10:23 AM    <DIR>          Users
03/21/2026  10:23 AM             3,394 vboxpostinstall.log
03/21/2026  10:30 AM    <DIR>          Windows
               7 File(s)  1,879,484,073 bytes
              11 Dir(s)  16,259,379,200 bytes free
```

**What each line means:**

`$Recycle.Bin` — Recycle Bin storage. Deleted files go here, not truly deleted
until emptied. Investigators always check — attackers forget to empty it.

`Boot` — bootloader files. Modified here = potential bootkit attack.

`bootmgr` — 416,102 bytes. Windows Boot Manager. First thing that executes from
disk after BIOS/UEFI hands off. If replaced with malicious version = bootkit.

`BOOTNXT` — 1 byte placeholder. Nearly empty.

`Documents and Settings` — marked JUNCTION. Redirects to C:\Users for backward
compatibility. Not a real folder.

`DumpStack.log.tmp` — dated 03/25/2026 = today. Kernel created this during
a crash or error event today.

`pagefile.sys` — 1,610,612,736 bytes = 1.5 GB. Windows swap file. Contains
RAM contents including passwords, encryption keys, and session tokens.
Unencrypted = forensic evidence and security risk.

`ProgramData` — hidden. Only visible because /A was used. All-user app data.

`swapfile.sys` — 268,435,456 bytes = 256 MB. Second swap file for UWP apps.

`System Volume Information` — shadow copies and restore points live here.
Ransomware runs vssadmin delete shadows /all to destroy these before encrypting.

`vboxpostinstall.log` — 3,394 bytes. VirtualBox installation log. Reveals this
is a VM. Attacker reconnaissance reads this file to detect sandbox/VM.

**Screenshot:** saved in artifacts/Day-16/

---

## Lab 2 — List Windows Folder Contents

**Command:**
```cmd
dir C:\Windows /A
```

**What each part does:**
- `dir` — lists directory contents
- `C:\Windows` — targets the Windows OS folder
- `/A` — shows all items including hidden and system files

**Key output lines and what they mean:**

`explorer.exe` — 5,254,336 bytes. The Windows shell — taskbar, Start menu,
desktop icons. Always running. Malware injects into it because it is trusted.

`notepad.exe` — 201,216 bytes. Simple text editor. Attackers replace with a
trojanized version that also opens Notepad while running malware.

`regedit.exe` — 370,176 bytes. Registry Editor. Used to view and modify registry.

`lsasetup.log` — created 03/21/2026 = VM install date. LSA setup log.

`Prefetch` — folder. Every program run creates a prefetch file here with
execution timestamp. Forensic evidence that survives program deletion.

`SoftwareDistribution` — Windows Update downloads land here before installing.

`Tasks` — scheduled task files. Top persistence mechanism — malware creates
tasks here to run at login or on a timer.

`Temp` — last modified 03/24/2026. Windows temp. Any executable here = suspicious.

`Logs` — Windows component logs including Windows Update and CBS logs.

**Screenshot:** saved in artifacts/Day-16/

---

## Lab 3 — List User Profile Including Hidden Items

**Command:**
```cmd
dir C:\Users\%USERNAME% /A
```

**What each part does:**
- `dir` — lists directory contents
- `C:\Users\%USERNAME%` — %USERNAME% is an environment variable. Windows
  automatically replaces it with the current username. Resolved to: vboxuser
- `/A` — shows all files including hidden ones like AppData

**Key output and what it means:**

Username resolved to `vboxuser` — default VirtualBox account.

`AppData` — present as directory. Hidden by default — /A revealed it.
This is where malware persists.

`<JUNCTION>` entries — Application Data, Cookies, Local Settings, My Documents,
Recent, SendTo, Start Menu, Templates — all redirect old paths to modern
AppData equivalents. Not real folders.

`Recent [AppData\Roaming\Microsoft\Windows\Recent]` — logs every file
recently opened. Forensic evidence even after files are deleted.

`NTUSER.DAT` — 1,048,576 bytes = 1 MB. This is the user's personal Registry
hive stored as a file. Contains user settings and the Run key where user-level
malware persistence lives. Always analyze during investigation.

`ntuser.dat.LOG1` and `ntuser.dat.LOG2` — transaction logs for NTUSER.DAT.
Used for recovery if Windows crashes while writing to user registry.

`ntuser.ini` — 20 bytes. Tells Windows this folder is a user profile.

**Screenshot:** saved in artifacts/Day-16/

---

## Lab 4 — List AppData Contents

**Command:**
```cmd
dir C:\Users\%USERNAME%\AppData /A
```

**What each part does:**
- Same dir and /A as before
- `%USERNAME%\AppData` — targets the hidden AppData folder inside user profile

**Output:**
```
03/21/2026  10:45 AM    <DIR>          Local
03/21/2026  10:23 AM    <DIR>          LocalLow
03/21/2026  10:23 AM    <DIR>          Roaming
               0 File(s)              0 bytes
```

**What each subfolder means:**

`Local` — machine-specific app data. Browser cache, app settings, and the
%TEMP% folder lives here at AppData\Local\Temp. Modified 10:45 AM on install day.

`LocalLow` — low-integrity data for sandboxed browser plugins.

`Roaming` — the most important for security. App data that follows users
across domain machines. Writable without elevation. Hidden from casual view.
Primary location where malware installs for persistence without admin rights.

**Screenshot:** saved in artifacts/Day-16/

---

## Lab 5 — List Program Files

**Command:**
```cmd
dir "C:\Program Files" /A
```

**What each part does:**
- Quotes are required because the path contains a space
- /A shows all including hidden desktop.ini

**Key output and what it means:**

`Oracle` — VirtualBox is made by Oracle. Guest Additions installed here.
Confirms VM to any investigator who looks.

`Windows Defender` — modified 03/23/2026 08:46 AM. Definition update.
Malware tries to disable Defender before spreading. Check this folder's
recent modification time to verify Defender is actively updating.

`RUXIM` and `Microsoft Update Health Tools` — created 03/24/2026. Recent
Windows update activity.

Only 1 file (desktop.ini) — very clean VM with minimal software installed.
Real machine would have 20-50 application folders here.

**Screenshot:** saved in artifacts/Day-16/

---

## Lab 6 — Count Files in System32

**Command:**
```cmd
dir C:\Windows\System32 /A | find "File(s)"
```

**What each part does:**
- `dir C:\Windows\System32 /A` — lists all contents of System32
- `|` — pipe operator. Sends the output of dir as input to the next command
  instead of printing it to screen
- `find "File(s)"` — searches through the dir output for the line containing
  "File(s)" which is the summary line at the bottom

**Output:**
```
4181 File(s)  1,910,112,524 bytes
```

**What this means:**
4,181 files totaling 1.9 GB in System32. This is the entire core of Windows.
Security relevance: a new unexplained file appearing in System32 after a
potential compromise is a finding. Forensic investigators baseline the file
count and hash all 4,181 files before and after an incident.

**Screenshot:** saved in artifacts/Day-16/

---

## Lab 7 — Read Environment Variables

**Commands:**
```cmd
echo %WINDIR%
echo %TEMP%
echo %APPDATA%
echo %PROGRAMDATA%
echo %USERNAME%
```

**What each part does:**
- `echo` — prints text to the terminal screen
- `%VARIABLE%` — environment variable syntax in Windows. The % symbols wrap
  the variable name. Windows replaces the entire %NAME% with the stored value
  before executing the command.

**Output received:**
```
%WINDIR%    → C:\Windows
%TEMP%      → C:\Users\vboxuser\AppData\Local\Temp
%APPDATA%   → C:\Users\vboxuser\AppData\Roaming
%PROGRAMDATA% → C:\ProgramData
%USERNAME%  → vboxuser
```

**What each reveals:**

`%WINDIR%` = C:\Windows — the OS installation directory. Use this in scripts
instead of hardcoding C:\Windows — works regardless of install drive.

`%TEMP%` = C:\Users\vboxuser\AppData\Local\Temp — note this is NOT
C:\Windows\Temp. Every user has their own temp folder inside their profile.
Malware uses this because it is writable without elevation and unique per user.

`%APPDATA%` = C:\Users\vboxuser\AppData\Roaming — confirmed primary
persistence location. Malware hardcodes %APPDATA% in its code so it works
regardless of which username is compromised.

`%PROGRAMDATA%` = C:\ProgramData — all-user hidden app data.

`%USERNAME%` = vboxuser — current logged-in user.

**Screenshot:** saved in artifacts/Day-16/

---

## Lab Summary Table

| Lab | Command | Key Finding |
|-----|---------|-------------|
| 1 | dir C:\ /A | pagefile.sys = 1.5 GB of RAM contents on disk |
| 2 | dir C:\Windows /A | Prefetch folder = execution evidence, Tasks = persistence |
| 3 | dir C:\Users\%USERNAME% /A | NTUSER.DAT = user registry, Recent = file access log |
| 4 | dir AppData /A | Roaming = primary malware persistence, no elevation needed |
| 5 | dir "C:\Program Files" /A | Oracle folder confirms VM, Defender last updated 03/23 |
| 6 | dir System32 \| find | 4,181 files in System32 = baseline for anomaly detection |
| 7 | echo %VARIABLE% | %TEMP% resolves to AppData not C:\Windows\Temp |