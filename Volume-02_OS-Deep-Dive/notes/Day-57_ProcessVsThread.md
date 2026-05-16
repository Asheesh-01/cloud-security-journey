# Day 57 — Process vs Thread

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 4 — Windows Processes and Services
**Subtopic:** 1 — Process vs Thread

---

## What It Is

Process: running program instance. Own memory, handles, security context.
Thread: unit of execution inside process. Own stack and register state.
Every process has at least one thread.
Threads within same process share address space and handles.

## Key Terms

**PID** — unique integer per running process. Reused after process exits.
Forensic: log PID may refer to different process than what runs now.

**PPID** — parent process identifier. Who created this process.
Parent-child relationships form the process tree.
Unexpected PPID = primary malware detection signal.

**Thread stack** — private memory per thread for local variables and return addresses.
Buffer overflow exploits target return addresses stored here.

**Handle** — reference to system resource tracked by kernel.
Handle leaks = eventual process crash.

**Process token** — security context: user, groups, privileges.
Privilege escalation modifies or impersonates tokens.

**Integrity level** — Untrusted < Low < Medium < High < System.
Standard user = Medium. Admin = High. System processes = System.
Low integrity: sandboxed (Edge browser). Cannot write to Medium locations.

## Attack Techniques

**Process hollowing**: create legitimate process suspended → empty memory
→ write malicious code → resume. Process list shows legitimate name.
Detection: executable path on disk vs actual memory contents differ.

**Process injection**: write code into another process's memory and execute.
Malicious code inherits legitimate process security token and identity.
Common methods: CreateRemoteThread, QueueUserAPC, SetWindowsHookEx.

**Thread hijacking**: suspend thread in legitimate process → modify instruction
pointer → resume. No new threads or processes created.

## Expected vs Suspicious Process Trees

Normal:
System(4) → smss.exe → wininit.exe → services.exe → svchost.exe
wininit.exe → lsass.exe
explorer.exe → user-launched applications

Suspicious parent-child pairs:
winword.exe → powershell.exe = malicious macro (extremely common)
lsass.exe → anything = critical finding (lsass never spawns children)
svchost.exe → cmd.exe = injection or malicious service
explorer.exe → process from AppData\Temp = suspicious user execution

## Commands (Windows 10 VM)

```cmd
tasklist /V
wmic process get name,processid,parentprocessid,executablepath /format:table
powershell "Get-WmiObject Win32_Process | Select-Object Name,ProcessId,ParentProcessId,ExecutablePath | Sort-Object ParentProcessId | Format-Table -AutoSize"
wmic process get name,processid,executablepath | findstr /V /I "system32\|syswow64\|program files\|windows"
tasklist /FI "MEMUSAGE gt 100000" /V
```