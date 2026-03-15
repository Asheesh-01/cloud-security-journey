# Day 09 — Topic 3: What is an Operating System (Subtopic 6 - File Systems Part 1)

**Date:** Day 09 of Cloud Security Journey
**Volume:** 01 — Computer and Internet Foundations
**GitHub:** github.com/Asheesh-01

---

## Subtopic 6 — File Systems (Part 1)

A file system is the method the OS uses to organize, store, and retrieve data on a storage device. Without a file system, a disk is just raw bytes with no structure.

**Analogy:** A library without a catalog system — books thrown on the floor randomly. The file system is the librarian. Every file gets a shelf number (block address), a title (filename), an owner, and timestamps. The librarian also records who is allowed to read each book (permissions).

**What a file system tracks per file — the inode:**
Every file has a metadata entry called an inode (index node). The inode stores:
- File size
- Owner (uid and gid)
- Permissions (rwx for owner, group, others)
- Timestamps — atime (last accessed), mtime (last modified), ctime (last changed)
- Pointers to actual data blocks on disk

The inode does NOT store the filename. The filename lives in the directory entry which points to the inode number. Two filenames can point to the same inode — called a hard link.

**Three timestamps — critical for forensics:**
- atime — last time file was read/accessed
- mtime — last time file content was modified
- ctime — last time inode metadata changed (cannot be faked by normal commands)

**Security angle:**
Attackers use timestomping — faking mtime and atime to make malware look old. But ctime updates automatically whenever the inode is touched. Forensic investigators always check ctime — if mtime shows 2020 but ctime shows last Tuesday at 3 AM, that is a red flag.

---

## Key Terms

- **File system** — rules for organizing, storing, retrieving data on disk
- **Inode** — index node, metadata entry storing all file properties except filename
- **Block** — fixed-size unit of disk storage (4096 bytes in ext4)
- **Hard link** — second filename pointing to the same inode number
- **atime** — access time, last time file was read
- **mtime** — modify time, last time file content changed
- **ctime** — change time, last time inode metadata changed — cannot be faked easily
- **Timestomping** — attacker technique faking file timestamps to evade forensics
- **Mount point** — directory where a file system is attached to the main directory tree
- **tmpfs** — temporary file system living in RAM, cleared on reboot
- **Swap partition** — disk space used when RAM is full, kernel pages memory here
- **df** — disk free command, shows all mounted file systems and usage
- **lsblk** — list block devices, shows disk and partition structure
- **stat** — shows complete inode metadata for a file
- **Slack space** — unused space in a disk block when file is smaller than block size

---

## Commands Run

```bash
df -h
lsblk
stat /etc/passwd
ls -li /etc/passwd
```

---

## Interview Prep — 5 Questions (Vol1_QA.md)

1. What is an inode? What information does it store and what does it NOT store?
2. What are the three file timestamps on Linux? Which one cannot be faked by an attacker and why?
3. A forensic investigator finds a file with mtime showing 3 years ago but ctime showing yesterday at 2 AM. What does this mean?
4. What is the difference between df and lsblk? When would you use each during an investigation?
5. What is timestomping? How does it work and how do you detect it?