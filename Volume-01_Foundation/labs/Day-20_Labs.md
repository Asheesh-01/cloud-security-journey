# Day 20 — Labs
# Topic 5: Windows OS Basics — Subtopic 7: Windows Users and Groups

---

## Lab Environment
- Host: Ubuntu 24.04 — Acer Swift Go 14
- VM: Windows 10 inside VirtualBox
- Tool: Command Prompt (Administrator)

---

## Lab 1 — List All Local User Accounts

### Command:
```cmd
net user
```

### Command explanation:
- `net` — Network command. Despite the name, it manages many local Windows resources including users and groups.
- `user` — the subcommand targeting user accounts.
- No additional arguments — lists all local user accounts on this machine.

---

## Lab 2 — View Details of Specific User

### Command:
```cmd
net user vboxuser
net user Administrator
net user Guest
```

### Command explanation:
- `net user [username]` — shows full details for that specific account.
- Shows: full name, account active status, password settings, last login, group memberships, logon hours, logon workstations.
- Run for all three accounts to compare — vboxuser (normal user), Administrator (built-in admin), Guest (should be disabled).

---

## Lab 3 — List All Local Groups

### Command:
```cmd
net localgroup
```

### Command explanation:
- `net localgroup` — lists all local groups on this machine.
- Shows all built-in groups and any custom groups created.

---

## Lab 4 — View Group Membership

### Command:
```cmd
net localgroup Administrators
net localgroup Users
net localgroup "Remote Desktop Users"
```

### Command explanation:
- `net localgroup [groupname]` — shows all members of that specific group.
- Quotes needed for group names with spaces.
- **Administrators membership is critical** — any unexpected account here = privilege escalation finding.

---

## Lab 5 — Check User Account Details via GUI

### Command:
```cmd
lusrmgr.msc
```

### Command explanation:
- `lusrmgr.msc` — Local Users and Groups Manager snap-in. GUI tool.
- Navigate to Users folder — see all accounts with their status icons.
- Arrow down icon = account disabled.
- Navigate to Groups folder — click any group to see members.

---

## Lab 6 — Create Test User, Add to Group, Then Delete

### Commands:
```cmd
REM Create a test user account
net user testuser TestP@ss123 /add

REM Verify it was created
net user

REM Add to Administrators group - simulating attacker escalation
net localgroup Administrators testuser /add

REM Verify group membership
net localgroup Administrators

REM Remove from Administrators - remediation step
net localgroup Administrators testuser /delete

REM Verify removal
net localgroup Administrators

REM Delete the test account entirely
net user testuser /delete

REM Verify deletion
net user
```

### Command explanation:
- `net user testuser TestP@ss123 /add` — create new account named testuser with password TestP@ss123. /add flag creates the account.
- `net localgroup Administrators testuser /add` — add testuser to Administrators group. /add flag adds member.
- `net localgroup Administrators testuser /delete` — remove testuser from Administrators. /delete flag removes member.
- `net user testuser /delete` — delete the entire user account. /delete flag removes the account.

---

## Lab 7 — Check SID of User Accounts

### Command:
```cmd
wmic useraccount get name,sid
```

### Command explanation:
- `wmic` — Windows Management Instrumentation Command-line.
- `useraccount` — the WMI class for user accounts.
- `get name,sid` — retrieve only the name and SID columns.
- SID format: S-1-5-21-XXXXXXXXX-XXXXXXXXX-XXXXXXXXX-YYYY where YYYY is the RID (Relative Identifier). RID 500 = built-in Administrator. RID 501 = built-in Guest. RID 1000+ = created accounts.

---

## Lab 8 — Check Security Log for Account Creation Events

### Command:
```cmd
wevtutil qe Security /c:20 /rd:true /f:text /q:"*[System[EventID=4720]]"
wevtutil qe Security /c:20 /rd:true /f:text /q:"*[System[EventID=4732]]"
```

### Command explanation:
- Event ID 4720 — a user account was created. Fires when net user /add runs.
- Event ID 4732 — a member was added to a security-enabled local group. Fires when net localgroup Administrators /add runs.
- These two event IDs are what SOC analysts filter for to detect unauthorized account creation and privilege escalation.
- After running Lab 6, re-run these queries to see the events you generated yourself.

---

## Key Findings From Today's Labs

1. Guest account should always be disabled — verify during any investigation
2. Administrators group membership is the most critical group to audit
3. net user /add and net localgroup /add generate Events 4720 and 4732 — detectable
4. SID RID identifies account type — 500 = Administrator, 501 = Guest, 1000+ = created
5. Attacker backdoor pattern: create account + add to Administrators + hide from login screen
6. Remediation pattern: remove from group + delete account + check event logs for timeline

---

*Volume 01 — Topic 5 — Subtopic 7 | Day 20 | Exam Season*
*Commit: Day 20 - Windows Users and Groups - account management, group membership, privilege escalation lab*