# Day 68 — UAC: What It Does

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 7 — Windows Permissions and Access Control
**Subtopic:** 1 — UAC

---

## What It Is

User Account Control — introduced Windows Vista 2006.
Prevents programs from running with full admin privileges by default.
Even admin accounts run at Medium integrity until explicitly elevated.
Microsoft explicitly states: UAC is NOT a security boundary.
It is a defense-in-depth control that raises the cost of elevation.

## Core Mechanism: Token Splitting

Admin login → Windows creates TWO tokens:
  Standard token (Medium integrity) — used by default for all programs.
  Elevated token (High integrity) — used only when elevation is confirmed.

Standard token: SeShutdownPrivilege, SeChangeNotifyPrivilege, basic set.
Elevated token: adds SeDebugPrivilege (read any process memory = Mimikatz),
  SeTakeOwnershipPrivilege, SeLoadDriverPrivilege, SeBackupPrivilege.

## Integrity Levels (Mandatory Integrity Control)

Untrusted < Low < Medium < High < System
Medium: standard user processes. Cannot write to High integrity objects.
High: elevated admin processes.
System: SYSTEM processes. Most powerful.
Low: sandboxed processes (old IE, Edge tabs). Cannot write to Medium.
Enforcement: kernel-level via MIC. Not bypassable at the kernel level
  but bypassable through UAC bypass techniques at user-mode level.

## UAC Prompt Types

Consent prompt (blue) — admin account: just click Yes.
  Trusted signed binary requesting elevation.
Credential prompt — standard user: must enter admin credentials.
  Cannot elevate without knowing admin password.
Yellow/orange prompt — unsigned or unverified binary. Higher risk warning.
No prompt — auto-elevating Windows binaries. Prime bypass target.

## UAC Settings Levels

Always notify — most secure. Prompts for everything.
Default — prompts for non-Windows programs. Auto-elevates Windows binaries.
No dim desktop — same as default but no secure desktop. Less secure.
Never notify — UAC disabled. Extremely insecure.

## UAC Registry Configuration

HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System

EnableLUA: 1=enabled (correct), 0=disabled (critical finding).
ConsentPromptBehaviorAdmin: 5=default, 0=silent auto-elevate (finding).
ConsentPromptBehaviorUser: 3=credentials required (default).
PromptOnSecureDesktop: 1=secure desktop (correct), 0=no secure desktop (finding).
EnableVirtualization: 1=virtualization enabled.

## UAC Virtualization

Legacy programs writing to protected locations redirected to:
C:\Users\[user]\AppData\Local\VirtualStore\
Writes to C:\Windows\ → VirtualStore\Windows\
Writes to HKLM\ → HKCU\Software\Classes\VirtualStore\
Program thinks write succeeded. System directories untouched.

## Security Significance

UAC limits blast radius: Medium integrity malware cannot:
- Write to System32 or Program Files
- Modify HKLM registry keys directly
- Install kernel drivers
- Read other processes' memory (without SeDebugPrivilege)

UAC does not stop: documented bypass techniques that silently elevate
from Medium to High without any prompt or user interaction.
No event visible in standard monitoring.
Detection: process at High integrity with no elevation event = UAC bypass.

## Commands (Windows 10 VM)

```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v EnableLUA
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v ConsentPromptBehaviorAdmin
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v PromptOnSecureDesktop
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System"
whoami /groups | findstr /I "integrity"
whoami /priv
dir /A "C:\Users\%USERNAME%\AppData\Local\VirtualStore\" 2>nul
```