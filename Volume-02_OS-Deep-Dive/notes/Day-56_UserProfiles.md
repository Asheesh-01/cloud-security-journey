# Day 56 — User Profiles

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 3 — Windows Users and Groups
**Subtopic:** 3 — User Profiles

---

## What It Is

Complete set of user-specific data: files, folders, registry settings.
Stored at C:\Users\[username]\ on disk.
Loaded into memory as registry hive (NTUSER.DAT = HKCU) on login.
Richest source of forensic evidence on a Windows machine.

## Key Terms

**Profile folder** — C:\Users\[username]\. Created on first login.
Protected by NTFS ACLs — user, SYSTEM, Administrators only.

**NTUSER.DAT** — user's registry hive file. Lives in profile root.
Loaded as HKCU on login. Contains: Run keys, shellbags, MRU lists,
typed URLs, recently used documents.
Locked while logged in. Forensic tools parse offline copies.

**AppData** — hidden folder with three subfolders:
Local (machine-specific), LocalLow (low integrity), Roaming (synced).
No admin rights needed to write here — primary malware install location.

**Shellbags** — registry artifacts in NTUSER.DAT recording folder access.
Prove user accessed a directory even after directory is deleted.

**Recent folder** — LNK shortcut files for recently accessed files.
LNK retains: original path, timestamps, volume serial number.
Original file deleted → LNK remains → access proved forensically.

**Credential Manager** — saved credentials in profile.
C:\Users\[user]\AppData\Roaming\Microsoft\Credentials\
Targeted by Mimikatz dpapi module.

**Default profile** — C:\Users\Default\. Template for new user profiles.
Modifying Default profile = persistence for every new account created.

**Public profile** — C:\Users\Public\. Accessible by all local users.
Malware here affects every user regardless of which account is active.

**ProfileList registry** — HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList
Maps SIDs to profile folder paths. Shows all profiles ever created.
Ghost accounts: SID in ProfileList but no corresponding folder.

## Forensically Important Locations

C:\Users[user]\AppData\Local\Temp\              → malware staging
C:\Users[user]\AppData\Roaming\Microsoft
Windows\Recent\                                → recently accessed files (LNK)
Windows\PowerShell\PSReadLine
ConsoleHost_history.txt                      → PowerShell command history
Credentials\                                   → saved credentials
C:\Users[user]\AppData\Local\Microsoft
Windows\INetCookies\                           → browser session cookies
Credentials\                                   → additional credential store
C:\Users[user].ssh\                            → SSH private keys
C:\Users[user]\NTUSER.DAT                       → entire user registry hive

## Commands (Windows 10 VM)

```cmd
dir C:\Users\
dir /A C:\Users\%USERNAME%\
dir C:\Users\%USERNAME%\AppData\
dir /A /O-D C:\Users\%USERNAME%\AppData\Local\Temp\
type "C:\Users\%USERNAME%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt" 2>nul
dir "C:\Users\%USERNAME%\AppData\Roaming\Microsoft\Windows\Recent\"
dir /A C:\Users\%USERNAME%\NTUSER.DAT
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList"
```