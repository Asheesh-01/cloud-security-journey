# Day 58 — Windows Services

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 4 — Windows Processes and Services
**Subtopic:** 2 — Windows Services

---

## What It Is

Background programs managed by SCM (services.exe, PID 684).
Start before user login. Run without logged-in user.
Primary Windows persistence mechanism for sophisticated malware.

## Key Terms

**SCM** — Service Control Manager. services.exe. Manages all services.
Reads config from HKLM\SYSTEM\CurrentControlSet\Services at boot.

**Service binary path** — ImagePath in registry. First thing to check.
Legitimate: System32, SysWOW64, Program Files.
Suspicious: Temp, AppData, Public, user profile folders.

**Start types:**
Auto = starts at boot. Delayed Auto = starts after boot.
Manual = starts on request. Disabled = cannot start.
Boot/System = kernel drivers, before SCM.

**Service accounts:**
LocalSystem = full local access. Most powerful. Often over-privileged.
NetworkService = limited local, network as machine account.
LocalService = minimal local, anonymous network.
Custom account = correct approach for least privilege.

**Service DLL** — DLL loaded by svchost.exe. ServiceDll registry value.
Process list shows only svchost.exe. DLL inside is hidden.
Malicious DLL = invisible without examining loaded modules per process.

**Unquoted service path** — path with spaces, no quotes.
C:\Program Files\App\service.exe → Windows tries C:\Program.exe first.
Attacker creates C:\Program.exe → runs as service account on next start.
Common privilege escalation finding in Windows assessments.

**Service failure actions** — what happens when service crashes.
Malware configures: restart self → resilient to process termination.

## Attack Techniques

**Create malicious service:**
sc create MalSvc binPath= "C:\Windows\Temp\mal.exe" start= auto
→ survives reboot, runs as LocalSystem, registry persists until deleted.

**Binary replacement:** overwrite existing service binary.
Service name/description unchanged. Binary on disk is replaced.
Takes effect after reboot or service restart.

**DLL hijacking:** replace DLL loaded by svchost-hosted service.
Invisible in process list — only svchost.exe visible.

**Unquoted path exploitation:** create binary Windows tries before real one.
Requires write access to early path component.

**ImagePath modification:** change existing service's registry ImagePath.
Legitimate service name, malicious binary.

## Audit Commands

```cmd
sc query type= all state= all
wmic service get name,pathname,startmode,state /format:table
wmic service get name,pathname | findstr /V /I "system32\|syswow64\|program files\|windows\|microsoft"
wmic service get name,startname,pathname | findstr /I "localsystem"
sc qc wuauserv
reg query "HKLM\SYSTEM\CurrentControlSet\Services\wuauserv"
wmic service get name,pathname | findstr /I /V """" | findstr /I " "
sc create TestPersistence binPath= "C:\Windows\Temp\test.exe" start= auto
sc delete TestPersistence
```