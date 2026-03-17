# Day 10 — Revision: File Systems Part 1 and Part 2

**Date:** Day 10
**Type:** Revision day — exam season protocol

---

## Inode
Every file on disk has an inode — a metadata record storing everything
about the file except its name. Stores: owner, permissions, file size,
timestamps, and pointers to actual data blocks on disk. The filename
lives in the directory entry which points to the inode number.

## Ctime
ctime is the timestamp that updates automatically whenever the inode
changes. Attackers can fake mtime and atime using touch -t command.
But ctime cannot be faked by normal commands. Forensic investigators
always check ctime — if mtime shows 2020 but ctime shows last Tuesday
3 AM, the file was tampered with.

## Slack Space
Block size on ext4 is 4096 bytes. Every file occupies at least one
full block. A 100 byte file still uses one 4096 byte block — remaining
3996 bytes is slack space. Old data sits there until overwritten.
Forensic tools recover deleted file fragments from slack space.

## file Command and Magic bytes
Every file type has a unique signature in its first bytes called magic
bytes. The file command reads these bytes and identifies the true type
— ignoring filename and extension completely. Malware named invoice.pdf
but containing an ELF executable is exposed immediately by file command.

## tmpfs
tmpfs lives entirely in RAM. No disk writes. Cleared completely on
reboot. Used for /tmp, /run, /dev/shm. Fileless malware runs in tmpfs
— no disk trace, no logs after reboot. That is why it is hard to detect.
```

---

