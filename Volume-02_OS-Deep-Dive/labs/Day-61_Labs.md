# Day 61 — Labs: Registry Hives

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — Explore HKLM Top-Level Subkeys
```cmd
reg query HKLM
```
- Lists HARDWARE, SAM, SECURITY, SOFTWARE, SYSTEM
- SAM and SECURITY return Access Denied for non-SYSTEM accounts

---

## Lab 2 — Explore HKCU Subkeys
```cmd
reg query HKCU
```
- Current user's personal hive contents
- AppEvents, Console, Control Panel, SOFTWARE, System etc.

---

## Lab 3 — View All Loaded User Hives
```cmd
reg query HKU
```
- Lists SIDs for every loaded user profile
- .DEFAULT, S-1-5-18 (SYSTEM), S-1-5-19 (LocalService), named users
- Investigation: examine Run key under each named user SID

---

## Lab 4 — Startup Persistence for Default Profile
```cmd
reg query "HKU\.DEFAULT\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" 2>nul
```
- Checks startup entries in the default profile
- Entries here run before any named user logs in

---

## Lab 5 — File Association Check
```cmd
reg query "HKCR\.exe"
reg query "HKCR\.ps1"
reg query "HKCR\.bat"
```
- Shows handler for each file extension
- Attackers modify these to hijack file type execution

---

## Lab 6 — COM Hijacking Surface
```cmd
reg query "HKCU\SOFTWARE\Classes\CLSID" 2>nul
```
- User-level COM registrations — no admin needed to write here
- Any unexpected CLSID = possible COM hijacking persistence
- Takes precedence over HKLM equivalent registrations

---

## Lab 7 — HKCC Hardware Profile
```cmd
reg query HKCC
```
- Current hardware profile pointer
- Maps to HKLM\SYSTEM\...\Hardware Profiles\Current

---

## Lab 8 — Check All User Run Keys via HKU
```cmd
for /f "tokens=*" %s in ('reg query HKU ^| findstr "S-1-5-21"') do reg query "%s\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" 2>nul
```
- Iterates through all named user SIDs in HKU
- Checks the Run key for each user simultaneously
- Finds startup persistence across all user accounts in one command