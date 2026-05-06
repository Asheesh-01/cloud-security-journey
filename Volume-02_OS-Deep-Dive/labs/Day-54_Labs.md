# Day 54 — Labs: Local vs Domain Users

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — List All Local Accounts
```cmd
net user
```
- Lists all local user accounts stored in SAM
- Domain accounts not shown — local only
- Unexpected accounts = immediate investigation

---

## Lab 2 — Detailed User Information
```cmd
net user %USERNAME%
```
- `%USERNAME%` — expands to current logged-in username
- Shows: active status, last logon, group memberships,
  password age, expiry, logon hours, workstations allowed

---

## Lab 3 — List All Local Groups
```cmd
net localgroup
```
- All local groups on this machine
- Key security groups: Administrators, Users, Guests,
  Remote Desktop Users, Backup Operators

---

## Lab 4 — Administrators Group Members
```cmd
net localgroup Administrators
```
- Every account with local administrator privileges
- Unexpected accounts here = critical finding
- In Volume 1: backdoor account appeared here after net localgroup add

---

## Lab 5 — GUI User Management
```cmd
lusrmgr.msc
```
- Local Users and Groups Manager
- Users folder: all local accounts with status icons
- Groups folder: all local groups with member lists
- Disabled accounts shown with down arrow icon

---

## Lab 6 — Built-in Administrator Status
```cmd
net user Administrator
```
- Account active: No = correctly hardened
- Account active: Yes = finding — enable only when needed
- Password last set date is a forensic artifact

---

## Lab 7 — Guest Account Status
```cmd
net user Guest
```
- Should always show Account active: No
- Guest has no password by default
- Enabled Guest = unauthenticated network access risk

---

## Lab 8 — Local Account Policy
```cmd
net accounts
```
- Password minimum length, age, history settings
- Lockout threshold and duration
- These govern local accounts only
- Domain accounts follow domain Group Policy instead