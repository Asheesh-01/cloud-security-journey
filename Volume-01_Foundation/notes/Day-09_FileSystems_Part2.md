# Day 09 — Topic 3: File Systems Part 2 (Subtopic 6 continued)

**Date:** Day 09 continued
**Volume:** 01 — Computer and Internet Foundations
**GitHub:** github.com/Asheesh-01

---

## Subtopic 6 — Part 2: File System Types and Structure

### File System Types

**ext4 — Fourth Extended File System**
The standard Linux file system. Used on your Kali VM (sda1). Journaling file system — before writing data, it logs what it is about to do in a journal. If power cuts mid-write, the journal replays and fixes corruption. Supports files up to 16TB, partitions up to 1 exabyte.

**tmpfs — Temporary File System**
Lives entirely in RAM. No disk writes. Cleared completely on reboot. Used for /tmp, /run, /dev/shm. Fast but not persistent. Security relevance — malware running only in tmpfs leaves no disk trace after reboot.

**ext4 Technical Details (from your system):**
- Block size: 4096 bytes — every file gets space in 4096 byte chunks minimum
- Inode size: 256 bytes — each inode record is 256 bytes storing all metadata
- Filesystem created: September 21 2025 — when this Kali VM was first set up
- Last mounted: March 15 2026 — today, when VM booted

### File Types on Linux

Linux has 7 file types. The first character in ls -l output tells you the type:

- `-` regular file — normal file containing data
- `d` directory — contains list of filenames and their inode numbers
- `l` symbolic link — pointer to another file path (like a shortcut)
- `b` block device — disk drives, partitions (/dev/sda1)
- `c` character device — keyboards, terminals, serial ports
- `p` named pipe — inter-process communication channel
- `s` socket — network or local IPC socket

Security relevance: attackers sometimes disguise malware as a directory or use unusual file types to hide tools. `file` command reveals the true type regardless of extension or name.

### What `file` command does

The `file` command reads the first few bytes of a file (called the magic bytes or file signature) and identifies the true file type — ignoring the filename and extension completely.

`file /etc/passwd` → `ASCII text` — confirms it is a plain text file. If it returned `ELF 64-bit executable` that would be a critical red flag — /etc/passwd should never be executable binary.

Security relevance: malware often disguises itself with innocent extensions (.jpg, .pdf, .txt). `file` command sees through this by reading actual content.

### What `mount` shows

`/dev/sda1 on / type ext4 (rw,relatime,errors=remount-ro)`

Breaking this down:
- `/dev/sda1` — the device (partition on disk)
- `on /` — mounted at the root directory
- `type ext4` — the file system format
- `rw` — mounted read-write (not read-only)
- `relatime` — access time only updated if older than modify time (performance optimization)
- `errors=remount-ro` — if errors detected, automatically remount as read-only to prevent corruption

Security relevance: `mount` shows every file system currently attached. An attacker who mounts a hidden encrypted container or a network share — it appears here.

### The Permission Denied errors from find

```
find: '/etc/ssl/private': Permission denied
find: '/etc/redis': Permission denied
```

These are not errors in the bad sense. They are the file system permissions working correctly. Your user krishna (uid 1000) does not have read permission on those directories. The kernel enforces this at the file system level — even `find` cannot list contents of directories where you have no permission.

Security relevance: these protected directories contain sensitive data — SSL private keys in /etc/ssl/private, Redis credentials in /etc/redis. If an attacker gets past these permission walls via privilege escalation, they access the most sensitive files on the system.

### The two passwd files found

```
3276902  /etc/pam.d/passwd   — 92 bytes
3277949  /etc/passwd         — 3487 bytes
```

Two completely different files with similar names. Different inodes, different sizes, different directories.

- `/etc/passwd` — the user account database (inode 3277949, same one from Part 1)
- `/etc/pam.d/passwd` — PAM (Pluggable Authentication Modules) configuration for the passwd command. Controls how password changes are handled — complexity rules, hashing method.

Inode numbers confirm these are different files — 3276902 vs 3277949. Same filename component (`passwd`) but completely different file system objects.

---

## Real Practical Example — You save a file in VS Code

Step 1 — you press Ctrl+S in VS Code. VS Code calls the write() system call with your file content.

Step 2 — kernel receives the write() call. Checks your uid has write permission on this file (rw- for owner).

Step 3 — ext4 journal logs: "about to write X bytes to inode Y at block Z".

Step 4 — kernel writes data to the appropriate blocks on sda1. Block size is 4096 bytes — if your file is 100 bytes, one full block is allocated, 3996 bytes of slack space remain.

Step 5 — kernel updates the inode — new file size, new mtime (modify time), new ctime (change time).

Step 6 — journal marks write as complete. If power cuts after step 3 but before step 6, journal replays on next boot and fixes it.

Step 7 — VS Code gets confirmation from kernel. Shows saved indicator.

---

## Security Angles Summary

- `file` command — detects real file type regardless of extension — finds disguised malware
- `mount` output — shows hidden mounts, unexpected network shares, attacker containers
- Permission denied errors — file system access control working correctly — privilege escalation targets these protected directories
- Block size 4096 — slack space in every block — forensics can recover data from slack space
- Journal — write-ahead logging — helps forensics reconstruct what happened before crash or power cut
- Filesystem created date — September 21 2025 — your VM install date, baseline for forensic timeline

---

## Key Terms

- **ext4** — Fourth Extended File System, standard Linux journaling file system
- **Journal** — log of pending writes, allows recovery after crash
- **Block size** — minimum allocation unit on disk, 4096 bytes on your system
- **Inode size** — size of each inode record, 256 bytes on your system
- **Slack space** — unused bytes in a disk block when file is smaller than block size
- **Magic bytes** — first bytes of a file identifying its true type, read by `file` command
- **Symbolic link** — pointer to another file path, like a Windows shortcut
- **Hard link** — second filename pointing directly to same inode
- **PAM** — Pluggable Authentication Modules, controls authentication rules on Linux
- **relatime** — mount option updating access time only when older than modify time
- **remount-ro** — automatic read-only remount on filesystem errors to prevent corruption
- **tune2fs** — tool to read and modify ext2/3/4 filesystem parameters
- **Superblock** — master record of a filesystem storing all parameters (block size, inode count, creation date)