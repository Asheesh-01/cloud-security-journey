# Day 21 — Labs

**Topic:** Linux OS Basics
**Environment:** Kali Linux VM (VirtualBox)
**User:** kali (not root)

---

## Lab 1 — Kernel Version

**Command:**
```bash
uname -r
uname -a
```

**What each part does:**
- `uname` — Unix Name. Prints system information.
- `-r` — release. Shows only the kernel version number.
- `-a` — all. Shows everything: kernel name, hostname, version,
  build date, machine hardware, OS name.

**Paste output here.**

---

## Lab 2 — Shell and User Identity

**Commands:**
```bash
echo $SHELL
echo $0
whoami
id
```

**What each part does:**
- `echo $SHELL` — prints the SHELL environment variable = path to current shell
- `echo $0` — $0 is the name of the current running program = shell name
- `whoami` — prints current username
- `id` — shows username, UID, primary group, and all group memberships.
  uid=1000(kali) means kali user has UID 1000.
  UID 0 = root always. UID 1000+ = regular users.

**Paste output here.**

---

## Lab 3 — Navigate Filesystem

**Commands:**
```bash
pwd
ls /
ls -la /etc
ls -la /var/log
ls -la /tmp
```

**What each part does:**
- `pwd` — Print Working Directory. Shows where you are right now.
- `ls /` — list root directory contents
- `ls -la` — -l = long format (permissions, owner, size, date)
             -a = all (include hidden files starting with .)
- Combining flags: `-la` = long format AND show hidden files

**Paste output here.**

---

## Lab 4 — Check Security-Critical Locations

**Commands:**
```bash
ls -la /etc/cron.d/
ls -la /var/spool/cron/
cat /var/log/auth.log | tail -20
```

**What each part does:**
- `ls -la /etc/cron.d/` — lists scheduled task files. Any unexpected file
  here = potential persistence mechanism.
- `cat` — concatenate and print file contents to terminal
- `|` — pipe. Sends cat output to tail as input
- `tail -20` — shows last 20 lines. Tail reads from the end of a file.
  Most recent log entries are at the bottom.

**Paste output here.**

---

## Lab 5 — Check Permissions on Sensitive Files

**Commands:**
```bash
ls -la /etc/passwd
ls -la /etc/shadow
ls -la /etc/sudoers
```

**What to look for:**
- /etc/passwd should be 644 — world readable (programs need to look up users)
- /etc/shadow should be 640 or 000 — NOT world readable (contains password hashes)
- /etc/sudoers should be 440 — readable by root and sudo group only

If /etc/shadow is world-readable = critical security misconfiguration.
Anyone can read password hashes and attempt offline cracking.

**Paste output here.**

---

## Lab 6 — Read /etc/passwd

**Command:**
```bash
cat /etc/passwd
```

**Format of each line:**
`username:password:UID:GID:comment:home_directory:shell`

- password field shows `x` — actual hash is in /etc/shadow (separation)
- UID 0 = root. Any account with UID 0 = root-equivalent.
- Shell field: `/bin/bash` = interactive login. `/usr/sbin/nologin` or
  `/bin/false` = service account, no interactive login allowed.

**Security: any account with UID 0 besides root = immediate finding.**

**Paste output here.**

---

## Lab 7 — Check PATH

**Command:**
```bash
echo $PATH
```

**What it shows:** colon-separated list of directories bash searches
when you type a command. Left to right — first match wins.

**Security risk:** if an attacker can prepend /tmp to your PATH, typing
`ls` might run /tmp/ls (malware) instead of /bin/ls.
Always verify PATH on a system you are investigating.

**Paste output here.**

---

## Lab 8 — Find All SUID Binaries

**Command:**
```bash
find / -perm -u=s -type f 2>/dev/null
```

**What each part does:**
- `find /` — search starting from root, recursively through all directories
- `-perm -u=s` — find files where the SUID bit is set for the user/owner.
  The `-` before `u=s` means "at least these bits set" (not exact match).
- `-type f` — only regular files (not directories or links)
- `2>/dev/null` — redirect standard error (stream 2) to /dev/null (discard).
  Without this, find prints permission denied errors for every directory
  it cannot access. /dev/null silently discards everything sent to it.

**What to look for:** standard SUID binaries include passwd, sudo, ping,
su, newgrp. Any SUID binary in /tmp, /home, or other unexpected locations
= immediate finding.

**Paste output here.**

---

## Lab 9 — Bash History

**Command:**
```bash
cat ~/.bash_history | tail -20
```

**What it shows:** last 20 commands run in this shell session history.
`~` = your home directory. .bash_history is a hidden file (starts with .)
that bash writes every command to.

**Forensic value:** during an investigation, bash_history shows what
commands the user (or attacker) ran. Attackers sometimes clear it with
`history -c` or delete the file — but the absence of history on a
machine that should have it is itself suspicious.

**Paste output here.**

---

## Lab 10 — Check /proc

**Commands:**
```bash
ls /proc | head -20
cat /proc/version
```

**What each part does:**
- `ls /proc` — lists contents of /proc virtual filesystem.
  Numbers = PIDs of running processes.
  Named directories = kernel-provided information.
- `head -20` — shows first 20 lines. Head reads from the beginning.
- `cat /proc/version` — shows kernel version and build information.
  Different from uname -r — includes compiler version and build date.

**Paste output here.**

---

## Lab Summary

| Lab | Commands | Security Purpose |
|-----|----------|-----------------|
| 1 | uname -r/-a | Kernel version — check for CVEs |
| 2 | whoami, id | Confirm user identity and groups |
| 3 | ls /, ls -la | Filesystem navigation baseline |
| 4 | cron.d, auth.log | Persistence and authentication check |
| 5 | ls -la passwd/shadow | Sensitive file permission audit |
| 6 | cat /etc/passwd | User account inventory |
| 7 | echo $PATH | PATH hijacking risk check |
| 8 | find SUID | Privilege escalation surface check |
| 9 | bash_history | Forensic command history |
| 10 | /proc | Running processes and kernel info |