# Day 59 — Labs: Startup Programs

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — Machine-Wide Run Key
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
```
- All-users startup entries — admin required to write
- Each value = one program launching at login for all users

---

## Lab 2 — Per-User Run Key
```cmd
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
```
- Current user startup entries — no admin required to write
- Most common malware persistence location
- Check this first in any startup investigation

---

## Lab 3 — RunOnce Keys
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce" 2>nul
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce" 2>nul
```
- Entries deleted after one execution
- `2>nul` — suppress error if key empty
- Active entries = something runs on next login then disappears

---

## Lab 4 — Startup Folders
```cmd
dir "C:\Users\%USERNAME%\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\"
dir "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp\"
```
- Per-user folder: no admin needed to add files
- All-users folder: admin required
- Any unexpected file here launches at login

---

## Lab 5 — List Scheduled Tasks
```cmd
schtasks /query /fo TABLE /nh
```
- Lists all scheduled tasks with next run time and status
- `/fo TABLE` — table format
- `/nh` — no header for cleaner output

---

## Lab 6 — Scheduled Task Details
```cmd
schtasks /query /fo LIST /v | findstr /I "task name\|run as user\|task to run\|status\|next run"
```
- Verbose details filtered to key fields
- "Run As User" = SYSTEM with unexpected executable = critical finding
- "Task To Run" = the actual executable path

---

## Lab 7 — Winlogon Values
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Userinit
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell
```
- Userinit must be exactly: `C:\Windows\system32\userinit.exe,`
- Shell must be exactly: `explorer.exe`
- Any additional value appended = malware persistence in login process

---

## Lab 8 — IFEO Accessibility Tool Check
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options" 2>nul | findstr /I "sethc\|utilman\|osk\|narrator\|magnify\|notepad\|calc"
```
- Checks for IFEO entries on commonly targeted programs
- Accessibility tools (sethc, utilman) targeted for pre-auth SYSTEM shell
- Any Debugger value under these keys = critical finding