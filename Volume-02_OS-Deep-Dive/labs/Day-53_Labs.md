# Day 53 — Labs: NTFS Permissions and ACLs

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — View File ACL
```cmd
icacls C:\Windows\System32\notepad.exe
```
- Shows ACL for notepad.exe
- (I) = inherited. F = Full Control. RX = Read and Execute.
- TrustedInstaller typically owns system binaries

---

## Lab 2 — View Directory ACL
```cmd
icacls C:\Windows\System32\
```
- (OI) = Object Inherit — applies to files inside
- (CI) = Container Inherit — applies to subfolders inside
- Compare with file ACL above

---

## Lab 3 — Sensitive Directory Check
```cmd
icacls C:\Users\
icacls "C:\Program Files\"
```
- C:\Users\ — each user should only access their own profile
- Program Files — standard users should have Read and Execute only
- Write access for standard users here = misconfiguration

---

## Lab 4 — World-Writable Directories
```cmd
icacls C:\Windows\Temp\
icacls C:\Users\Public\
```
- These are intentionally permissive
- Everyone having Write here is expected and by design
- Same permissions on system or application directories = finding

---

## Lab 5 — Create File and View Default ACL
```cmd
echo "sensitive data" > C:\Users\Public\sensitive.txt
icacls C:\Users\Public\sensitive.txt
```
- New file inherits parent folder permissions
- Shows default inherited ACL on freshly created file

---

## Lab 6 — Grant Permission
```cmd
icacls C:\Users\Public\sensitive.txt /grant Everyone:R
```
- `/grant` — adds an Allow ACE
- `Everyone:R` — grants Read to all users including unauthenticated
- Granting Everyone any access to sensitive files = security finding

---

## Lab 7 — Deny Permission
```cmd
icacls C:\Users\Public\sensitive.txt /deny Everyone:W
```
- `/deny` — adds an explicit Deny ACE
- Deny fires before Allow in evaluation
- Even group Allow entries cannot override explicit Deny

---

## Lab 8 — View Combined ACL
```cmd
icacls C:\Users\Public\sensitive.txt
```
- Shows both Grant (R) and Deny (W) entries coexisting
- Demonstrates how DACL holds multiple ACE types simultaneously

---

## Lab 9 — Reset and Clean Up
```cmd
icacls C:\Users\Public\sensitive.txt /reset
del C:\Users\Public\sensitive.txt
```
- `/reset` — removes all explicit ACEs, restores pure inheritance
- `del` — removes test file
- Always clean up after permission labs