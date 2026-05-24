# Day 62 — Labs: Registry Keys and Values

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — Query Run Key
```cmd
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
```
- Reads all startup values for current user
- Output: value name, type, executable path

---

## Lab 2 — Query Specific Named Value
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /v ProductName
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /v RegisteredOwner
```
- `/v` retrieves only the named value
- Baseline OS identification

---

## Lab 3 — Add REG_SZ Value
```cmd
reg add "HKCU\SOFTWARE\TestKey" /v TestValue /t REG_SZ /d "TestData" /f
```
- Creates TestKey if missing, adds TestValue with string data
- `/f` — force, no confirmation needed

---

## Lab 4 — Add REG_DWORD Value
```cmd
reg add "HKCU\SOFTWARE\TestKey" /v TestDword /t REG_DWORD /d 1 /f
```
- Adds numeric enable/disable flag value
- Value 1 = enabled, 0 = disabled pattern

---

## Lab 5 — Verify Written Values
```cmd
reg query "HKCU\SOFTWARE\TestKey"
```
- Confirms both values exist with correct types and data

---

## Lab 6 — Export Key to .reg File
```cmd
reg export "HKCU\SOFTWARE\TestKey" C:\Users\Public\testkey_backup.reg /y
type C:\Users\Public\testkey_backup.reg
```
- Exports key with all values to .reg file
- `type` shows the .reg file format including double-backslash notation

---

## Lab 7 — Delete Specific Value
```cmd
reg delete "HKCU\SOFTWARE\TestKey" /v TestValue /f
reg query "HKCU\SOFTWARE\TestKey"
```
- `/v` removes only the named value — key and other values remain
- Query confirms TestValue gone, TestDword still present

---

## Lab 8 — Delete Entire Key and Clean Up
```cmd
reg delete "HKCU\SOFTWARE\TestKey" /f
del C:\Users\Public\testkey_backup.reg
```
- Removes TestKey and all remaining contents recursively
- Deletes backup file — always clean up after labs