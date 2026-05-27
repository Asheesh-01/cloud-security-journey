# Day 64 — Labs: Windows Event Logging Channels

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — Open Event Viewer
```cmd
eventvwr.msc
```
- Graphical log browser
- Windows Logs section: Application, Security, System, Setup, Forwarded
- Double-click any event for full details including XML view

---

## Lab 2 — List All Log Channels
```cmd
wevtutil el
```
- Lists every available channel name
- Hundreds of channels on modern Windows
- `wevtutil el | find "Security"` — search for specific channel

---

## Lab 3 — Security Log Configuration
```cmd
wevtutil gl Security
```
- enabled: true/false
- maxSize: maximum size in bytes
- fileSize: current size
- numberOfLogRecords: events currently stored
- logFileName: path to .evtx file

---

## Lab 4 — Compare Classic Channels
```cmd
wevtutil gl Application
wevtutil gl System
```
- Compare size and record counts across channels
- Note which channel has most activity on this machine

---

## Lab 5 — Query Recent Security Events
```cmd
wevtutil qe Security /c:10 /rd:true /f:text
```
- `/c:10` — most recent 10 events
- `/rd:true` — newest first
- `/f:text` — human-readable format (vs default XML)

---

## Lab 6 — Check Audit Policy
```cmd
auditpol /get /category:*
```
- Shows all 53 audit subcategories
- "No Auditing" = events of that type not recorded
- Look for: Logon, Account Management, Process Creation settings

---

## Lab 7 — Security Log Key Fields
```cmd
wevtutil gl Security | findstr /I "logFileName\|maxSize\|fileSize\|numberOfLogRecords"
```
- Filtered view of most important log configuration fields
- maxSize in bytes — divide by 1048576 for MB

---

## Lab 8 — Export Security Log
```cmd
wevtutil epl Security C:\Users\Public\security_log_backup.evtx
del C:\Users\Public\security_log_backup.evtx
```
- Exports entire Security log to .evtx file
- In real investigations: export before system shutdown or log clearing
- Delete backup after lab — cleanup