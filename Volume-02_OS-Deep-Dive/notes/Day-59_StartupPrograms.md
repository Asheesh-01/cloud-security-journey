# Day 59 — Startup Programs

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 4 — Windows Processes and Services
**Subtopic:** 3 — Startup Programs

---

## What It Is

Windows checks multiple locations at boot and login to launch programs.
Beyond services: registry Run keys, startup folders, scheduled tasks,
Winlogon, IFEO, AppInit DLLs, Boot Execute.
Each has different privilege requirements.
Attackers use whichever location matches their access level.

## Key Startup Locations

**Run Keys (most common malware target):**
HKLM\...\Run — all users, requires admin.
HKCU\...\Run — current user only, NO admin required.
HKLM\...\RunOnce — all users, deletes after execution.
HKCU\...\RunOnce — current user, deletes after execution.

**Startup Folders:**
Per-user: C:\Users\[user]\AppData\Roaming\Microsoft\Windows\
  Start Menu\Programs\Startup\ — no admin required.
All-users: C:\ProgramData\Microsoft\Windows\Start Menu\
  Programs\StartUp\ — admin required.

**Scheduled Tasks:**
XML files in C:\Windows\System32\Tasks\.
Can run as any user including SYSTEM.
Triggered by: login, startup, idle, time, event.
Often missed in basic startup audits.

**Winlogon:**
HKLM\...\Winlogon\Userinit — should be exactly:
  C:\Windows\system32\userinit.exe,
HKLM\...\Winlogon\Shell — should be: explorer.exe
Anything appended = runs at every login as part of login process.
One of stealthiest persistence locations.

**IFEO (Image File Execution Options):**
HKLM\...\Image File Execution Options\[exe]\Debugger
Runs attacker binary instead of target program.
Accessibility tools (sethc.exe, utilman.exe) targeted for login-screen access.
sethc.exe Debugger = cmd.exe → SYSTEM shell at login screen before auth.

**AppInit DLLs:**
HKLM\...\Windows\AppInit_DLLs
DLL injected into every process loading User32.dll (~every GUI process).
Disabled by default on Win8+ with Secure Boot enabled.
When enabled: global process injection mechanism.

**Boot Execute:**
HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\BootExecute
Runs before most of OS loads. Before most security tools initialize.
Normally: autocheck autochk *

## Autoruns — Essential Tool

Sysinternals Autoruns: checks ALL startup locations in one pass.
Shows: file, publisher, digital signature status, running status.
Unsigned entries or unexpected locations highlighted.
First tool run on suspected compromised machine.
Command-line: autorunsc.exe for scripted investigations.

## Commands (Windows 10 VM)

```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce" 2>nul
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce" 2>nul
dir "C:\Users\%USERNAME%\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\"
dir "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp\"
schtasks /query /fo TABLE /nh
schtasks /query /fo LIST /v | findstr /I "task name\|run as user\|task to run\|status"
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Userinit
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options" | findstr /I "sethc\|utilman\|notepad\|calc"
```