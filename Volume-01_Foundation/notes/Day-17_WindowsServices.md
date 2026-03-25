# Day 17 — Windows Services

**Date:** 25-03-26
**Volume:** 01 — Computer and Internet Foundations
**Topic:** Windows OS Basics
**Subtopic:** 4 — Windows Services

---

## What It Is in Plain English

A service is a background program Windows runs automatically without a
user needing to be logged in. No taskbar icon. No double-click to start.
SCM (services.exe) manages all services — starts them at boot, restarts
them if they crash, stops them on shutdown.

Security relevance: services are the primary persistence mechanism used
by attackers. Malicious service = malware that survives every reboot.

---

## Key Terms

**SCM (Service Control Manager)** — services.exe. Parent of every service
process. Reads HKLM\SYSTEM\CurrentControlSet\Services at boot and starts
all Automatic services.

**Service binary** — the actual .exe or .dll the service runs. Its path is
stored in the registry. Wrong path = first investigation finding.

**Start types:**
- Automatic — starts at boot before login
- Automatic (Delayed) — starts shortly after boot
- Manual — starts only when requested
- Disabled — never starts

**Service accounts:**
- LocalSystem — full local admin rights, highest risk
- NetworkService — limited local, network as computer account
- LocalService — minimum rights, most secure, least privilege

**svchost.exe** — hosts multiple services in one process. Many instances
running is normal. Verify by checking path and parent.

**Registry location:** HKLM\SYSTEM\CurrentControlSet\Services\[ServiceName]

---

## The Three Service Accounts — Security Priority

| Account | Local Rights | Risk |
|---|---|---|
| LocalSystem | Full admin | HIGHEST |
| NetworkService | Limited | MEDIUM |
| LocalService | Minimum | LOWEST |

Audit: which services run as LocalSystem unnecessarily? Those violate
least privilege and expand attack surface.

---

## Svchost.exe — Legitimate vs Malicious

**Legitimate svchost.exe:**
- Path: C:\Windows\System32\svchost.exe — always
- Parent: services.exe — always
- Arguments: -k [GroupName] — always

**Malicious masquerading as svchost.exe:**
- Path: anywhere other than System32
- Parent: anything other than services.exe
- Arguments: missing or unusual

---

## Real Attack Example — Malware Service Installation
```
sc create MalSvc binPath= "C:\Windows\Temp\update.exe" start= auto
sc failure MalSvc reset= 0 actions= restart/60000
sc start MalSvc
```

Result: malware starts at every boot, restarts if killed, runs as
LocalSystem by default. Survives reboot indefinitely until removed.

Investigation catches it: binary path points to Temp folder. Legitimate
services do not live in Temp.

---

## The Four Checks for Every Suspicious Service

1. **Binary path** — System32 or Program Files = expected. Anywhere else = finding.
2. **Account** — LocalSystem when not needed = least privilege violation.
3. **Start type** — recently changed to Automatic = malware installation timeline.
4. **Digital signature** — unsigned binary claiming to be Windows component = finding.

---

## Security Angle — Stuxnet 2010

Stuxnet installed two kernel-mode driver services using stolen legitimate
digital certificates. Bypassed Windows driver signing. Ran undetected for
years in Iranian nuclear facility networks. Detection required kernel-level
analysis most organizations did not perform. Takeaway: even properly signed
services with legitimate-looking names require verification.

---

## Connection to Previous Topics

Day 14: services.exe (PID 684) is the SCM — parent of all services.
Day 15: service binaries stored on NTFS — permissions and timestamps apply.
Day 16: service binaries should live in System32 or Program Files — wrong
folder = same logic as folder structure investigation from Day 16.

---

## Interview Prep — in interview-prep/Vol1_QA.md

**Recall:**
1. What is a Windows service vs a regular application?
2. What is services.exe and what does it do?
3. Three service accounts and their privilege levels?
4. How to distinguish legitimate svchost.exe from malware?
5. Registry location for service configuration?

**Scenario:**
6. svchost.exe running from C:\Users\Public — investigation steps?
7. Attacker still has access after reboot — what persistence do you check first?