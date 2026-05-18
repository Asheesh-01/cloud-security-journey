# Day 60 — Labs: Windows Registry Introduction

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — Open Registry Editor
```cmd
regedit
```
- Graphical Registry tree browser
- Left pane: key navigation tree
- Right pane: values in selected key
- File > Export: backup key to .reg file before modifying

---

## Lab 2 — Query a Registry Key
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion"
```
- `reg query` — reads and displays key values
- Shows all values directly under this key
- Subkeys listed separately — use /s for recursive

---

## Lab 3 — Recursive Registry Query
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /s
```
- `/s` — recursive, shows all subkeys and their values
- Shows complete content of a Registry branch

---

## Lab 4 — Query Specific Named Value
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /v ProductName
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /v CurrentVersion
```
- `/v ValueName` — retrieve only the named value
- Baseline OS identification from Registry

---

## Lab 5 — View Hive Files on Disk
```cmd
dir C:\Windows\System32\config\
```
- Lists SYSTEM, SOFTWARE, SAM, SECURITY, DEFAULT hive files
- All locked while Windows runs
- SAM contains local account password hashes

---

## Lab 6 — View NTUSER.DAT
```cmd
dir /A C:\Users\%USERNAME%\NTUSER.DAT
```
- Current user's HKCU hive file
- `/A` — show hidden attribute files
- Locked while user is logged in

---

## Lab 7 — Registry Key Permissions
```cmd
powershell "Get-Acl -Path 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run' | Format-List"
```
- `Get-Acl` on Registry path — shows key ACL
- `HKLM:\` — PowerShell Registry drive notation
- Shows who can read, write, and set permissions on this key

---

## Lab 8 — Check WOW6432Node (32-bit Virtualization)
```cmd
reg query "HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run" 2>nul
```
- 32-bit application startup entries on 64-bit Windows
- Security checks must inspect both Run locations
- Malware written as 32-bit may persist here instead of standard Run key