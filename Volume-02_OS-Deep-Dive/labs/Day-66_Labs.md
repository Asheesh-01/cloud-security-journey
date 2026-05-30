# Day 66 — Labs: Event ID 4625

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — Recent Failed Logons
```cmd
wevtutil qe Security /q:"*[System[EventID=4625]]" /c:10 /rd:true /f:text
```
- 10 most recent failed logon events
- Review: account name, status code, source IP, logon type

---

## Lab 2 — Generate Failed Logon Events
```cmd
net use \\localhost\IPC$ /user:nonexistentuser wrongpassword 2>nul
net use \\localhost\IPC$ /user:%USERNAME% wrongpassword 2>nul
```
- First: nonexistent username → Status 0xC0000064
- Second: valid username, wrong password → Status 0xC000006A
- Compare the two status codes in the resulting 4625 events

---

## Lab 3 — Verify Generated Events
```cmd
wevtutil qe Security /q:"*[System[EventID=4625]]" /c:5 /rd:true /f:text
```
- Confirms both 4625 events were generated
- Read Status field — two different codes for two different failure types

---

## Lab 4 — Failed Logons Last 30 Minutes
```cmd
powershell "
$start = (Get-Date).AddMinutes(-30)
$events = Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625; StartTime=$start} -ErrorAction SilentlyContinue
if ($events) {
    Write-Output \"Failed logons in last 30 minutes: $($events.Count)\"
    $events | ForEach-Object {
        [PSCustomObject]@{Time=$_.TimeCreated; User=$_.Properties[5].Value; IP=$_.Properties[19].Value}
    } | Format-Table -AutoSize
} else { Write-Output 'No failures in last 30 minutes' }
"
```
- Time-windowed failure query
- Shows user and source IP per event

---

## Lab 5 — Spray Detection Pattern
```cmd
powershell "
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625} -MaxEvents 200 -ErrorAction SilentlyContinue |
ForEach-Object { [PSCustomObject]@{User=$_.Properties[5].Value; IP=$_.Properties[19].Value} } |
Group-Object IP | Where-Object {$_.Count -gt 3} |
ForEach-Object {
    $u = ($_.Group | Select-Object -ExpandProperty User -Unique).Count
    [PSCustomObject]@{IP=$_.Name; Failures=$_.Count; UniqueAccounts=$u}
} | Sort-Object Failures -Descending | Format-Table -AutoSize
"
```
- Groups failures by source IP
- Shows unique account count per IP
- High unique accounts from one IP = password spray pattern

---

## Lab 6 — Account Lockout Events
```cmd
wevtutil qe Security /q:"*[System[EventID=4740]]" /c:10 /rd:true /f:text
```
- Event ID 4740 = Account Locked Out
- Generated after consecutive 4625 failures exceed threshold
- Contains locked account name and caller machine

---

## Lab 7 — Failure Code Distribution
```cmd
powershell "
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625} -MaxEvents 50 -ErrorAction SilentlyContinue |
ForEach-Object { [PSCustomObject]@{Status=$_.Properties[7].Value.ToString('X8'); User=$_.Properties[5].Value} } |
Group-Object Status | Select-Object Name,Count | Sort-Object Count -Descending | Format-Table -AutoSize
"
```
- Groups by Status hex code
- C000006A dominant = valid usernames, password guessing
- C0000064 dominant = still enumerating usernames

---

## Lab 8 — Combined 4625 and 4624 Correlation
```cmd
powershell "
$failures = (Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625} -MaxEvents 100 -ErrorAction SilentlyContinue).Count
$successes = (Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4624} -MaxEvents 100 -ErrorAction SilentlyContinue).Count
Write-Output \"Last 100 logon events: $failures failures, $successes successes\"
Write-Output \"Failure rate: $([math]::Round($failures/($failures+$successes)*100,1))%\"
"
```
- Calculates failure rate from recent events
- High failure rate = brute force or credential stuffing in progress
- Low failure rate with all successes = normal operation