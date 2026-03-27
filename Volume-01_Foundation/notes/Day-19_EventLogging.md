# Day 19 — Topic 5: Windows OS Basics
# Subtopic 6 — Windows Event Logging

---

## What is Windows Event Logging

Windows Event Logging is the built-in system Windows uses to record everything significant that happens on the machine. Every login attempt, every failed password, every service starting or stopping, every policy change — Windows writes a record into log files called event logs.

Event logs are the CCTV footage of a Windows machine. When something bad happens — malware runs, attacker logs in, account gets compromised — the evidence is in the event logs. As a SOC analyst, event logs are the first thing you look at during any Windows investigation.

---

## Key Terms

| Term | Meaning |
|---|---|
| Event | A single recorded action. One login attempt = one event. Has ID, timestamp, source, and details. |
| Event ID | A number identifying what type of event occurred. 4624 always means successful login. Fixed worldwide. |
| Event log | A file storing a collection of events. Multiple logs for different categories. |
| Log channel | A specific event log file — Security channel, System channel, Application channel. |
| Event Viewer | GUI tool for reading event logs. eventvwr.msc |
| wevtutil | Command-line tool for querying event logs. More powerful than GUI for investigations. |
| Source | Which component generated the event. |
| Audit policy | Controls which event types get logged. If not enabled — events never recorded. |
| Logon type | Number inside login events showing HOW user logged in. Type 2 = keyboard, Type 10 = RDP. |
| SID | Security Identifier. Unique number identifying every user account. Appears in security events. |
| EVTX | File format for Windows event logs. Stored in C:\Windows\System32\winevt\Logs\ |

---

## The Three Log Channels

| Channel | What It Records | Security Use |
|---|---|---|
| Security | All security events — logins, logouts, account changes, privilege use, policy changes | First place to look in any investigation — 4624, 4625, 4648 live here |
| System | Windows system components — services starting/stopping, hardware errors, driver failures | Investigate crashes and service issues |
| Application | Applications installed on machine — software errors, warnings | Less critical but sometimes shows malicious software activity |

---

## The Three Critical Event IDs

### Event ID 4624 — Successful Logon
Someone successfully logged into this machine.

**Key fields:**
- Account Name — which account logged in
- Logon Type — HOW they logged in
- Source IP — where the login came from
- Logon ID — unique session ID referenced by other events
- Authentication Package — NTLM or Kerberos

**Logon Types:**

| Type | Name | Meaning |
|---|---|---|
| 2 | Interactive | Sat at keyboard, logged in locally |
| 3 | Network | Connected over network — file share, mapped drive |
| 4 | Batch | Scheduled task ran |
| 5 | Service | A service started |
| 7 | Unlock | User unlocked a locked workstation |
| 8 | NetworkCleartext | Network login sending password in cleartext — red flag |
| 9 | NewCredentials | runas with different credentials |
| 10 | RemoteInteractive | RDP login |
| 11 | CachedInteractive | Login using cached credentials — no DC contact |

**Security relevance:**
- Logon Type 10 from unexpected IP = possible RDP attack
- Logon Type 3 at 3AM from internal IP to server = possible lateral movement
- Many 4624 with different accounts from same IP = credential stuffing
- 4624 after many 4625 from same IP = brute force that succeeded

---

### Event ID 4625 — Failed Logon
Someone tried to log in and failed.

**Key fields:**
- Account Name — which account was attempted
- Failure Reason — why it failed
- Sub Status — specific failure code
- Logon Type — how they tried
- Source IP — where attempt came from

**Sub Status codes:**

| Code | Meaning |
|---|---|
| 0xC000006A | Wrong password — account exists |
| 0xC0000064 | Account does not exist |
| 0xC0000234 | Account locked out |
| 0xC0000072 | Account disabled |
| 0xC000006F | Login outside allowed hours |

**Security relevance:**
- 500 failed logins from same IP in 5 minutes = brute force
- Failed logins for Administrator = attacker targeting most powerful account
- 0xC0000064 in bulk = attacker guessing usernames
- 0xC000006A in bulk for one account = password spray or brute force
- 4625 followed immediately by 4624 same account = brute force succeeded

**Detection rule: 5 or more 4625 from same source IP within 1 minute = brute force alert**

---

### Event ID 4648 — Logon Using Explicit Credentials
Someone used explicit credentials while already logged in as someone else.

**Key fields:**
- Subject — the account currently logged in
- Account Whose Credentials Were Used — the account whose credentials were explicitly provided
- Target Server — what resource they were accessing
- Process Name — which program made the credential call

**Security relevance:**
- Signature of lateral movement. Attacker compromised Account A, uses Account B's stolen credentials to access another machine.
- You see 4648 on source machine and 4624 Logon Type 3 on target machine.
- Legitimate 4648 events exist — service accounts doing their job.
- 4648 at odd hours for user accounts accessing unusual servers = high priority finding.
- Combined with Mimikatz indicators = attacker extracted and immediately used credentials.

---

## Brute Force Attack — Full Timeline with Event IDs

Step 1 — attacker from IP 185.220.101.45 starts brute force against RDP at 2:47 AM.

Step 2 — Windows generates hundreds of 4625 events:
EventID 4625 | Account: Administrator | Logon Type: 10 | Sub Status: 0xC000006A | Source: 185.220.101.45

Step 3 — at 02:51:33 after 847 attempts — password guessed correctly.

Step 4 — Windows generates 4624:
EventID 4624 | Account: Administrator | Logon Type: 10 | Source: 185.220.101.45

Step 5 — attacker runs Mimikatz — dumps lsass — moves laterally — generates 4648 on this machine, 4624 Logon Type 3 on file server.

Step 6 — SIEM correlation rule fires: 50+ Event ID 4625 from same IP in 60 seconds followed by 4624 from same IP. SOC analyst investigates. Timeline established. Escalated.

---

## Security Angle — Colonial Pipeline 2021

The attack started with one compromised VPN password obtained from a leaked credential database. No MFA. The attacker logged in with valid credentials — generating one clean 4624 event that looked completely normal. No 4625 events before it because the password was correct first try.

Credential stuffing (trying known leaked passwords) is harder to detect than brute force — very few failed attempts before succeeding. This is why monitoring 4624 for unusual logon times and unusual source IPs is as important as monitoring 4625 for brute force.

---

## EVTX File Locations

```
C:\Windows\System32\winevt\Logs\Security.evtx
C:\Windows\System32\winevt\Logs\System.evtx
C:\Windows\System32\winevt\Logs\Application.evtx
```

Forensic investigators copy these files from compromised machines and analyze them offline using tools like Event Viewer, Autopsy, or SIEM ingestion.

---

*Volume 01 — Topic 5 — Subtopic 6 | Day 19 | Exam Season*