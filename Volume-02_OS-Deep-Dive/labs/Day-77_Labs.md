# Day 77 — Labs: Permissions + Network + Archiving + System Info

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Lab 1 — Identify Current User and Privileges

```bash
whoami
id
groups
sudo -l
```
- Prints username, UID/GID/groups, group list, sudo access
- Note docker group if present (privilege escalation vector)

---

## Lab 2 — Check Network Connectivity

```bash
ping -c 4 8.8.8.8
```
- 4 ICMP requests to Google DNS
- 0% packet loss = server connected to internet
- If fails = isolated or firewalled

---

## Lab 3 — Verify HTTPS Connectivity

```bash
curl -I https://google.com
```
- HEAD request, headers only
- HTTP/2 200 = HTTPS reachable
- Alternative when ICMP blocked

---

## Lab 4 — List All Listening Ports

```bash
ss -tulnp
```
- Show all TCP/UDP listening ports
- Note process name and PID
- Unexpected port (not sshd, http, mysql) = investigate

---

## Lab 5 — Show Active Connections

```bash
ss -an | grep ESTABLISHED
```
- All active TCP connections
- Unexpected destination IP = possible C2 communication
- Check against threat intelligence database

---

## Lab 6 — Verify System Baseline

```bash
uname -a
uptime
date
hostname
echo $PATH
```
- Complete system profile
- Check kernel version for vulnerabilities
- Low uptime = recent reboot (cleanup indicator)
- Verify PATH doesn't contain /tmp first

---

## Lab 7 — Create tar Archive

```bash
mkdir ~/evidence
tar -cvf ~/evidence/logs.tar /var/log
ls -lh ~/evidence/logs.tar
```
- Bundle logs into archive
- Check size before compression

---

## Lab 8 — Compress Archive with gzip

```bash
tar -cvzf ~/evidence/logs.tar.gz /var/log
ls -lh ~/evidence/logs.tar.gz
```
- Compress while creating tar
- Compare size to uncompressed version (Lab 7)
- Smaller = successful compression

---

## Lab 9 — Extract tar Archive

```bash
mkdir ~/extracted
tar -xvzf ~/evidence/logs.tar.gz -C ~/extracted
ls -la ~/extracted/var/log | head -10
```
- Extract archive to test directory
- Verify files restored to original structure
- `-C` specifies extraction destination

---

## Lab 10 — Create zip Archive

```bash
zip -r ~/evidence/logs.zip /var/log
ls -lh ~/evidence/logs.zip
```
- Windows-compatible zip format
- Use when sharing evidence with Windows teams

---

## Lab 11 — Hash Archive for Chain of Custody

```bash
sha256sum ~/evidence/logs.tar.gz > ~/evidence/logs.sha256
cat ~/evidence/logs.sha256
```
- Calculate hash of compressed archive
- Store hash separately
- Verify hash after transfer to confirm no tampering

---

## Lab 12 — Complete Investigation Workflow

```bash
whoami
id
ss -tulnp
ss -an | grep ESTABLISHED
uname -a
uptime
tar -cvzf /tmp/investigation_evidence.tar.gz /var/log /tmp /home
sha256sum /tmp/investigation_evidence.tar.gz
```
- Full 60-second assessment and evidence preservation