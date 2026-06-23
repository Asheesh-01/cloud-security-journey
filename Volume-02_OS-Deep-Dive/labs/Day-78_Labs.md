# Day 78 — Labs: Security One-Liners + Hardening

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Lab 1 — Run All Security One-Liners

```bash
# Brute force detection
sudo journalctl _COMM=sshd | grep "Failed" | awk '{print $(NF-3)}' | sort | uniq -c | sort -rn | head -10

# SUID backdoor detection
find / -perm -4000 -type f 2>/dev/null | grep -v "^/usr\|^/bin\|^/sbin\|^/lib"

# Recently modified system files
find /etc /usr/sbin /usr/bin -mtime -7 -type f 2>/dev/null | head -20

# Unexpected listening ports
ss -tulnp 2>/dev/null | grep LISTEN | grep -v ":22\|:80\|:443"

# Hidden files modified recently
find /home /root /tmp -name ".*" -mtime -7 2>/dev/null

# World-writable files
find / -type f -perm -002 2>/dev/null | grep -v "/proc\|/sys\|/tmp"
```

Run each. Verify your system (no compromises expected on your VM).

---

## Lab 2 — Install Lynis

```bash
sudo apt update
sudo apt install lynis -y
lynis --version
```

Verify installation.

---

## Lab 3 — Run Full Security Audit

```bash
sudo lynis audit system
```

Takes 2-5 minutes. Read output carefully. Note all [L] critical findings and [W] warnings.

---

## Lab 4 — Save Report

```bash
sudo lynis audit system 2>&1 | tee ~/lynis_report.txt
cat ~/lynis_report.txt | grep "\[L\]"
```

Show only critical findings.

---

## Lab 5 — Implement One Fix

Based on Lynis findings, pick one issue and fix it. Examples:

```bash
# Remove world-writable bit
sudo find /tmp -type d -perm -002 -exec chmod o-w {} \;

# Disable unused service
sudo systemctl stop cups
sudo systemctl disable cups

# Fix SSH config
sudo sed -i 's/#PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart sshd
```

---

## Lab 6 — Re-Audit and Verify Fix

```bash
sudo lynis audit system 2>&1 | grep -c "\[L\]"
```

Compare number of critical findings to Lab 4. Should be one fewer.

---

## Lab 7 — Create Complete Evidence Package

```bash
sudo tar -cvzf /tmp/complete_audit.tar.gz ~/lynis_report.txt /var/log
sha256sum /tmp/complete_audit.tar.gz > /tmp/audit.sha256
cat /tmp/audit.sha256
```

Chain of custody complete.