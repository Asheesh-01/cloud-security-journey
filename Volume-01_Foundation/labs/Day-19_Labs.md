# Day 19 — Labs
# Topic 5: Windows OS Basics — Subtopic 6: Windows Event Logging

---

## Lab Environment
- Host: Ubuntu 24.04 — Acer Swift Go 14
- VM: Windows 10 inside VirtualBox
- Tool: Command Prompt (Administrator) + Event Viewer GUI
- Note: Screenshots uploaded to GitHub as proof

---

## Lab 1 — Event Viewer GUI Exploration

### Command:
```cmd
eventvwr.msc
```

### What was done:
- Opened Event Viewer
- Navigated to Windows Logs → Security
- Observed event list with Event IDs, timestamps, and sources
- Navigated to Windows Logs → System
- Observed service start/stop events

---

## Lab 2 — Security Log Information

### Command:
```cmd
wevtutil gli Security
```

### What this shows:
- Total number of records in Security log
- File size of Security.evtx
- Creation time, last write time
- Oldest and newest event timestamps
- Whether log is enabled

---

## Lab 3 — Query Last 20 Security Events

### Command:
```cmd
wevtutil qe Security /c:20 /rd:true /f:text
```

### What was observed:
- Last 20 events from Security log displayed as plain text
- Each event shows: Log Name, Source, Date, Event ID, Task Category, Level, Keywords, User, Computer, Description
- Mix of system-generated security events visible

---

## Lab 4 — Filter for Successful Logins (4624)

### Command:
```cmd
wevtutil qe Security /c:10 /rd:true /f:text /q:"*[System[EventID=4624]]"
```

### What was observed:
- Last 10 successful login events
- Account names, logon types, and source information visible
- Logon Type 2 (interactive) and Logon Type 5 (service) most common on idle machine

---

## Lab 5 — Filter for Failed Logins (4625)

### Command:
```cmd
wevtutil qe Security /c:10 /rd:true /f:text /q:"*[System[EventID=4625]]"
```

### What was observed:
- Failed login events in Security log
- Sub Status codes visible showing reason for failure

---

## Lab 6 — Filter for Explicit Credential Use (4648)

### Command:
```cmd
wevtutil qe Security /c:5 /rd:true /f:text /q:"*[System[EventID=4648]]"
```

### What was observed:
- Events showing explicit credential use
- Subject account and target account visible in each event

---

## Lab 7 — System Log Query

### Command:
```cmd
wevtutil qe System /c:20 /rd:true /f:text
```

### What was observed:
- System events — service control manager events
- Hardware and driver events
- System startup events

---

## Lab 8 — List All Log Channels

### Command:
```cmd
wevtutil el
```

### What was observed:
- Hundreds of log channels listed
- Security, System, Application visible
- Microsoft-Windows-PowerShell, Microsoft-Windows-TaskScheduler also present
- Every Windows component has its own dedicated log channel

---

## Lab 9 — Export Events to File

### Command:
```cmd
wevtutil qe Security /c:50 /rd:true /f:text > C:\Users\Public\security_events.txt
type C:\Users\Public\security_events.txt
```

### What was done:
- Last 50 Security events exported to text file
- File read back and displayed
- Simulates how investigators export logs for offline analysis

---

## Lab 10 — Simulating a Failed Login to Generate 4625

### Command:
```cmd
runas /user:fakeuser123 cmd
```

### What was done:
- Attempted to run cmd as non-existent account fakeuser123
- Entered wrong password when prompted
- Command failed as expected
- Re-ran Lab 5 filter and confirmed new 4625 event appeared in Security log
- Proved that every failed login attempt is recorded immediately

---

## Lab 11 — Cleanup

### Command:
```cmd
del C:\Users\Public\security_events.txt
```

---

## Key Findings From Today's Labs

1. Security log captures every login attempt — successful and failed — in real time
2. wevtutil filters by Event ID using XPath — essential skill for investigation
3. Generated own 4625 event and confirmed it appeared in Security log within seconds
4. Hundreds of log channels exist — Security is the primary investigation starting point
5. Events export cleanly to text files — usable for sharing with team or ingesting into SIEM

---

*Volume 01 — Topic 5 — Subtopic 6 | Day 19 | Exam Season*
*Commit: Day 19 - Windows Event Logging - wevtutil lab, 4624 4625 4648 investigation*
*Screenshots uploaded to GitHub artifacts folder*