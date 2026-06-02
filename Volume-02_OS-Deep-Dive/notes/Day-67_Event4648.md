# Day 67 — Event ID 4648: Explicit Credentials / Lateral Movement

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 6 — Windows Event Logging
**Subtopic:** 4 — Event ID 4648

---

## What It Is

Generated when a process uses explicitly specified credentials
different from the currently logged-in user's credentials.
Primary lateral movement indicator Event ID.
Fires on SOURCE machine — not the destination being accessed.

## Key Fields

**Subject Account Name** — who is currently logged in. The compromised account.
**Account Whose Credentials Were Used** — the explicit credentials specified.
  In lateral movement: the stolen privileged account.
**Target Server Name** — where the explicit credentials were directed.
  In lateral movement: the next machine being accessed.
**Process Name** — what initiated the explicit credential logon.
  Legitimate: runas.exe, lsass.exe, known applications.
  Suspicious: powershell.exe, cmd.exe, unknown binary from AppData.

## How 4648 Fits With 4624 and 4625

4624 — fires on DESTINATION. Records successful logon.
4625 — fires on DESTINATION. Records failed logon.
4648 — fires on SOURCE. Records explicit credential use.

Lateral movement full picture:
4648 on source (stolen credentials used) → 4624 Type 3 on destination (access succeeded).
Two log sources. One attack. Correlated by timestamp and account name.

## Legitimate vs Malicious Patterns

Legitimate:
- Admin uses RunAs. Process = runas.exe. Target = local.
- Scheduled task under service account. Process = taskeng.exe.
- Mapped network drive with stored credentials.
- IIS app pool startup under specific account.

Malicious:
- Standard user → explicit domain admin credentials → sensitive server.
- Multiple 4648 events targeting different servers rapidly (mass lateral movement).
- Process generating 4648 is powershell.exe, cmd.exe, or unknown binary.
- Service account credentials used that should never be used interactively.

## Mimikatz Connection

After credential dump: attacker has NTLM hashes or Kerberos tickets.
sekurlsa::pth (pass the hash) → explicit credential use → 4648 fires.
kerberos::ptt (pass the ticket) → explicit credential use → 4648 fires.
4648 shows: compromised account + stolen privileged creds + destination target.

## Detection Queries

```cmd
wevtutil qe Security /q:"*[System[EventID=4648]]" /c:10 /rd:true /f:text
wevtutil qe Security /q:"*[System[EventID=4648]]" /c:3 /rd:true /f:xml
wevtutil qe Security /q:"*[System[EventID=4648]]" /c:20 /rd:true /f:text | findstr /I "Target Server\|Account Name\|Process Name"
```

## Investigation Checklist When 4648 Found

1. What account is the Subject (who is already logged in)?
2. What account's credentials were used explicitly?
3. Are these credentials more privileged than Subject?
4. What process initiated the explicit credential logon?
5. What is the Target Server?
6. What time did this happen — business hours or off-hours?
7. Correlate: did a 4624 Type 3 appear on the Target Server at same time?
8. Are there multiple 4648 events in rapid succession to different targets?

## Commands (Windows 10 VM)

```cmd
wevtutil qe Security /q:"*[System[EventID=4648]]" /c:10 /rd:true /f:text
runas /user:%COMPUTERNAME%\Administrator "cmd.exe /c whoami"
wevtutil qe Security /q:"*[System[EventID=4648]]" /c:5 /rd:true /f:text
wevtutil qe Security /q:"*[System[EventID=4648]]" /c:3 /rd:true /f:xml
wevtutil qe Security /q:"*[System[EventID=4648]]" /c:20 /rd:true /f:text | findstr /I "Target Server\|Account Name\|Process Name"
```