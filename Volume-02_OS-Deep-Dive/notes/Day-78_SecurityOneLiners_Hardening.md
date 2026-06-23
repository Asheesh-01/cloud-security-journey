# Day 78 — Security-Focused One-Liners + Linux Hardening

**Volume:** 02 — Operating Systems Deep Dive (FINAL)
**Topic:** 9.5 — Linux Command Line Mastery (CAPSTONE)
**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Part 1 — Security-Focused One-Liners

### One-Liner 1 — Brute Force Detection (Ranked Failed SSH Logins)

```bash
sudo journalctl _COMM=sshd | grep "Failed" | awk '{print $(NF-3)}' | sort | uniq -c | sort -rn | head -20
```

**Use:** Active SSH brute force attack detection. Top attacker IPs immediately visible.

**Output:** 250 192.168.1.100 (250 failed attempts from this IP)

**Action:** Block IP at firewall immediately.

---

### One-Liner 2 — Backdoor Detection (Suspicious SUID Binaries)

```bash
find / -perm -4000 -type f 2>/dev/null | grep -v "^/usr\|^/bin\|^/sbin\|^/lib"
```

**Use:** Find SUID binaries outside standard system paths. Attacker-planted backdoors.

**Output:** /tmp/updater, /home/user/.local/bin/helper

**Action:** Analyze each binary with file, strings, strace. Check malware databases.

---

### One-Liner 3 — Timeline Reconstruction (Recently Modified System Files)

```bash
find /etc /usr/sbin /usr/bin -mtime -7 -type f 2>/dev/null | head -20
```

**Use:** Attacker activity timeline. Which system files changed and when.

**Output:** /etc/passwd, /etc/shadow, /usr/bin/ncat (attacker-planted)

**Action:** Correlate file modification times with log entries to find exact attack window.

---

### One-Liner 4 — Persistence Detection (Unexpected Listening Ports)

```bash
ss -tulnp 2>/dev/null | grep LISTEN | awk '{print $4, $7}' | grep -v "127.0.0.1" | grep -v ":22\|:80\|:443\|:3306\|:5432"
```

**Use:** Find backdoor listening ports. Attacker communication channels.

**Output:** 0.0.0.0:4444 sshd/1234, 0.0.0.0:9999 unknown/5678

**Action:** Kill unknown processes. Investigate port ownership.

---

### One-Liner 5 — Hidden File Detection (Recently Modified Hidden Files)

```bash
find /home /root /tmp -name ".*" -mtime -7 2>/dev/null
```

**Use:** Find hidden files modified recently. Attacker stealth mechanisms.

**Output:** /home/user/.ssh/authorized_keys, /root/.bashrc, /tmp/.malware

**Action:** Check authorized_keys for unauthorized SSH keys. Check bashrc for backdoors.

---

### One-Liner 6 — Attack Pattern Analysis (Top Requesting IPs)

```bash
cat /var/log/apache2/access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head -20
```

**Use:** Web server scanning and brute force detection. Rank by request frequency.

**Output:** 5000 192.168.1.100 (5000 requests from single IP = scanning pattern)

**Action:** Correlate with HTTP status codes. Find successful attacks.

---

### One-Liner 7 — Rootkit Checking (Suspicious Kernel Modules)

```bash
ls -la /lib/modules/$(uname -r)/kernel/
```

**Use:** Detect kernel-level rootkits. Modules that hide attacker activity.

**Output:** persistence.ko (not standard kernel module)

**Action:** Run chkrootkit or rkhunter. Kernel module analysis required.

---

### One-Liner 8 — World-Writable Detection (Privilege Escalation Vectors)

```bash
find / -type f -perm -002 2>/dev/null | grep -v "/proc\|/sys\|/tmp\|/var/tmp"
```

**Use:** Find files anyone can modify. Privilege escalation vectors.

**Output:** /usr/bin/helper, /opt/script.sh

**Action:** Tighten permissions. If helper is SUID = critical finding.

---

## Part 2 — Linux Hardening with Lynis

### What Lynis Does
Security audit tool. Scans entire system for hardening opportunities. Generates actionable report with critical findings, warnings, and suggestions.

### Installation

```bash
sudo apt update
sudo apt install lynis -y
```

### Run Full Audit

```bash
sudo lynis audit system
```

Takes 2-5 minutes. Checks 200+ security aspects.

### Save Report for Analysis

```bash
sudo lynis audit system 2>&1 | tee ~/lynis_report.txt
```

### Implement Fixes (Examples)

**Disable insecure service:**
```bash
sudo systemctl stop telnetd
sudo systemctl disable telnetd
```

**Fix file permissions:**
```bash
sudo chmod 644 /var/log/auth.log
```

**Remove world-writable bit:**
```bash
sudo find /tmp -type d -perm -002 -exec chmod o-w {} \;
```

### Re-Audit to Verify

```bash
sudo lynis audit system
```

Compare new report with previous. Verify findings decreased.

---

## Complete 5-Minute Incident Response Flow

```bash
# 1. Detect active attack (brute force)
sudo journalctl _COMM=sshd | grep "Failed" | awk '{print $(NF-3)}' | sort | uniq -c | sort -rn | head -5

# 2. Find backdoors (SUID)
find / -perm -4000 -type f 2>/dev/null | grep -v "^/usr\|^/bin\|^/sbin\|^/lib"

# 3. Build timeline (modified files)
find /etc /usr/sbin /usr/bin -mtime -7 -type f 2>/dev/null

# 4. Find persistence (listening ports)
ss -tulnp | grep LISTEN | grep -v ":22\|:80\|:443"

# 5. Find hidden malware (hidden files)
find /home /root /tmp -name ".*" -mtime -7 2>/dev/null

# 6. Evidence preservation
tar -cvzf incident_evidence.tar.gz /var/log /tmp /home

# 7. Hash for chain of custody
sha256sum incident_evidence.tar.gz > evidence.sha256
```

60 seconds. Complete investigation.

---

## Security Angle — Real Incident Flow

Using these one-liners, SOC analysts routinely:
1. Detect attacks within minutes
2. Identify compromise scope
3. Find persistence mechanisms
4. Preserve evidence
5. Recommend containment

Without these commands, investigation takes days and evidence may be overwritten.