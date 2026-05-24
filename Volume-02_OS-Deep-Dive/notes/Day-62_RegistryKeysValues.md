# Day 62 — Registry Keys and Values

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 5 — Windows Registry
**Subtopic:** 3 — Keys and Values

---

## What It Is

Key = container (like folder). Holds subkeys and values.
Value = actual data. Has name, data type, and data content.
All Registry content = values inside keys inside hives.

## Key Terms

**Default value** — unnamed value in every key. Shown as (Default) in regedit.
**Value name** — identifier within parent key. Must be unique per key.
**Full path** — HIVE\Key\Subkey\...\Subkey. Unique within Registry.

## The Four Core Operations

**Query** — read key content or specific value.
**Add** — create key or write value (hardening, attack sim, malware itself).
**Delete** — remove key or specific value (cleanup, remediation).
**Export** — save key to .reg file. ALWAYS do this before modifying.

## Reading Output Format

HKEY_CURRENT_USER...\Run
ValueName    REG_SZ    C:\Path\To\Program.exe
Column 1: value name (the label — can be anything).
Column 2: data type.
Column 3: actual data (what really runs — this is what matters).
Name legitimate, path suspicious = finding.

## .reg File Format
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\SOFTWARE\Key]
"StringValue"="Data"
"DwordValue"=dword:00000001
"BinaryValue"=hex:01,02,03
Double backslash in .reg = single backslash in actual value.
[-HKCU\Key] with minus = delete this key on import.
"name"=- with minus = delete this value on import.

## REG_EXPAND_SZ Note

%SystemRoot%\system32\svchost.exe = C:\Windows\system32\svchost.exe
Both are the same binary. Know this when searching logs.
Forensic searches must account for both representations.

## Investigation Flow

1. reg export [key] [backup.reg] /y — backup first always
2. reg query [key] — read current state
3. reg delete [key] /v [value] /f — remove specific malicious value
4. reg query [key] — verify removal
5. del [backup.reg] — clean up backup after confirmed remediation

## Commands (Windows 10 VM)

```cmd
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /v ProductName
reg add "HKCU\SOFTWARE\TestKey" /v TestValue /t REG_SZ /d "TestData" /f
reg add "HKCU\SOFTWARE\TestKey" /v TestDword /t REG_DWORD /d 1 /f
reg query "HKCU\SOFTWARE\TestKey"
reg export "HKCU\SOFTWARE\TestKey" C:\Users\Public\testkey_backup.reg /y
type C:\Users\Public\testkey_backup.reg
reg delete "HKCU\SOFTWARE\TestKey" /v TestValue /f
reg delete "HKCU\SOFTWARE\TestKey" /f
del C:\Users\Public\testkey_backup.reg
```