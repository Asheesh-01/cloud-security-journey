# Day 56 — Labs: User Profiles

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — List All Profile Folders
```cmd
dir C:\Users\
```
- Every folder = one user profile on this machine
- Default, Public, and named user accounts
- Unexpected folders = accounts that logged in previously

---

## Lab 2 — View Profile Structure Including Hidden
```cmd
dir /A C:\Users\%USERNAME%\
```
- `/A` — show all including hidden folders
- AppData is hidden by default — revealed with /A
- Shows all top-level profile subfolders

---

## Lab 3 — AppData Subfolders
```cmd
dir C:\Users\%USERNAME%\AppData\
```
- Three subfolders: Local, LocalLow, Roaming
- No admin rights needed to write anywhere here
- Primary malware installation target

---

## Lab 4 — Temp Folder Audit
```cmd
dir /A /O-D C:\Users\%USERNAME%\AppData\Local\Temp\
```
- `/O-D` — sort newest files first
- Executables here = immediate investigation
- Malware downloads and stages here before execution

---

## Lab 5 — PowerShell Command History
```cmd
type "C:\Users\%USERNAME%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt" 2>nul
```
- Every interactive PowerShell command ever run by this user
- `2>nul` — suppress error if file missing (never ran PowerShell)
- Attackers using PowerShell leave trail here

---

## Lab 6 — Recent Files
```cmd
dir "C:\Users\%USERNAME%\AppData\Roaming\Microsoft\Windows\Recent\"
```
- LNK shortcut files for recently accessed items
- Each LNK contains metadata about the original file
- Survives original file deletion — forensic evidence remains

---

## Lab 7 — NTUSER.DAT Location and Size
```cmd
dir /A C:\Users\%USERNAME%\NTUSER.DAT
```
- User registry hive file
- Locked while user is logged in
- Size indicates extent of stored user history

---

## Lab 8 — ProfileList Registry Key
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList"
```
- Maps every SID to its profile folder path
- Shows all profiles ever created on this machine
- SID present but no folder = ghost account = investigate