# Day 54 — Local Users vs Domain Users

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 3 — Windows Users and Groups
**Subtopic:** 1 — Local vs Domain Users

---

## What It Is

Two account types on every Windows machine.
Local: stored in SAM on the machine. Valid only there.
Domain: stored in Active Directory. Valid on any domain-joined machine.
Both always coexist — local accounts never disappear in enterprise.

## Key Terms

**SAM database** — Security Accounts Manager. Local account store.
Path: C:\Windows\System32\config\SAM. Registry: HKLM\SAM.
Locked while OS runs. Contains NTLM hashes of local account passwords.
Primary target for offline credential extraction.

**Active Directory** — domain account store on domain controllers.
Compromising DC = compromising every account in the organization.

**LSA** — Local Security Authority. Handles local authentication.
Forwards domain account requests to domain controller.

**LSASS** — lsass.exe. Implements LSA. Stores credential material in memory.
NTLM hashes and Kerberos tickets for logged-in users.
Target of Mimikatz and credential dumping tools. You verified PID 704.

**Kerberos** — primary domain authentication protocol.
Tickets issued by domain controller. Cached in lsass.exe memory.
Pass-the-ticket attack uses stolen Kerberos tickets.

**NTLM** — fallback authentication. Hash-based.
Pass-the-hash attack uses stolen NTLM hash directly — no plaintext needed.

**Credential caching** — domain credentials cached locally for offline use.
HKLM\SECURITY\Cache. Crackable offline if stolen.
Hardening: reduce cached credentials to 0 or 1 via Group Policy.

**LAPS** — Local Administrator Password Solution.
Randomizes local Administrator password per machine.
Stores unique password in Active Directory.
Prevents lateral movement via shared local admin hash.

**Workgroup** — no domain. Peer-to-peer. Each machine manages own accounts.
No centralized policy. Home networks and small businesses.

## Local vs Domain Security Comparison

Local accounts:
- SAM database. LSA authentication. One machine only.
- Not subject to domain Group Policy.
- Built-in accounts always exist regardless of domain join.
- Pass-the-hash attacks use local NTLM hashes.
- Same local admin password across org = lateral movement to all machines.

Domain accounts:
- Active Directory. Kerberos authentication.
- Subject to domain Group Policy.
- Valid on any domain-joined machine.
- Pass-the-ticket attacks use Kerberos tickets from lsass.exe.
- Golden ticket = forged Kerberos ticket valid for entire domain.

## Real Attack — Shared Local Admin Password

Attacker compromises one machine → dumps SAM → recovers local admin hash
→ same hash on all machines (common misconfiguration)
→ authenticates as Administrator on every machine without touching AD.
Fix: LAPS — unique random password per machine stored in AD.

## Commands (Windows 10 VM)

```cmd
net user                          # list all local accounts
net user %USERNAME%               # details for current user
net localgroup                    # list all local groups
net localgroup Administrators     # members with local admin rights
lusrmgr.msc                       # GUI user management
net user Administrator            # check built-in admin status (should be disabled)
net user Guest                    # check guest status (should be disabled)
net accounts                      # local password and lockout policy
```