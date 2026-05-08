# Day 55 — Labs: Built-in Windows Accounts

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — Check All Built-in Account Statuses
```cmd
net user Administrator
net user Guest
net user DefaultAccount
net user WDAGUtilityAccount
```
- Shows full details for each built-in account
- Key: Account active — should be No for all four
- Password last set — forensic artifact showing account history

---

## Lab 2 — Get Built-in Account SIDs
```cmd
wmic useraccount where name="Administrator" get name,sid
wmic useraccount where name="Guest" get name,sid
```
- `wmic useraccount` — queries local accounts via WMI
- Administrator SID ends in -500 on every Windows machine
- Guest SID ends in -501 on every Windows machine
- These endings are well-known and used by tools to find accounts by SID

---

## Lab 3 — List SYSTEM Processes
```cmd
tasklist /FI "USERNAME eq SYSTEM" /FO TABLE
```
- `/FI "USERNAME eq SYSTEM"` — filter to SYSTEM-owned processes
- `/FO TABLE` — table format output
- Every process listed has unrestricted local access
- Unexpected entries here = immediate investigation

---

## Lab 4 — SYSTEM Processes with Paths
```cmd
powershell "Get-Process | Where-Object {$_.SI -eq 0} | Select-Object Name,Id,Path | Format-Table -AutoSize"
```
- `$_.SI -eq 0` — Session 0 = system session where SYSTEM processes run
- Path column reveals binary location
- Legitimate SYSTEM processes always run from System32 or Windows directories

---

## Lab 5 — Security-Relevant Group Members
```cmd
net localgroup Administrators
net localgroup "Backup Operators"
net localgroup "Remote Desktop Users"
```
- Administrators: unexpected accounts = critical finding
- Backup Operators: non-admin account here = ACL bypass capability
- Remote Desktop Users: unexpected accounts = potential persistence

---

## Lab 6 — All Local Users with Status
```cmd
powershell "Get-LocalUser | Select-Object Name,Enabled,PasswordRequired,LastLogon | Format-Table -AutoSize"
```
- `Get-LocalUser` — modern PowerShell cmdlet for local account enumeration
- Enabled: False = correctly disabled
- PasswordRequired: False = account has no password = security risk
- LastLogon: shows when account was last used — forensic artifact

---

## Lab 7 — Account Lockout Policy
```cmd
net accounts
```
- Lockout threshold — how many failures before lockout
- Administrator is exempt from this threshold
- Domain accounts follow domain Group Policy, not this setting

---

## Lab 8 — Current User SID
```cmd
whoami /user
powershell "[System.Security.Principal.WindowsIdentity]::GetCurrent().User.Value"
```
- `whoami /user` — current account name and SID
- PowerShell command — programmatic SID retrieval
- Compare SID structure: S-1-5-21-[machine]-[RID]
- RID 500 = Administrator, 501 = Guest, 1000+ = regular user accounts