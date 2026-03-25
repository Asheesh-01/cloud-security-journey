# Day 17 — Labs

**Topic:** Windows OS Basics — Subtopic 4: Windows Services
**Environment:** Windows 10 VM (VirtualBox)
**Opened as:** Administrator (right-click cmd → Run as Administrator)

---

## Lab 1 — List All Services

**Command:**
```cmd
sc query type= all state= all
```

**What each part does:**
- `sc` — Service Control command line tool. Communicates directly with SCM.
- `query` — retrieves service status information
- `type= all` — includes Win32 services and driver services
- `state= all` — includes running, stopped, and paused services

**Paste output here after running.**

---

## Lab 2 — List Only Running Services

**Command:**
```cmd
sc query state= active
```

**What each part does:**
- `state= active` — filters to only currently running services
- Shorter output than Lab 1 — only what is live right now

**Paste output here after running.**

---

## Lab 3 — Inspect Windows Update Service Configuration

**Command:**
```cmd
sc qc wuauserv
```

**What each part does:**
- `qc` — Query Configuration. Shows the full configuration of one service.
- `wuauserv` — the internal name of the Windows Update service

**What to look for in output:**
- BINARY_PATH_NAME — where the executable lives
- START_TYPE — Automatic, Manual, or Disabled
- SERVICE_START_NAME — which account it runs as

**Paste output here after running.**

---

## Lab 4 — Inspect Windows Defender Service

**Command:**
```cmd
sc qc WinDefend
```

**What each part does:**
- Same structure as Lab 3
- `WinDefend` — internal name for Windows Defender Antivirus service

**Paste output here after running.**

---

## Lab 5 — Check Registry Entry for a Service

**Command:**
```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Services\wuauserv"
```

**What each part does:**
- `reg query` — reads a registry key and displays its values
- The path is where Windows stores all service configuration
- Every service has its own subkey here

**What to look for:**
- ImagePath — binary path that SCM uses to start the service
- Start — 2=Automatic, 3=Manual, 4=Disabled
- ObjectName — the account the service runs as

**Paste output here after running.**

---

## Lab 6 — List All Services With Binary Paths

**Command:**
```cmd
wmic service get name,pathname,startmode,state
```

**What each part does:**
- `wmic` — Windows Management Instrumentation Command line
- `service get` — queries the service class
- `name,pathname,startmode,state` — the four columns to display

**Security use:** this command shows every service binary path in one view.
During investigation this is the first thing you run to find services with
unexpected paths.

**Paste output here after running.**

---

## Lab 7 — Find Services Outside System32 and Program Files

**Command:**
```cmd
wmic service get name,pathname | findstr /v /i "system32\|program files"
```

**What each part does:**
- `wmic service get name,pathname` — lists all services and their paths
- `|` — pipe. Sends output to findstr
- `findstr /v /i` — /v inverts the match (show lines that do NOT match),
  /i makes it case-insensitive
- `"system32\|program files"` — exclude lines containing either string
- Result: only services whose binary does NOT live in System32 or Program Files

**Security use:** any result here is worth investigating. Legitimate services
occasionally appear (some third-party software), but any unfamiliar service
with a path in Temp, AppData, or Public is a finding.

**Paste output here after running.**

---

## Lab 8 — Check svchost.exe Parent Processes

**Command:**
```cmd
powershell "Get-WmiObject Win32_Process -Filter 'Name=""svchost.exe""' | Select-Object ProcessId,ParentProcessId,CommandLine | Format-Table -AutoSize"
```

**What each part does:**
- `powershell` — launches PowerShell to run this command from cmd
- `Get-WmiObject Win32_Process` — queries all running processes via WMI
- `-Filter 'Name="svchost.exe"'` — filters to only svchost.exe instances
- `Select-Object` — picks which columns to display
- `ProcessId` — PID of each svchost instance
- `ParentProcessId` — PID of its parent (should always be services.exe PID)
- `CommandLine` — the full command used to start it (should contain -k GroupName)

**What to verify:**
- All ParentProcessId values should match the PID of services.exe
- All CommandLine entries should contain -k followed by a group name
- Any svchost.exe with a different parent PID is malicious

**Paste output here after running.**

---

## Lab Summary Table

| Lab | Command | Security Purpose |
|-----|---------|-----------------|
| 1 | sc query type= all state= all | Full service inventory |
| 2 | sc query state= active | What is running right now |
| 3 | sc qc wuauserv | Configuration of one service |
| 4 | sc qc WinDefend | Verify Defender service is healthy |
| 5 | reg query Services\wuauserv | Raw registry entry for service |
| 6 | wmic service get name,pathname | All service paths in one view |
| 7 | wmic + findstr | Services outside expected locations |
| 8 | PowerShell Get-WmiObject | Verify all svchost parents = services.exe |