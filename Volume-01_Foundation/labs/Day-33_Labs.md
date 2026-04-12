# Day 33 — Labs: Authentication vs Authorization

**Environment:** Kali Linux VM

---

## Lab 1 — Generate SSH Key Pair (Authentication Demo)
```bash
ssh-keygen -t ed25519 -C "test-auth-demo" -f /tmp/test_auth_key -N ""
```
- `ssh-keygen` — generates cryptographic key pair for SSH authentication
- `-t ed25519` — Ed25519 algorithm, modern and secure
- `-C` — comment label embedded in public key
- `-f /tmp/test_auth_key` — output path for private key
- `-N ""` — no passphrase for demo purposes only

---

## Lab 2 — Inspect Key Files
```bash
ls -la /tmp/test_auth_key*
cat /tmp/test_auth_key.pub
```
- `ls -la` — private key must be 600, public key 644
- `cat` — shows public key content — safe to share with servers
- Private key stays local — proves identity without revealing the key itself

---

## Lab 3 — Authorization at OS Level
```bash
ls -la /etc/passwd
ls -la /etc/shadow
ls -la /etc/sudoers
```
- Three files, three authorization levels
- passwd: 644 — world readable by design
- shadow: 640 or 000 — root only, contains password hashes
- sudoers: 440 — root and sudo group only

---

## Lab 4 — Authentication OK, Authorization Fails
```bash
cat /etc/shadow
sudo cat /etc/shadow | head -3
```
- First: authenticated as kali, not authorized. Permission denied.
- Second: sudo provides root authorization. Access granted.
- Identical authentication, different authorization = different outcome

---

## Lab 5 — Active Authentication Sessions
```bash
who
w
last | head -10
```
- `who` — currently authenticated sessions
- `w` — sessions plus current activity
- `last` — authentication history from /var/log/wtmp

---

## Lab 6 — Authorization Scope Check
```bash
sudo -l
id
groups
```
- `sudo -l` — exact commands authorized to run as root
- `id` — UID, GID, group memberships that determine authorization
- `groups` — all groups = all authorization categories this user belongs to

---

## Lab 7 — HTTP Auth/AuthZ Status Codes
```bash
sudo cat /var/log/apache2/access.log 2>/dev/null | grep -E " 401 | 403 " | tail -10 || echo "No entries"
```
- `grep -E " 401 | 403 "` — finds auth and authz failure responses
- 401 = not authenticated, 403 = authenticated but not authorized
- Pattern of 403 from one IP = authorized user probing unauthorized resources

---

## Lab 8 — Clean Up
```bash
rm /tmp/test_auth_key /tmp/test_auth_key.pub
```
- Remove temporary keys immediately after demo
- Unused credentials left on disk are a security risk