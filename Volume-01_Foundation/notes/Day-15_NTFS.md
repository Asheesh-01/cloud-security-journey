# Day 15 — Topic 5: Windows OS Basics
# Subtopic 2 — NTFS File System

---

## What is NTFS

NTFS stands for New Technology File System. It is the file system Windows uses to organize and store everything on disk. A file system is the method an OS uses to know where files are, who owns them, what permissions they have, and what data they contain. Without a file system, a disk is just random bytes with no structure. NTFS gives those bytes meaning.

Introduced with Windows NT in 1993. Still the primary Windows file system today.

---

## Key Terms

| Term | Meaning |
|---|---|
| File system | System for organizing data on a storage device. Defines how files are named, stored, retrieved, and protected. |
| Cluster | Smallest unit of disk space NTFS can allocate to a file. Default size 4096 bytes (4 KB). Even a 1-byte file occupies one full cluster. |
| MFT (Master File Table) | Heart of NTFS. Database containing one record for every file and folder on the volume. Every file's name, size, timestamps, permissions, and location on disk stored here. |
| Metadata | Data about data. File name, size, creation time, owner — these are metadata. The actual file content is the data. |
| Volume | A storage unit with its own file system. C: drive is one volume. D: drive is another. |
| Attribute | A property of a file stored in the MFT. A file in NTFS is a collection of attributes — name, data, security, timestamps. |
| ACL (Access Control List) | List of permissions attached to a file or folder. Contains ACEs — each entry says which user or group has which permissions. |
| ACE (Access Control Entry) | One entry inside an ACL. Specifies a user or group and what permissions they have — allow or deny. |
| ADS (Alternate Data Streams) | NTFS feature allowing a file to have multiple hidden data streams under one filename. Default stream is what you see. Extra streams are invisible to normal directory listings. |
| Journaling | NTFS keeps a transaction log of changes. If system crashes mid-write, NTFS recovers by replaying or rolling back the journal. Prevents corruption. |
| Timestamps | NTFS stores four timestamps per file — Created, Modified, Accessed, MFT Changed. |
| Slack space | Wasted space in a cluster after the file data ends. 14-byte file in a 4096-byte cluster wastes 4082 bytes. Deleted file fragments can hide in slack space — forensic evidence. |
| Resident attribute | File data stored directly inside the MFT record. Happens for small files under ~700 bytes. |
| Non-resident attribute | File data stored outside the MFT in disk clusters, with pointers in the MFT record. Happens for larger files. |

---

## How NTFS Organizes a Disk

When you format a drive with NTFS, Windows:
1. Divides the disk into clusters
2. Creates the Master File Table at the beginning of the volume
3. Assigns one 1024-byte MFT record to every file and folder

For small files — file data stored directly in MFT record (resident attribute).
For large files — MFT record contains pointers to clusters on disk where data lives (non-resident attribute).

---

## NTFS Timestamps — Four Per File

| Timestamp | Flag | What It Records |
|---|---|---|
| Created | C | When file was first created on this volume |
| Modified | W (Written) | When file content was last changed |
| Accessed | A | When file was last opened or read |
| MFT Changed | $MFT | When the MFT record itself was last updated — rename, permission change |

**Critical forensic rule:** Modified time can be faked by attackers using timestomping — changing M and A times to make malware look old. But MFT Changed timestamp is harder to fake. If Modified says 2019 but MFT Changed says last Tuesday 3AM — timestamps were tampered with.

**Another forensic rule:** If Created is newer than Modified — file was copied from somewhere. Copy resets Created to now but keeps original Modified. File was brought in from outside, not created locally.

---

## NTFS Permissions — The ACL System

Six standard NTFS permissions:

| Permission | What It Allows |
|---|---|
| Full Control | Read, write, modify, delete, change permissions, take ownership |
| Modify | Read, write, delete, execute — cannot change permissions |
| Read and Execute | View content and run executable files |
| Read | View file content and attributes |
| Write | Write to file, add files to folder |
| List Folder Contents | View folder contents — folders only |

**Critical rule:** Deny always overrides Allow. User in two groups — one allowed, one denied — user is denied. Deny wins always.

**ACL flags seen in icacls output:**

| Flag | Meaning |
|---|---|
| (F) | Full Control |
| (M) | Modify |
| (RX) | Read and Execute |
| (CI) | Container Inherit — subfolders inherit this permission |
| (OI) | Object Inherit — files inside inherit this permission |
| (IO) | Inherit Only — applies to contents, not the folder itself |
| (GR,GE) | Generic Read and Generic Execute |

---

## System32 Permissions — What We Observed

```
NT SERVICE\TrustedInstaller:(F)       — Full Control — owns the files
NT AUTHORITY\SYSTEM:(M)              — Modify
BUILTIN\Administrators:(M)           — Modify only — cannot replace owned files
BUILTIN\Users:(RX)                   — Read and Execute only
```

**TrustedInstaller** owns most Windows system files. Even Administrators cannot modify System32 files by default — only TrustedInstaller can. This is why malware running as Administrator still cannot replace system files without escalating to TrustedInstaller level or using a vulnerability.

---

## Alternate Data Streams — The Hidden Threat

ADS allows any file to have additional hidden data streams attached to it.

Normal file: `testfile.txt` — visible, 14 bytes.
With ADS: `testfile.txt:hiddenstream.txt` — hidden stream attached, completely invisible in normal dir.

**How to create ADS:**
```cmd
echo this is hidden > testfile.txt:hiddenstream.txt
```

**Normal dir does NOT show it:**
```
dir testfile.txt
→ shows 14 bytes only
```

**dir /R reveals it:**
```
dir /R testfile.txt
→ shows testfile.txt:hiddenstream.txt:$DATA  17 bytes
```

**Read the hidden stream:**
```cmd
more < testfile.txt:hiddenstream.txt
→ this is hidden
```

**Why attackers use ADS:**
- Hidden stream is completely invisible to normal users
- File size shown does not include hidden stream
- File passes quick visual inspection looking normal
- Malware executable hidden in stream can be executed directly

**Legitimate ADS example:**
Zone.Identifier — when you download a file from internet, Windows attaches this stream marking it as internet-origin. This is why you see security warnings when opening downloaded files.

---

## Real Practical Example — You Save a File in Notepad

Step 1 — you type "hello world" in Notepad. Click Save As. Save as notes.txt on Desktop.

Step 2 — Windows calls NtCreateFile() through NTDLL into kernel. I/O Manager receives request.

Step 3 — NTFS creates new MFT record for notes.txt. Allocates one cluster (4096 bytes) even though file is only 11 bytes. Remaining 4085 bytes = slack space.

Step 4 — NTFS writes file data directly into MFT record — file is small enough to be resident attribute.

Step 5 — NTFS sets all four timestamps — Created = now, Modified = now, Accessed = now, MFT Changed = now.

Step 6 — NTFS updates directory entry for Desktop folder adding notes.txt.

Step 7 — NTFS writes journal entry recording this transaction. If power cuts now, journal allows recovery on next boot.

Step 8 — you open the file later. Accessed timestamp updates. Modified and Created stay same.

Step 9 — you edit and save. Modified timestamp updates. Created stays same.

Step 10 — attacker runs tool to change Modified timestamp back to 2020 to hide their activity. Modified now says 2020. But MFT Changed says today. Forensic investigator sees discrepancy — timestamps were tampered.

---

## Three NTFS Features That Matter in Every Security Investigation

1. **Permission misconfiguration** — if a folder has weak permissions (Everyone: Full Control), any user or malware can write, execute, delete. Always check permissions on sensitive directories during investigation.

2. **Alternate Data Streams** — used by real malware in the wild. Use `dir /R` or a dedicated ADS scanner to reveal hidden streams on suspicious files.

3. **Timestomping** — standard attacker anti-forensics. After compromising a system, attacker changes file timestamps to blend malware in with old system files. Defence: always check MFT Changed timestamp alongside Modified. Check NTFS journal for actual creation time.

---

*Volume 01 — Topic 5 — Subtopic 2 | Day 15 | Exam Season*