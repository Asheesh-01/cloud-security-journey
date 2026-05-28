# Day 65 — Event ID 4624: Successful Logon

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 6 — Windows Event Logging
**Subtopic:** 2 — Event ID 4624

---

## What It Is

Generated on every successful Windows logon.
Primary authentication visibility event.
Contains 20+ fields including who, when, where from, and how.

## Key Fields

**Logon ID** — unique session identifier. Links to 4634 (logoff), 4672 (privileges).
**Subject** — account that was logged in when this occurred. Usually SYSTEM.
**New Logon** — the account that just authenticated. This is what you care about.
**Network Information** — source machine name and source IP. Lateral movement detection.
**Authentication Package** — NTLM or Kerberos. NTLM on Kerberos domain = finding.

## Logon Types — Complete Decode

2 = Interactive. Physical keyboard or console.
3 = Network. SMB, remote registry, file share. Source IP visible.
4 = Batch. Scheduled task executed.
5 = Service. Windows service started.
7 = Unlock. Locked workstation unlocked.
8 = NetworkCleartext. Credentials sent unencrypted. Should not exist.
9 = NewCredentials. RunAs /netonly. Pass-the-Hash technique.
10 = RemoteInteractive. RDP connection. Source IP = attacker location.
11 = CachedInteractive. Offline domain logon using cached credentials.

## Detection Patterns

**Lateral movement:**
Type 3 logon on server from unusual source IP.
Off-hours timing. Domain admin account. Unfamiliar source workstation.

**Pass-the-hash:**
Type 3 logon with NTLM authentication on Kerberos domain.
AuthenticationPackageName = NTLM = flag for investigation.

**Service account abuse:**
Service account normally Type 5 on one server.
Appears as Type 3 across multiple servers = stolen credentials.

**RDP compromise:**
Multiple 4625 failures then 4624 Type 10 success from same IP.
Brute force culminating in successful RDP access.

**Scheduled task persistence:**
New Type 4 logons from an account not previously seen in batch logons.
New scheduled task installed by attacker firing for first time.

## Commands (Windows 10 VM)

```cmd
wevtutil qe Security /q:"*[System[EventID=4624]]" /c:10 /rd:true /f:text
wevtutil qe Security /q:"*[System[EventID=4624] and EventData[Data[@Name='LogonType']='2']]" /c:5 /rd:true /f:text
wevtutil qe Security /q:"*[System[EventID=4624] and EventData[Data[@Name='LogonType']='10']]" /c:5 /rd:true /f:text
wevtutil qe Security /q:"*[System[EventID=4624] and EventData[Data[@Name='LogonType']='5']]" /c:5 /rd:true /f:text
wevtutil qe Security /q:"*[System[EventID=4624]]" /c:3 /rd:true /f:xml
powershell "Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4624; StartTime=(Get-Date).AddHours(-1)} | Select-Object TimeCreated,Message | Format-List"
```