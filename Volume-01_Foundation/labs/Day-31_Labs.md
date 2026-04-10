# Day 31 — Labs: Least Privilege

**Environment:** Kali Linux VM

---

## Lab 1 — Check Your Own Permissions
```bash
id
whoami
groups
```
- `id` — shows uid, gid, and all group memberships for current user
- `whoami` — prints current username
- `groups` — lists all groups current user belongs to
- Baseline audit: know your own privilege scope before anything else

---

## Lab 2 — Check Sudo Permissions
```bash
sudo -l
```
- `sudo -l` — lists what the current user can run with sudo
- `(ALL) ALL` = unrestricted sudo = overprivileged = finding in real audit
- Specific command entries = least privilege applied correctly

---

## Lab 3 — Find SUID Binaries
```bash
find / -perm -u=s -type f 2>/dev/null
```
- `find /` — search entire filesystem
- `-perm -u=s` — files with SUID bit set (run as owner, not caller)
- `-type f` — regular files only
- `2>/dev/null` — discard permission denied errors
- Unexpected SUID binaries owned by root = privilege escalation paths

---

## Lab 4 — View Sudoers File
```bash
sudo cat /etc/sudoers
```
- `/etc/sudoers` — defines all sudo privilege assignments
- `NOPASSWD:` entries = sudo without password = convenience over security
- Never edit directly — always use `visudo` to prevent syntax errors that
  lock you out of sudo

---

## Lab 5 — Create Minimal User
```bash
sudo useradd -m -s /bin/bash limiteduser
sudo passwd limiteduser
```
- `useradd -m` — create user with home directory
- `-s /bin/bash` — assign bash as login shell
- `passwd limiteduser` — set password
- No sudo, no special groups — minimal privilege by default

---

## Lab 6 — Verify Limited Permissions
```bash
sudo -u limiteduser id
sudo -u limiteduser sudo -l
sudo -u limiteduser cat /etc/shadow
```
- `-u limiteduser` — run command as limiteduser
- `id` — confirms limited UID and no privileged group memberships
- `sudo -l` — confirms no sudo permissions
- `cat /etc/shadow` — confirms no access to sensitive files

---

## Lab 7 — Check Root Processes
```bash
ps aux | grep "^root" | head -20
```
- `ps aux` — all processes with full details
- `grep "^root"` — lines starting with root = root-owned processes
- `head -20` — first 20 results
- Each root process with a vulnerability is a vertical escalation path
- Minimizing root processes reduces attack surface

---

## Lab 8 — Clean Up
```bash
sudo userdel -r limiteduser
```
- `userdel` — remove user account
- `-r` — also remove home directory and mail spool
- Always remove test accounts — orphaned accounts are a least privilege violation