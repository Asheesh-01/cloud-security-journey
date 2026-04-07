# Day 27 — Labs: Confidentiality

**Environment:** Kali Linux VM

---

## Lab 1 — Check Listening Services
```bash
ss -tlnp
```
- `ss` — Socket Statistics
- `-t` — TCP only
- `-l` — listening sockets
- `-n` — numeric port numbers
- `-p` — show owning process

---

## Lab 2 — HTTP vs HTTPS Traffic
```bash
curl -v http://example.com 2>&1 | head -50
curl -v https://example.com 2>&1 | head -50
```
- `curl` — makes HTTP/HTTPS requests from command line
- `-v` — verbose, shows all headers and connection details
- `http://` — unencrypted, payload visible on network
- `https://` — TLS encrypted, payload protected in transit
- `2>&1` — merge stderr into stdout for full verbose output
- `| head -50` — first 50 lines only

---

## Lab 3 — File Permission Checks
```bash
ls -la /etc/shadow
ls -la /etc/passwd
ls -la ~/.ssh/ 2>/dev/null || echo "No .ssh directory yet"
```
- `ls -la` — long format listing including hidden files
- `/etc/shadow` — password hashes, should be 640 or stricter
- `/etc/passwd` — account info, world-readable at 644 by design
- `~/.ssh/` — SSH keys directory, should be 700
- `2>/dev/null` — discard errors silently
- `|| echo` — run this if previous command fails

---

## Lab 4 — Access Control Verification
```bash
cat /etc/shadow
sudo cat /etc/shadow | head -5
```
- First command — runs as normal user, should fail with Permission denied.
  Confirms access control is protecting confidentiality of password hashes.
- Second command — runs as root via sudo, authorized access succeeds.
  Shows the difference between authorized and unauthorized access to same file.

---

## Lab 5 — Find World-Readable Config Files
```bash
find /etc -readable -not -group root -name "*.conf" 2>/dev/null | head -10
```
- `find /etc` — search inside /etc
- `-readable` — files the current user can read
- `-not -group root` — not owned by root group
- `-name "*.conf"` — configuration files only
- `2>/dev/null` — suppress permission denied errors
- `| head -10` — show first 10 results
- Security use: finds config files accessible to non-root — potential
  confidentiality exposure depending on file contents.