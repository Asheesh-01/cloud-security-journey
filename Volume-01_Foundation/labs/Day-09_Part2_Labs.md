# Day 09 — Labs Part 2: File Systems

---

## Command 1 — file /etc/passwd
```bash
file /etc/passwd
```
Output:
```
/etc/passwd: ASCII text
```
What it means: file reads the first bytes of the file (magic bytes) and identifies true type. ASCII text = plain text file. If this returned ELF 64-bit executable that would be a critical red flag. Security use: detects malware disguised with innocent extensions like .jpg or .txt.

---

## Command 2 — mount | grep sda
```bash
mount | grep sda
```
Output:
```
/dev/sda1 on / type ext4 (rw,relatime,errors=remount-ro)
```
What it means:
- /dev/sda1 — the partition
- on / — mounted at root
- type ext4 — filesystem format
- rw — read-write
- relatime — access time only updated when older than modify time (performance)
- errors=remount-ro — on filesystem error, automatically switches to read-only to prevent corruption
- Security use: unexpected mounts, hidden encrypted containers, network shares all show here

---

## Command 3 — find /etc -name "passwd" -ls
```bash
find /etc -name "passwd" -ls
```
Output:
```
find: '/etc/ssl/private': Permission denied
find: '/etc/polkit-1/rules.d': Permission denied
find: '/etc/credstore.encrypted': Permission denied
find: '/etc/ipsec.d/private': Permission denied
  3276902  4  -rw-r--r--  1 root  root  92   Apr 19 2025  /etc/pam.d/passwd
find: '/etc/credstore': Permission denied
find: '/etc/openvas/gnupg': Permission denied
  3277949  4  -rw-r--r--  1 root  root  3487  Mar 2 23:35  /etc/passwd
find: '/etc/redis': Permission denied
find: '/etc/vpnc': Permission denied
```
What it means:
- Permission denied errors are file system access control working correctly — not a problem
- /etc/ssl/private contains SSL private keys — protected deliberately
- /etc/redis contains Redis credentials — protected deliberately
- Two passwd files found with different inodes (3276902 vs 3277949) — completely different files
- /etc/passwd — user account database, 3487 bytes
- /etc/pam.d/passwd — PAM authentication config for password changes, 92 bytes
- Security use: privilege escalation attacks target the permission denied directories

---

## Command 4 — sudo tune2fs
```bash
sudo tune2fs -l /dev/sda1 | grep -E "Block size|Inode size|Filesystem created|Last mount"
```
Output:
```
Last mounted on:          /
Block size:               4096
Filesystem created:       Sun Sep 21 14:08:49 2025
Inode size:               256
Last mount time:          Sun Mar 15 22:40:30 2026
```
What it means:
- Block size 4096 — every file gets minimum 4096 bytes of disk space. File of 100 bytes wastes 3996 bytes (slack space). Forensics can recover data from slack space.
- Inode size 256 — each inode record is 256 bytes storing all file metadata
- Filesystem created September 21 2025 — Kali VM install date. This is the forensic baseline — nothing on this disk can be older than this date legitimately
- Last mount March 15 2026 — today, when VM booted
- tune2fs reads the filesystem superblock — the master record containing all filesystem parameters
- Required sudo because reading raw device /dev/sda1 requires root