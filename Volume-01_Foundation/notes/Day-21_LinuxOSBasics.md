# Day 21 — Linux OS Basics

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 6 — Linux OS Basics

---

## Why Linux Matters for Cloud Security

Every AWS EC2, every SIEM server, every security tool runs Linux.
Kali VM is your primary lab from now on. Ubuntu 24.04 is your host OS.
Linux fluency is not optional — it is the daily operating environment
of a cloud security engineer.

---

## Key Terms

**Distro** — packaged version of Linux. Kali = security tools pre-installed.
Ubuntu = general purpose. RHEL/CentOS = enterprise servers. Arch = minimal.

**Kernel** — core OS. Manages hardware, memory, processes, system calls.
`uname -r` shows version. Old kernel = known CVEs = privilege escalation risk.

**Shell** — command interpreter. Bash is default on Kali and Ubuntu.
$ prompt = normal user. # prompt = root.

**sudo** — run one command as root. Logged. Correct way to use root privilege.

**Daemon** — background process on Linux. Equivalent to Windows service.
Named with d: sshd, nginx, systemd.

**Package manager** — installs/updates/removes software with dependency handling.
apt on Kali/Ubuntu. dnf/yum on RHEL/CentOS. pacman on Arch.

---

## Linux Distributions — Security Relevance

| Distro | Base | Package Manager | Use Case |
|---|---|---|---|
| Kali | Debian | apt | Security testing — your lab |
| Ubuntu | Debian | apt | General purpose, cloud instances |
| CentOS/RHEL | Red Hat | dnf/yum | Enterprise servers |
| Arch | Independent | pacman | Minimal, custom builds |

Kali on a production server = immediate red flag.

---

## Directory Structure — Security Focus
```
/etc        Config files — attacker target
            passwd, shadow, sudoers, ssh/sshd_config, cron.d/

/tmp        World-writable, clears on reboot
            Top malware staging location
            Any executable here = finding

/var/log    All log files
            auth.log = authentication events (Linux Security Event Log)
            syslog = general system messages

/home/[user]
            .bash_history = command history forensic artifact
            .bashrc = shell startup — persistence location
            .ssh/ = SSH keys

/proc       Virtual — kernel exposes live system info
            /proc/[PID]/ = per-process info
            /proc/net/tcp = active connections

/boot       Kernel image and bootloader
            Tampering here = attacker controls boot
```

**Four directories checked first during Linux incident:**
1. /tmp — malware executables
2. /etc/cron.d/ and /var/spool/cron/ — persistence via scheduled tasks
3. /home/[user]/.bashrc — persistence via shell startup
4. /var/log/auth.log — authentication forensics

---

## File Permissions

**Format:** `-rwxr-xr--`
- Position 1: type (- file, d directory, l symlink)
- Positions 2-4: owner permissions
- Positions 5-7: group permissions
- Positions 8-10: others permissions

**Numeric values:** r=4, w=2, x=1. Add for each set.
- 7 = rwx, 6 = rw-, 5 = r-x, 4 = r--, 0 = ---

**Common patterns:**
- 644 = rw-r--r-- = normal config file
- 755 = rwxr-xr-x = normal executable
- 600 = rw------- = private (SSH keys must be 600)
- 777 = rwxrwxrwx = everyone full access = immediate finding

**Special bits:**
- SUID (s in owner execute): file runs as file owner, not caller
  Legitimate: /usr/bin/passwd (runs as root to write /etc/shadow)
  Risk: vulnerable SUID root binary = any user gets root
  Find all: `find / -perm -u=s -type f 2>/dev/null`

- Sticky bit (t in others execute): on directories, users can only
  delete their own files. /tmp has sticky bit.

---

## Package Manager — apt Commands
```bash
apt update                      # refresh package list
apt upgrade                     # install updates
apt install [package]           # install
apt remove [package]            # remove
apt purge [package]             # remove + delete config
apt list --installed            # show installed packages
```

Security relevance: outdated packages = known CVEs.
Equifax 2017 — patched Apache Struts vulnerability ignored for 2 months.
`apt update && apt upgrade` is the first command on any new system.

---

## Kernel Version and CVEs

`uname -r` → your kernel version
Search nvd.nist.gov: linux kernel [version]
Dirty Cow CVE-2016-5195 — unpatched kernels allowed any user to write
/etc/passwd → trivial privilege escalation.

---

## Interview Prep — in interview-prep/Vol1_QA.md

**Recall:**
1. /bin vs /sbin — what is in each?
2. Why is /tmp a malware staging location?
3. What does SUID do? Legitimate example + security risk?
4. File shows 600 permissions — explain each set.
5. apt update vs apt upgrade — difference and order?

**Scenario:**
6. First four directories during Linux compromise investigation?
7. /tmp file, permissions 777, SUID set, owned by root — what do you do?