# Day 63 — Labs: Registry Persistence Locations

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — All Run Key Variants
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" 2>nul
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" 2>nul
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce" 2>nul
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce" 2>nul
reg query "HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run" 2>nul
```
- Six Run key variants — all checked in sequence
- Any unexpected value in any = startup persistence finding

---

## Lab 2 — Service Binary Path Audit
```cmd
wmic service get name,pathname,startmode | findstr /V /I "system32\|syswow64\|program files\|windows\|microsoft"
```
- Removes expected locations leaving only unexpected ones
- Each result requires investigation

---

## Lab 3 — Winlogon Values
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Userinit
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell
```
- Userinit: exactly `C:\Windows\system32\userinit.exe,` only
- Shell: exactly `explorer.exe` only
- Any addition = login-process persistence

---

## Lab 4 — IFEO Debugger Check
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options" /s 2>nul | findstr "Debugger"
```
- Recursively searches all IFEO subkeys for Debugger values
- Any result = program being hijacked at execution

---

## Lab 5 — AppInit DLL Check
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows" /v AppInit_DLLs
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows" /v LoadAppInit_DLLs
```
- AppInit_DLLs: should be empty string
- LoadAppInit_DLLs: should be 0x0 (disabled)

---

## Lab 6 — Boot Execute Check
```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager" /v BootExecute
```
- Expected: `autocheck autochk *` only
- Anything else executes before OS fully initializes

---

## Lab 7 — LSA Package Check
```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v "Authentication Packages"
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v "Security Packages"
```
- Authentication Packages: normally msv1_0 only
- Security Packages: known list of standard packages
- Additions = DLL loaded inside lsass.exe

---

## Lab 8 — Complete Persistence Check Script
```cmd
echo === RUN KEYS === && reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" 2>nul && reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" 2>nul
echo === WINLOGON === && reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Userinit && reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell
echo === IFEO === && reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options" /s 2>nul | findstr "Debugger"
echo === APPINIT === && reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows" /v AppInit_DLLs && reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows" /v LoadAppInit_DLLs
echo === BOOT EXECUTE === && reg query "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager" /v BootExecute
echo === LSA === && reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v "Security Packages"
```
- Full persistence audit in one command chain
- Save output: append `> persistence_check.txt` at end
- Compare against clean baseline to find deviations