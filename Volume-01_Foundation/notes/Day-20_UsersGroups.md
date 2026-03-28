# Day 20 — Topic 5: Windows OS Basics
# Subtopic 7 — Windows Users and Groups

---

## What is Windows Users and Groups

Windows uses a system of user accounts and groups to control who can do what on a machine. Every person who logs in has a user account. Every user account belongs to one or more groups. Groups define what permissions a user has — what files they can access, what programs they can run, what system changes they can make.

For security this matters in two directions. First — attackers try to escalate from a low-privilege user account to a high-privilege one. Second — defenders check user accounts for unauthorized additions, privilege escalation signs, and accounts that should not exist.

---

## Key Terms

| Term | Meaning |
|---|---|
| User account | An identity on Windows. Has a username, password, SID, and group memberships. |
| Group | A collection of user accounts. Permissions assigned to the group apply to all members. |
| SID | Security Identifier. Unique number identifying every account and group. Format: S-1-5-21-XXXXXXXXX-1000 |
| Local account | Exists only on this one machine. Cannot log into other machines with it. |
| Domain account | Exists in Active Directory. Can log into any machine in the domain. |
| Built-in account | Accounts Windows creates automatically — Administrator, Guest, SYSTEM. |
| Built-in group | Groups Windows creates automatically — Administrators, Users, Guests. |
| Administrator | Most powerful local account. Full control of the machine. |
| SYSTEM | Not a real login account. Used by the OS kernel and services internally. More powerful than Administrator. |
| Guest | Disabled by default. Extremely limited access. Should always stay disabled. |
| UAC | User Account Control. Even Administrators run at medium integrity by default. Must confirm elevation to high integrity for admin tasks. |
| Integrity level | A Windows security layer on top of permissions. Low, Medium, High, System. Processes run at different integrity levels. |
| lusrmgr.msc | Local Users and Groups management console — GUI tool. |
| net user | Command-line tool for managing local user accounts. |
| net localgroup | Command-line tool for managing local groups. |

---

## Built-in Accounts

### Administrator
- Created automatically when Windows installs
- Member of Administrators group
- Full control of the machine — install software, change settings, modify system files
- In modern Windows — disabled by default, a separate admin account is created during setup
- **Security relevance:** Attackers always try to gain Administrator access. If compromised, they own the machine. Brute force attacks commonly target the Administrator account by name — which is why renaming or disabling it reduces attack surface.

### Guest
- Created automatically, disabled by default
- Severely limited access — cannot install software, cannot change settings
- Has no password by default
- **Security relevance:** Should always remain disabled. If Guest is enabled on a machine you are investigating — that is a finding. Malware sometimes enables Guest to create a backdoor low-privilege entry point.

### SYSTEM
- Not a real login account — you cannot log in as SYSTEM
- Used internally by the Windows kernel and system services
- More powerful than Administrator — can access files that even Administrator cannot
- Services running as LocalSystem (which you learned in subtopic 4) run with SYSTEM privileges
- **Security relevance:** Malware that achieves SYSTEM-level execution owns the machine completely. Privilege escalation attacks aim to go from User → Administrator → SYSTEM.

### DefaultAccount
- A managed service account used by Windows for system-managed scenarios
- Disabled by default
- Not relevant for most investigations

---

## Built-in Groups

### Administrators
- Members have full control of the machine
- Administrator account is a member by default
- Any user added here gets complete machine control
- **Security relevance:** Unauthorized addition to Administrators group = privilege escalation. This is one of the first things an attacker does after compromising a machine — add their account or a backdoor account to Administrators. During investigation always check who is in this group and when membership changed.

### Users
- Standard group all normal user accounts belong to
- Can run applications, use the machine, save files
- Cannot install software, cannot modify system settings
- **Security relevance:** This is the correct group for any regular employee account. An account that should be in Users but is also in Administrators = misconfiguration or compromise.

### Guests
- Members of the Guest group
- Most restricted — even less than Users
- Guest account is a member by default

### Remote Desktop Users
- Members can connect to this machine via RDP
- Regular Users cannot RDP by default — must be explicitly added here
- **Security relevance:** Check this group during investigation. Unauthorized accounts added to Remote Desktop Users = attacker setting up RDP access for persistence.

### Backup Operators
- Members can bypass file and directory permissions to perform backups
- Can read files they normally cannot access
- **Security relevance:** Attackers add their account to Backup Operators to read sensitive files without being in Administrators. Less obvious than Administrators membership but equally dangerous for data theft.

---

## How UAC Works — Important for Security

Even if you are a member of the Administrators group, Windows runs your processes at medium integrity by default. This is UAC — User Account Control.

When you try to do something that requires admin rights — install software, modify system files — Windows shows the UAC prompt. You confirm, and that specific action runs at high integrity. Everything else you do still runs at medium integrity.

**Why this matters for security:**
- Malware running in your user session starts at medium integrity even if your account is in Administrators
- The malware needs to bypass UAC to escalate to high integrity
- UAC bypass techniques exist — dozens of them — and attackers use them constantly
- UAC is not a security boundary by Microsoft's own definition — it is a convenience feature to reduce accidental admin actions. A determined attacker will bypass it.

**Integrity levels:**
- System — kernel and SYSTEM account processes
- High — elevated administrator processes (after UAC confirmation)
- Medium — standard user processes and non-elevated administrator processes
- Low — sandboxed processes — browser tabs, downloaded content

---

## Real Practical Example — Attacker Creates Backdoor Account

Step 1 — attacker gains code execution on the machine through phishing. Running as vboxuser at medium integrity.

Step 2 — attacker uses a UAC bypass technique to escalate to high integrity Administrator.

Step 3 — attacker creates a hidden backdoor account:
```cmd
net user backdoor P@ssw0rd123 /add
```
New account named backdoor created with password P@ssw0rd123.

Step 4 — attacker adds it to Administrators group:
```cmd
net localgroup Administrators backdoor /add
```
backdoor account now has full machine control.

Step 5 — attacker hides the account from the login screen by modifying a registry value so it does not appear on the Windows login screen.

Step 6 — attacker can now RDP into this machine anytime using backdoor account. Even if the original phishing payload is detected and removed — the backdoor account survives.

Step 7 — SOC analyst investigates. Runs `net user` — sees unexpected account backdoor. Checks when it was created — matches incident timeline. Checks group membership — Administrators. Checks Event ID 4720 (account created) and Event ID 4732 (account added to group) in Security log — confirms timeline. Account disabled and deleted. Registry change reverted. Incident documented.

---

## Security Angle — Real Attack

In the 2020 SolarWinds supply chain attack, after gaining access to victim networks, attackers created new domain administrator accounts to maintain persistent access. The accounts were named to blend in with legitimate service accounts — using naming conventions that matched existing accounts. Detection required comparing current account lists against known-good baselines — any account that did not exist before the compromise window was a finding. This shows why baselining your user accounts and monitoring Event ID 4720 (new account created) and 4732 (account added to privileged group) is critical.

---

*Volume 01 — Topic 5 — Subtopic 7 | Day 20 | Exam Season*