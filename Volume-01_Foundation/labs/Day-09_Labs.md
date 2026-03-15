# Day 09 — Labs: File Systems Part 1

---

## Command 1 — df -h
```bash
df -h
```
Output:
```
Filesystem      Size  Used Avail Use% Mounted on
udev            2.1G     0  2.1G   0% /dev
tmpfs           442M 1016K  441M   1% /run
/dev/sda1        56G   31G   23G  57% /
tmpfs           2.2G  4.0K  2.2G   1% /dev/shm
none            1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
tmpfs           2.2G  8.0K  2.2G   1% /tmp
VM_FOLDER        95G   64G   31G  68% /media/sf_VM_FOLDER
none            1.0M     0  1.0M   0% /run/credentials/getty@tty1.service
tmpfs           442M  108K  442M   1% /run/user/1000
```
What it means:
- /dev/sda1 56G — main Kali partition, 57% full, mounted at /
- udev — virtual filesystem in RAM representing hardware devices, nothing stored on disk
- tmpfs entries — all RAM-based temporary filesystems, cleared on reboot
- VM_FOLDER — VirtualBox shared folder from Ubuntu host machine showing through
- Security use: unusual mount points on a suspect machine = investigate immediately

---

## Command 2 — lsblk
```bash
lsblk
```
Output:
```
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 60.2G  0 disk
├─sda1   8:1    0   57G  0 part /
├─sda2   8:2    0    1K  0 part
└─sda5   8:5    0  3.1G  0 part [SWAP]
sr0     11:0    1 50.9M  0 rom
```
What it means:
- sda — entire virtual disk, 60.2GB
- sda1 — 57GB main partition, Kali installed here, ext4, mounted at /
- sda2 — 1KB extended partition container (MBR legacy trick for more than 4 partitions)
- sda5 — 3.1GB swap partition, not mounted at directory, special [SWAP] designation
- sr0 — CD/DVD ROM with VirtualBox guest additions ISO loaded
- Security use: lsblk shows ALL partitions including unmounted ones — hidden attacker partitions visible here even if df does not show them

---

## Command 3 — stat /etc/passwd
```bash
stat /etc/passwd
```
Output:
```
  File: /etc/passwd
  Size: 3487            Blocks: 8          IO Block: 4096   regular file
Device: 8,1     Inode: 3277949     Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2026-03-13 23:15:01.993502695 +0530
Modify: 2026-03-02 23:35:21.463294275 +0530
Change: 2026-03-02 23:35:21.483294276 +0530
 Birth: 2026-03-02 23:35:21.463294275 +0530
```
What it means:
- Size 3487 bytes, IO Block 4096 — file system allocates one full 4096 byte block even though file is only 3487 bytes. Remaining 609 bytes = slack space
- Inode 3277949 — unique identity of this file on the disk. Not the name, the real identity
- Links 1 — one hard link, one filename points to this inode
- Access 0644 — owner read-write, group read only, others read only
- Access timestamp March 13 — something read this file on that date (login check, script)
- Modify timestamp March 2 — last content change = Kali install date, user added
- Change timestamp March 2 — matches modify, inode metadata also changed that day
- Birth March 2 — file created on install day
- Forensic note: Modify and Access can be faked with touch -t. Change (ctime) cannot be faked by normal commands — attacker would need kernel-level access

---

## Command 4 — ls -li /etc/passwd
```bash
ls -li /etc/passwd
```
Output:
```
3277949 -rw-r--r-- 1 root root 3487 Mar  2 23:35 /etc/passwd
```
What it means:
- 3277949 — inode number at start, matches stat output exactly, confirms same file identity
- -rw-r--r-- — regular file, owner read-write, group and others read only
- 1 — hard link count
- root root — owner and group
- 3487 — file size
- Mar 2 23:35 — last modified date matching stat Modify timestamp
- Key point: filename /etc/passwd is just a label in the directory entry pointing to inode 3277949. The inode is the real file. The name is just a pointer.