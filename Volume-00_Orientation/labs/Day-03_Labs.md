# Day 03 — Labs

**Topic:** Career Roles & Goal Setting
**Environment:** Kali Linux VM

---

## Lab 1 — Check who is logged in

**Commands:**
```bash
who
w
last | head -20
```

**What who does:**
Shows who is currently logged into the system.
Each line shows username, terminal, login time, IP if remote.

**What w does:**
More detailed than who. Shows who is logged in AND what
they are currently doing. Shows CPU usage per user.
A SOC analyst uses this to check for unauthorized active sessions.

**What last | head -20 does:**
last reads from /var/log/wtmp — file that stores all login
history on the system.
| head -20 limits output to first 20 lines.
Shows username, terminal, IP address, date and time of login.

**What the output means:**
This is exactly what a SOC Tier 1 analyst does when
investigating suspicious activity — check who logged in,
when, and from where. Unauthorized login at 3AM from
unknown IP = incident. Open a ticket. Escalate.

**Screenshot:** saved in artifacts/Day-03/

---

## Lab 2 — Check open network ports

**Command:**
```bash
netstat -tulnp 2>/dev/null
```

**What this command does:**
netstat — network statistics tool
-t — show TCP connections
-u — show UDP connections
-l — show only listening ports (services waiting for connections)
-n — show IP addresses not hostnames (faster, more precise)
-p — show which process owns each connection
2>/dev/null — hide permission errors

**What the output means:**
Each line is one open port — a potential entry point for
an attacker. Cloud Security Engineer reviews open ports
to make sure nothing unexpected is exposed.
Empty output = no services running = clean machine.
On production server = 10 to 20 lines expected.
Unexpected open port = potential compromise or misconfiguration.

**Screenshot:** saved in artifacts/Day-03/

---

## Lab 3 — Check running processes

**Commands:**
```bash
ps aux | grep -i security
ps aux | head -20
```

**What ps aux does:**
ps — process status. Shows all running processes.
a — show processes from all users
u — show detailed user information
x — show processes not attached to a terminal

**What grep -i security does:**
Filters output to show only lines containing word security.
-i means case insensitive — finds Security, SECURITY, security.

**What ps aux | head -20 does:**
Shows first 20 processes running on the system.
Each line shows user, PID (process ID), CPU%, memory%,
start time, and the command that started the process.

**What the output means:**
Cloud security engineer checks running processes to verify
security tools are active and spot unauthorized processes.
Unknown process consuming high CPU or RAM = investigate immediately.

**Screenshot:** saved in artifacts/Day-03/

---

## Lab 4 — Read authentication logs

**Commands:**
```bash
cat /var/log/auth.log 2>/dev/null | tail -20
journalctl -n 20 2>/dev/null
```

**What cat /var/log/auth.log does:**
cat — reads and prints file contents
/var/log/auth.log — authentication log file
Records every login attempt, sudo usage, SSH attempt,
and authentication event on the system.
tail -20 — shows last 20 lines (most recent events)

**What journalctl -n 20 does:**
journalctl — log viewer for modern Linux systems using systemd
-n 20 — show last 20 log entries
Shows system events, service starts, errors, security events.

**What the output means:**
Every line is one security event. Detection engineers write
rules on top of these logs. Example — if same IP has 10
failed logins in 60 seconds, fire brute force alert.
You build exactly this detector in Volume 04 mini project.

**Screenshot:** saved in artifacts/Day-03/

---

## Lab 5 — Scan localhost with nmap

**Command:**
```bash
nmap -sV localhost
```

**What this command does:**
nmap — network mapper, industry standard scanning tool
-sV — service version detection. Identifies exactly what
software and version runs on each open port.
localhost — scan your own machine only.
Never scan systems you do not own — illegal under IT Act 2000.

**What the output means:**
Each line shows open port, protocol, service, and version.
Penetration tester uses this to find which services run
and checks CVE database for known vulnerabilities in
that exact version. You use this in Volume 09 Project 2.

**Screenshot:** saved in artifacts/Day-03/

---

## Lab 6 — System health check

**Commands:**
```bash
df -h
free -h
uptime
```

**What df -h does:**
df — disk free. Shows disk space usage per partition.
-h — human readable format (GB, MB instead of bytes)

**What free -h does:**
Shows RAM usage — used, free, and cached memory.
-h — human readable format.

**What uptime does:**
Shows how long system has been running, number of users
logged in, and load average for last 1, 5, 15 minutes.
Load average above 1.0 per CPU core = high resource usage.

**What the output means:**
Incident responder runs these immediately on compromised machine.
Disk full = attacker possibly storing data before exfiltration.
High RAM = malware possibly running in memory.
Unexpected reboot time = system was restarted suspiciously.
High load = something consuming heavy resources, possibly malware.

**Screenshot:** saved in artifacts/Day-03/

---

## Lab 7 — Create goals file

**Commands:**
```bash
echo "Phase 1 - SOC Intern - 6 months" > ~/goals.txt
echo "Phase 2 - Cloud Security Engineer - 14 months" >> ~/goals.txt
echo "Phase 3 - Mid Level - 3 years" >> ~/goals.txt
cat ~/goals.txt
```

**What echo does:**
Prints text to screen or redirects to a file.

**What > does:**
Redirect operator. Sends output to file.
Creates file if not exists. Overwrites if exists.

**What >> does:**
Append operator. Adds to end of file without overwriting.

**What cat ~/goals.txt does:**
Reads and prints the file just created.
~/ is shortcut for home directory.

**What the output means:**
You just created a file, wrote to it, appended to it,
and read it back. This exact pattern is used constantly
in security scripting — saving log outputs, generating
reports, writing alert data to files.

**Screenshot:** saved in artifacts/Day-03/