# Day 64 — Windows Event Logging Channels

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 6 — Windows Event Logging
**Subtopic:** 1 — Log Channels

---

## What It Is

Windows organizes events into channels — separate log files per category.
Each channel receives events from specific sources.
Security channel = primary SOC visibility source for Windows.

## Event Structure

Every event has:
- Event ID — numeric identifier. Standardized across Windows versions.
- Source — component that generated it.
- Timestamp — when it happened.
- Level — Information, Warning, Error, Critical, Audit Success, Audit Failure.
- Data — additional details specific to the event type.

## Three Classic Channels

**Application** — user-mode app and service events.
Path: C:\Windows\System32\winevt\Logs\Application.evtx
Useful: app errors, crashes, start/stop. Less security-critical.

**Security** — ALL security audit events. Most important channel.
Path: C:\Windows\System32\winevt\Logs\Security.evtx
Requires admin to read. Contains: auth events, account changes, privilege use.
Default size: 20MB. On busy machines this covers only hours of history.

**System** — Windows components and driver events.
Path: C:\Windows\System32\winevt\Logs\System.evtx
Useful: service start/stop (detect malicious service), hardware errors, GPO.

## Key Additional Security Channels

PowerShell/Operational — script block logging. Every PS command when enabled.
Sysmon/Operational — process creation, network, file, registry (if Sysmon installed).
TaskScheduler/Operational — scheduled task creation and execution.
TerminalServices-LocalSessionManager — RDP connections with source IPs.
WMI-Activity/Operational — WMI execution (common attacker tool).

## Audit Policy

Controls which events are recorded in Security channel.
Default Windows: many important subcategories disabled.
Key subcategories to enable:
  Logon/Logoff: 4624, 4625, 4634
  Account Management: 4720, 4732
  Privilege Use: 4672
  Process Creation: 4688 (often disabled by default)
  Policy Change: 4719

Check: auditpol /get /category:*
Subcategory showing "No Auditing" = blind spot.

## Log Forwarding — Security Control

Local logs: admin can clear them (wevtutil cl Security).
Forwarded logs: protected from local tampering.
WEF (Windows Event Forwarding): forwards to central WEC server.
SIEM integration: further protection and analysis.
Log forwarding = security control, not just convenience.

## Commands (Windows 10 VM)

```cmd
eventvwr.msc
wevtutil el
wevtutil gl Security
wevtutil gl Application
wevtutil gl System
wevtutil qe Security /c:10 /rd:true /f:text
auditpol /get /category:*
wevtutil gl Security | findstr /I "logFileName\|maxSize\|fileSize\|numberOfLogRecords"
wevtutil epl Security C:\Users\Public\security_log_backup.evtx
```