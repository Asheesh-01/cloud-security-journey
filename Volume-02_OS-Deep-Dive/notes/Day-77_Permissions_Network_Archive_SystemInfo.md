# Day 77 — Permissions + Network + Archiving + System Info

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 9.5 — Linux Command Line Mastery
**Subtopics:** 9 — Permissions | 10 — Network | 11 — Archiving | 12 — System Info
**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Subtopic 9 — Permissions Quick Check

### What It Is
Determine current user privileges, sudo access, group membership, ability to access system files.

### Key Commands

**whoami**
- Prints current username
- Output: `krishna`
- Use: Confirm identity before investigation

**id**
- Prints UID, GID, and all group memberships
- Output: `uid=1000(krishna) gid=1000(krishna) groups=1000(krishna),4(adm),27(sudo),142(docker)`
- Use: Detailed privilege assessment. Look for docker group = root equivalent.

**groups**
- Prints groups in shorter format
- Output: `krishna adm cdrom sudo dip plugdev lpadmin lxd docker`
- Use: Quick group check. Look for sudo = can run commands as root.

**sudo whoami**
- Runs whoami as root
- Requires password unless passwordless sudo configured
- Output: `root`
- Use: Confirm sudo works and password accepted

**sudo -l**
- Lists commands you can run with sudo
- Output: `(ALL) ALL` = unrestricted sudo access
- Use: Check for privilege escalation opportunities

**cat /etc/sudoers**
- Shows sudoers configuration (who can sudo what)
- Use: Audit sudo privileges. Never edit directly — use visudo.

**su -**
- Switch to root user
- Requires root password
- Output: Prompt changes to `#`
- Use: On test systems only, not production

### Security Angle
Docker group membership allows privilege escalation. Users in docker group can mount host filesystem and get root shell. Sudo misconfiguration (NOPASSWD entries) enables lateral movement.

---

## Subtopic 10 — Network Quick Commands

### What It Is
Check connectivity, identify open ports, discover active connections, detect C2 beaconing.

### Key Commands

**ping -c 4 8.8.8.8**
- Sends 4 ICMP echo requests to target IP
- Output: 4 received, 0% packet loss = connectivity confirmed
- Use: Test if target reachable. If fails = server isolated or firewalled.
- Modern: Same as netstat legacy command

**curl -I https://google.com**
- HEAD request to URL (fetch headers only, no content)
- Output: HTTP/2 200 = server reachable
- Use: Test HTTPS connectivity when ICMP blocked
- Modern alternative to ping when firewall blocks ICMP

**ss -tulnp**
- Shows all listening TCP and UDP sockets
- `-t` TCP, `-u` UDP, `-l` listening, `-n` numeric, `-p` process
- Output: port, state, process name and PID
- Use: Identify all open ports and their owning processes. Unexpected port = backdoor.
- **Modern replacement for netstat. Kali prefers ss.**

**ss -an | grep ESTABLISHED**
- Shows all active connections
- Output: source IP:port, destination IP:port
- Use: Find what external IPs server is talking to. Unexpected IP = C2 communication.

### Security Angle
Beaconing pattern detection: same external IP contacted repeatedly every 60 seconds = C2 check-in. ss -an reveals this pattern instantly.

---

## Subtopic 11 — Archiving and Compression

### What It Is
Combine multiple files into archive. Reduce size with compression. Preserve evidence for chain of custody.

### Key Commands

**tar -cvf logs.tar ~/investigation/logs/**
- Create archive from directory
- `-c` create, `-v` verbose, `-f filename`
- Output: 500MB uncompressed archive
- Use: Bundle files for preservation before compression

**tar -cvzf logs.tar.gz ~/investigation/logs/**
- Create compressed archive using gzip
- `-z` adds gzip compression
- Output: 45MB compressed archive (90% reduction)
- Use: **Preferred method.** Faster transfer, smaller storage.

**tar -xvzf logs.tar.gz**
- Extract compressed archive
- `-x` extract, `-v` verbose, `-z` decompress
- Output: All original files restored to directory structure
- Use: Decompress evidence for analysis

**zip -r evidence.zip ~/investigation/**
- Create zip archive with compression
- `-r` recursive
- Output: Windows-compatible zip file
- Use: When evidence shared with Windows-based teams

**unzip evidence.zip**
- Extract zip archive
- Output: All files restored
- Use: Decompress zip files

### Legacy Alternative
```bash
tar -cvf logs.tar ~/logs/ && gzip logs.tar
```
Two-step process (create then compress separately). Modern practice uses `-z` in tar (one step).

### Security Angle
Chain of custody: calculate SHA256 hash of archive before and after transfer. Matching hash proves no tampering during transfer.

---

## Subtopic 12 — System Info Commands

### What It Is
Understand system baseline: kernel version, uptime, hostname, time, environment variables. Detect anomalies indicating compromise.

### Key Commands

**uname -a**
- Prints kernel name, version, processor architecture, build info
- Output: `Linux kali 6.6.9-amd64 x86_64 GNU/Linux`
- Use: Check kernel version against known vulnerabilities. Old kernel = exploitable.

**hostnamectl**
- Modern replacement for uname. Shows additional OS details
- Output: hostname, OS, kernel, architecture
- Use: Detailed system identification
- **Modern command. Both work on Kali.**

**uptime**
- How long since last reboot and CPU load average
- Output: `up 45 days, 3:21; load average: 0.45, 0.52, 0.38`
- Use: Detect recent reboot = possible cleanup by attacker. High load = attack ongoing or system overloaded.

**hostname**
- Current hostname
- Output: `kali`
- Use: Verify correct server during SSH session

**date**
- Current system date and time
- Output: `Thu Jun 18 14:32:15 UTC 2026`
- Use: Timeline reconstruction. If wrong = logs unreliable. Attackers change time to evade detection.

**env**
- All environment variables
- Output: SHELL, USER, HOME, PATH, LANG, etc.
- Use: Check PATH for writable directories (privilege escalation vector)

**echo $PATH**
- Print PATH variable only
- Output: `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin`
- Use: Verify /tmp not in PATH. If present and first = command hijacking vulnerability.

### Security Angle
Uptime detection: system rebooted recently = attacker cleaned up. Correlate reboot time with suspicious activity in logs to narrow investigation window.

---

## Complete Investigation Flow — One Session

```bash
# 1. Identify yourself
whoami
id
sudo -l

# 2. Check network state
ss -an | grep ESTABLISHED | grep -v 127.0.0.1

# 3. Check listening ports
ss -tulnp

# 4. Check system baseline
uname -a
uptime
date

# 5. Archive evidence
tar -cvzf incident_evidence.tar.gz /var/log /home /tmp

# 6. Hash evidence for chain of custody
sha256sum incident_evidence.tar.gz > evidence.sha256
```

60 seconds. Complete system assessment and evidence preservation.