# Day 65 — Labs: Event ID 4624

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — Recent Successful Logons
```cmd
wevtutil qe Security /q:"*[System[EventID=4624]]" /c:10 /rd:true /f:text
```
- Returns 10 most recent 4624 events in readable format
- Scan for: account names, logon types, source IPs

---

## Lab 2 — Interactive Logons Only
```cmd
wevtutil qe Security /q:"*[System[EventID=4624] and EventData[Data[@Name='LogonType']='2']]" /c:5 /rd:true /f:text
```
- LogonType=2 filter: only physical/console logons
- Shows who sat at the keyboard and when

---

## Lab 3 — RDP Logons
```cmd
wevtutil qe Security /q:"*[System[EventID=4624] and EventData[Data[@Name='LogonType']='10']]" /c:5 /rd:true /f:text
```
- LogonType=10: Remote Desktop connections only
- IpAddress field shows source of each RDP connection

---

## Lab 4 — Service Logons
```cmd
wevtutil qe Security /q:"*[System[EventID=4624] and EventData[Data[@Name='LogonType']='5']]" /c:5 /rd:true /f:text
```
- LogonType=5: service account logons
- Compare account names against known service accounts

---

## Lab 5 — Full XML View
```cmd
wevtutil qe Security /q:"*[System[EventID=4624]]" /c:3 /rd:true /f:xml
```
- XML format shows every field name and value
- Identify: SubjectUserName, TargetUserName, LogonType,
  IpAddress, AuthenticationPackageName, WorkstationName

---

## Lab 6 — Generate Network Logon Event
```cmd
net use \\localhost\C$ /user:%USERNAME% ""
net use \\localhost\C$ /delete 2>nul
wevtutil qe Security /q:"*[System[EventID=4624] and EventData[Data[@Name='LogonType']='3']]" /c:3 /rd:true /f:text
```
- Creates a Type 3 network logon to localhost
- Then immediately checks if 4624 Type 3 was generated
- Demonstrates event generation from known action

---

## Lab 7 — Last Hour Logons via PowerShell
```cmd
powershell "Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4624; StartTime=(Get-Date).AddHours(-1)} | Select-Object TimeCreated,Message | Format-List"
```
- Queries only events from last 1 hour
- `AddHours(-1)` — change number for different time window
- Format-List — shows each event with full message text

---

## Lab 8 — Logon Type Distribution
```cmd
powershell "Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4624} -MaxEvents 100 | ForEach-Object { [PSCustomObject]@{Type=$_.Properties[8].Value; User=$_.Properties[5].Value} } | Group-Object Type | Select-Object Name,Count | Sort-Object Count -Descending"
```
- Gets last 100 logon events
- Groups by logon type with count
- Shows frequency distribution of logon types on this machine