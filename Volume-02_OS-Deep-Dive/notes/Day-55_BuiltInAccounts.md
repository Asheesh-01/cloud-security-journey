# Day 55 — Built-in Windows Accounts

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 3 — Windows Users and Groups
**Subtopic:** 2 — Built-in Accounts

---

## What It Is

Accounts created by Windows during installation. Fixed well-known SIDs.
Identical SIDs on every Windows machine worldwide.
Cannot be deleted — only disabled or renamed.
Attackers know exactly what each one is and what it can do.

## The Four Built-in Accounts

**Administrator — S-1-5-500**
Full local control. Cannot be locked out. Cannot be deleted.
Should be DISABLED on all production machines.
If enabled: unlimited brute force attempts possible — no lockout.
Hardening: disable it, use a named admin account instead.
Rename: minimal security benefit — SID stays S-1-5-500, tools find it.

**Guest — S-1-5-501**
No password by default. Member of Guests group only.
Severely restricted — cannot install software or change settings.
Should be DISABLED always. Windows disables by default.
If enabled: unauthenticated access to machine. Never acceptable.

**SYSTEM — S-1-5-18**
Not a login account. No password. Does not appear in user list.
Most powerful account on local machine — more powerful than Administrator.
Represents the OS itself. Kernel runs as SYSTEM. Core services run as SYSTEM.
Unrestricted access to all files, registry, processes, memory.
Goal of local privilege escalation attacks: user → admin → SYSTEM.
Legitimate SYSTEM processes: smss.exe, csrss.exe, wininit.exe,
  services.exe, lsass.exe, svchost.exe hosting core services.
Unexpected process running as SYSTEM = immediate investigation.

**DefaultAccount — S-1-5-93-0**
Used for multi-user manifest apps. Disabled by default.
No interactive login. No password. Minimal security relevance.
Value: recognition — its presence in user list is normal and expected.
Suspicious: enabled status or presence in privileged groups.

**WDAGUtilityAccount**
Used by Windows Defender Application Guard (Edge isolation).
Disabled if Application Guard not enabled.
Value: recognition — normal in user list, suspicious if privileged.

## Built-in Groups With Security Significance

**Administrators** — full local admin rights. Most critical group.
Unexpected account here = high severity finding.

**Guests** — severely restricted. Guest account primary member.

**Remote Desktop Users** — RDP access. Unexpected members = persistence.

**Backup Operators** — read ANY file regardless of NTFS ACL.
Can read SAM database. Often overlooked in security assessments.
Non-admin in this group = ACL bypass capability.

**Event Log Readers** — read event logs including Security log.
Unexpected members = attacker maintaining persistent log access.

## SYSTEM vs Administrator

SYSTEM bypasses restrictions that apply to Administrator.
Some registry keys and file paths accessible to SYSTEM not Administrator.
Kernel enforces this distinction.
Admin → SYSTEM escalation: several documented techniques exist.
This is why SYSTEM is the actual goal, not just Administrator.

## Commands (Windows 10 VM)

```cmd
net user Administrator
net user Guest
net user DefaultAccount
net user WDAGUtilityAccount
wmic useraccount where name="Administrator" get name,sid
wmic useraccount where name="Guest" get name,sid
tasklist /FI "USERNAME eq SYSTEM" /FO TABLE
powershell "Get-Process | Where-Object {$_.SI -eq 0} | Select-Object Name,Id,Path | Format-Table -AutoSize"
net localgroup Administrators
net localgroup "Backup Operators"
net localgroup "Remote Desktop Users"
net accounts
powershell "Get-LocalUser | Select-Object Name,Enabled,PasswordRequired,LastLogon | Format-Table -AutoSize"
```