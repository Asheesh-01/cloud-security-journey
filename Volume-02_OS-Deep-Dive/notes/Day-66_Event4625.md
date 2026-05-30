# Day 66 — Event ID 4625: Failed Logon

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 6 — Windows Event Logging
**Subtopic:** 3 — Event ID 4625

---

## What It Is

Generated on every failed Windows authentication attempt.
Primary event for brute force detection and credential attack analysis.
Status code field reveals how far the attack has progressed.

## Key Fields

**Failure Reason** — text explanation of why logon failed.
**Status / Sub Status** — hex codes. More reliable than text for analysis.
**Caller Process Name** — what process attempted logon.
**Network Information** — source IP for network-based failures.

## Critical Status Codes

0xC000006A — wrong password, valid username. Attacker has account list.
0xC0000064 — username does not exist. Still in enumeration phase.
0xC000006D — generic logon failure. Wrong password.
0xC0000072 — account disabled.
0xC0000234 — account locked out (lockout already triggered).
0xC0000193 — account expired.
0xC000006F — logon outside permitted hours.
0xC0000070 — logon from unauthorized workstation.

## Attack Pattern Detection

**Online brute force:**
High volume 4625 from one IP against one account.
Detection: count per source IP per time window.
Response: account lockout policy triggers 4740.

**Password spray:**
Low volume per account. High account diversity from one IP.
Stays under lockout threshold per account.
Detection: aggregate across accounts — same IP, many accounts, low count each.

**Credential stuffing:**
Many different source IPs. Small percentage of 4624 successes.
Detection: failure rate with intermittent successes from distributed IPs.

## Investigation Chain

4625 (multiple failures) → 4740 (lockout triggered) → 4624 (access after unlock)
Complete brute force story in three Event IDs.

4740 contains: locked account name + caller machine (failure source).

## Status Code Analysis

All 0xC0000064 = still enumerating usernames. No valid account list yet.
All 0xC000006A = valid usernames known. Now guessing passwords.
Mixed codes = multiple attack types or multiple targets.

## Commands (Windows 10 VM)

```cmd
wevtutil qe Security /q:"*[System[EventID=4625]]" /c:10 /rd:true /f:text
wevtutil qe Security /q:"*[System[EventID=4740]]" /c:10 /rd:true /f:text
net use \\localhost\IPC$ /user:nonexistentuser wrongpassword 2>nul
net use \\localhost\IPC$ /user:%USERNAME% wrongpassword 2>nul
powershell "Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625; StartTime=(Get-Date).AddMinutes(-30)} ..."
```