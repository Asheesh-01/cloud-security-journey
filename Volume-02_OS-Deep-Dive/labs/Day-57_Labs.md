# Day 57 — Labs: Process vs Thread

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — List All Processes Verbose
```cmd
tasklist /V
```
- `/V` — verbose: adds username, CPU time, memory, window title
- Baseline view of all running processes with security context

---

## Lab 2 — Process Tree Analysis
```cmd
wmic process get name,processid,parentprocessid,executablepath /format:table
```
- Shows name, PID, PPID, and full executable path for every process
- Sort mentally by PPID to read parent-child relationships
- Empty executable path = possible process hollowing indicator

---

## Lab 3 — PowerShell Process Tree
```cmd
powershell "Get-WmiObject Win32_Process | Select-Object Name,ProcessId,ParentProcessId,ExecutablePath | Sort-Object ParentProcessId | Format-Table -AutoSize"
```
- `Sort-Object ParentProcessId` — groups children under same parent
- `ExecutablePath` — full disk path to the binary
- Compare paths against expected locations

---

## Lab 4 — Find Processes Outside Expected Locations
```cmd
wmic process get name,processid,executablepath | findstr /V /I "system32\|syswow64\|program files\|windows"
```
- Excludes standard Windows locations
- Any result = process running from unexpected location
- Investigate: legitimate third-party software or malware

---

## Lab 5 — Analyze explorer.exe Threads
```cmd
powershell "
$proc = Get-Process -Name 'explorer' | Select-Object -First 1
Write-Output \"PID: $($proc.Id) Threads: $($proc.Threads.Count)\"
$proc.Threads | Select-Object -First 10 | Format-Table Id,ThreadState -AutoSize
"
```
- Shows thread count and first 10 threads for explorer.exe
- Abnormally high thread count = possible injection

---

## Lab 6 — High Memory Usage Processes
```cmd
tasklist /FI "MEMUSAGE gt 100000" /V
```
- Filters to processes using more than 100MB RAM
- High memory + unexpected parent = injection indicator
- Compare against known baseline for each process

---

## Lab 7 — Find cmd.exe Parent Process
```cmd
powershell "
$proc = Get-WmiObject Win32_Process -Filter 'Name=""cmd.exe""'
foreach ($p in $proc) {
    $parent = Get-WmiObject Win32_Process -Filter ""ProcessId=$($p.ParentProcessId)""
    Write-Output \"cmd.exe PID $($p.ProcessId) parent: $($parent.Name)\"
}
"
```
- Finds all cmd.exe instances and their parent process names
- Expected parent: explorer.exe (user opened it) or cmd.exe (nested)
- Unexpected parent: any service process or document application