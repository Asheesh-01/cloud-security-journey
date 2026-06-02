# Day 67 — Labs: Event ID 4648

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — Query 4648 Events
```cmd
wevtutil qe Security /q:"*[System[EventID=4648]]" /c:10 /rd:true /f:text
```
- Returns 10 most recent explicit credential logon events
- Key fields: Subject account, credentials used, target server, process name

---

## Lab 2 — Generate a 4648 Event
```cmd
runas /user:%COMPUTERNAME%\Administrator "cmd.exe /c whoami"
```
- RunAs generates 4648 when attempting explicit credential use
- Type wrong password or press Ctrl+C to cancel safely
- Even a cancelled or failed attempt may generate the event

---

## Lab 3 — Verify 4648 Was Generated
```cmd
wevtutil qe Security /q:"*[System[EventID=4648]]" /c:5 /rd:true /f:text
```
- Confirms the RunAs attempt generated a 4648 event
- Subject = your current account
- Credentials Used = Administrator account
- Process = runas.exe

---

## Lab 4 — Full XML View of 4648
```cmd
wevtutil qe Security /q:"*[System[EventID=4648]]" /c:3 /rd:true /f:xml
```
- XML shows all field names and values
- SubjectUserName, TargetUserName, TargetServerName, ProcessName all visible

---

## Lab 5 — Filter Key Fields Only
```cmd
wevtutil qe Security /q:"*[System[EventID=4648]]" /c:20 /rd:true /f:text | findstr /I "Target Server\|Account Name\|Process Name"
```
- Filters verbose output to three most relevant fields
- Quick scan for suspicious patterns across multiple events

---

## Lab 6 — Correlate 4648 with 4624
```cmd
powershell "
$e4648 = Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4648} -MaxEvents 5 -ErrorAction SilentlyContinue
$e4624 = Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4624} -MaxEvents 5 -ErrorAction SilentlyContinue
Write-Output '=== 4648 Explicit Credential Events ==='
$e4648 | ForEach-Object { Write-Output \"$($_.TimeCreated) | $($_.Properties[1].Value) used $($_.Properties[6].Value) creds\" }
Write-Output '=== 4624 Successful Logon Events ==='
$e4624 | ForEach-Object { Write-Output \"$($_.TimeCreated) | $($_.Properties[5].Value) logged in | Type $($_.Properties[8].Value)\" }
"
```
- Shows both event types side by side
- Match timestamps to correlate source activity with destination logon

---

## Lab 7 — Hourly 4648 Distribution
```cmd
powershell "
$events = Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4648; StartTime=(Get-Date).AddHours(-24)} -ErrorAction SilentlyContinue
if ($events) {
    $events | Group-Object {($_.TimeCreated).Hour} | Sort-Object Name |
    ForEach-Object { Write-Output \"Hour $($_.Name):00 - $($_.Count) events\" }
} else { Write-Output 'No 4648 events in last 24 hours' }
"
```
- Shows 4648 distribution across hours of the day
- Spike during off-hours = suspicious lateral movement

---

## Lab 8 — Three Event ID Summary
```cmd
powershell "
@(4624, 4625, 4648) | ForEach-Object {
    $id = $_
    $count = (Get-WinEvent -FilterHashtable @{LogName='Security'; Id=$id} -MaxEvents 1000 -ErrorAction SilentlyContinue).Count
    Write-Output \"Event $id : $count events\"
}
"
```
- Quick count of all three authentication Event IDs
- Baseline picture of authentication activity on this machine