# Day 69 — Labs: Share Permissions

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — List All Shares
```cmd
net share
```
- Lists all shares including hidden administrative shares
- C$, ADMIN$, IPC$ present on every Windows machine
- Dollar sign = hidden from network browsing

---

## Lab 2 — View Administrative Share Details
```cmd
net share C$
```
- Shows C$ configuration: path, permissions, connections
- Should show Administrators: Full Control only
- Any other permission entry = finding

---

## Lab 3 — Create a Test Share
```cmd
mkdir C:\Users\Public\TestShare
net share TestShare=C:\Users\Public\TestShare /GRANT:Everyone,READ
```
- Creates folder and shares it with Read for Everyone
- Share accessible at \\localhost\TestShare

---

## Lab 4 — Verify Share Permissions
```cmd
net share TestShare
powershell "Get-SmbShareAccess -Name 'TestShare' | Format-Table -AutoSize"
```
- Confirms Everyone has Read at share level
- `Get-SmbShareAccess` shows AccountName and AccessRight columns

---

## Lab 5 — Audit All Shares and Permissions
```cmd
powershell "Get-SmbShare | ForEach-Object { Write-Output \"=== $($_.Name) ===\"; Get-SmbShareAccess -Name $_.Name | Format-Table AccountName,AccessRight -AutoSize }"
```
- Every share and every permission entry in one pass
- Everyone with Full Control or Change = misconfiguration finding

---

## Lab 6 — Test Network Access to Share
```cmd
dir \\localhost\TestShare
```
- Accesses share over SMB loopback
- Confirms share is accessible and lists visible contents
- Tests actual effective permissions from network perspective

---

## Lab 7 — Compare Share vs NTFS Permissions
```cmd
icacls C:\Users\Public\TestShare
```
- Shows NTFS permissions on shared folder
- Compare with share permissions from Lab 4
- Effective = most restrictive of both systems
- NTFS more restrictive = NTFS wins over network

---

## Lab 8 — Administrative Share Permission Audit
```cmd
powershell "Get-SmbShareAccess -Name 'C$' | Format-Table -AutoSize"
powershell "Get-SmbShareAccess -Name 'ADMIN$' | Format-Table -AutoSize"
```
- Administrative shares should have Administrators only
- Any additional account = critical security finding

---

## Lab 9 — Clean Up
```cmd
net share TestShare /DELETE
rmdir C:\Users\Public\TestShare
```
- Removes share registration and folder
- Always clean up after labs