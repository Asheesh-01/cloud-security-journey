# Day 58 — Labs: Windows Services

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — List All Services
```cmd
sc query type= all state= all
```
- Lists every service and driver with current state
- TYPE: 1=kernel driver, 10=own process, 20=shared process
- STATE: 1=stopped, 4=running

---

## Lab 2 — Services with Binary Paths
```cmd
wmic service get name,pathname,startmode,state /format:table
```
- Most important service audit command
- pathname = full binary path = what actually runs
- startmode = Auto/Manual/Disabled
- Review every pathname for unexpected locations

---

## Lab 3 — Services Outside Expected Locations
```cmd
wmic service get name,pathname | findstr /V /I "system32\|syswow64\|program files\|windows\|microsoft"
```
- Excludes standard Windows locations
- Each result requires investigation
- Legitimate third-party software appears here too

---

## Lab 4 — Over-Privileged Services
```cmd
wmic service get name,startname,pathname | findstr /I "localsystem"
```
- Shows all LocalSystem services
- Services not needing full machine access but running as LocalSystem
- Each is a candidate for least-privilege remediation

---

## Lab 5 — Detailed Service Configuration
```cmd
sc qc wuauserv
sc qc Spooler
```
- BINARY_PATH_NAME: where the executable lives
- SERVICE_START_NAME: what account it runs as
- DEPENDENCIES: what other services must be running first

---

## Lab 6 — Service Registry Entry
```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Services\wuauserv"
```
- Authoritative source for service configuration
- ImagePath: binary path. ObjectName: account. Start: start type number.
- Start values: 0=Boot, 1=System, 2=Auto, 3=Manual, 4=Disabled

---

## Lab 7 — Unquoted Service Paths
```cmd
wmic service get name,pathname | findstr /I /V """" | findstr /I " "
```
- First filter: removes properly quoted paths
- Second filter: keeps paths containing spaces
- Results: unquoted paths with spaces = privilege escalation candidates

---

## Lab 8 — Simulate Service Persistence
```cmd
sc create TestPersistence binPath= "C:\Windows\Temp\test.exe" start= auto DisplayName= "Windows Test Service"
sc query TestPersistence
reg query "HKLM\SYSTEM\CurrentControlSet\Services\TestPersistence"
sc delete TestPersistence
```
- Creates a service registration — one command, no UAC prompt needed
- Verifies registry entry was written automatically
- Demonstrates how trivial service persistence installation is
- sc delete removes it cleanly — always clean up simulation labs