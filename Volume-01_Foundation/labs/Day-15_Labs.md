# Day 15 — Labs
# Topic 5: Windows OS Basics — Subtopic 2: NTFS File System

---

## Lab Environment
- Host: Ubuntu 24.04 — Acer Swift Go 14
- VM: Windows 10 inside VirtualBox
- Tool used: Command Prompt (cmd)

---

## Lab 1 — Checking Volume and NTFS Information

### Command:
```cmd
fsutil fsinfo volumeinfo C:
```

### Output:
```
Volume Name :
Volume Serial Number : 0xf2fa27ed
Max Component Length : 255
File System Name : NTFS
Is ReadWrite
Supports Case-sensitive filenames
Preserves Case of filenames
Preserves & Enforces ACL's
Supports file-based Compression
Supports Disk Quotas
Supports Sparse files
Supports Named Streams
Supports Encrypted File System
Supports USN Journal
```

### What this output means:
- **File System Name: NTFS** — confirms C: drive uses NTFS. All NTFS features are active.
- **Preserves & Enforces ACL's** — confirms permissions are active on this volume. Every file has an ACL enforced at file system level.
- **Supports Named Streams** — this is the ADS feature. Named Streams = Alternate Data Streams. Hidden streams are possible on this volume.
- **Supports USN Journal** — every file change is logged. Forensic investigators use USN Journal to reconstruct what happened even after files are deleted.
- **Supports Encrypted File System** — EFS is available. Individual files can be encrypted using the logged-in user's certificate.

---

## Lab 2 — Checking NTFS Internal Details

### Command:
```cmd
fsutil fsinfo ntfsinfo C:
```

### Output:
```
NTFS Version      :    3.1
Total Sectors     :    95,447,039  (45.5 GB)
Total Clusters    :    11,930,879  (45.5 GB)
Free Clusters     :     8,560,145  (32.7 GB)
Bytes Per Sector  :    512
Bytes Per Cluster :    4096
Bytes Per FileRecord Segment : 1024
Mft Valid Data Length :  115.50 MB
```

### What this output means:
- **NTFS Version 3.1** — released with Windows XP in 2001, unchanged since. All modern Windows uses 3.1.
- **Total Clusters: 11,930,879 (45.5 GB)** — entire C: drive is 45.5 GB. This is the VM disk size.
- **Free Clusters: 8,560,145 (32.7 GB)** — 32.7 GB free, 12.8 GB used.
- **Bytes Per Sector: 512** — smallest unit the physical disk reads/writes. 512 bytes per sector.
- **Bytes Per Cluster: 4096** — this is critical. One cluster = 4096 bytes = 4 KB. Smallest unit NTFS allocates to any file. Even a 1-byte file gets a full 4096-byte cluster. Remaining bytes = slack space.
- **Bytes Per FileRecord Segment: 1024** — every file in MFT gets exactly 1024 bytes for its record. Stores filename, timestamps, permissions, and small file data.
- **Mft Valid Data Length: 115.50 MB** — current MFT size. Records for every file and folder on C: fit in 115.5 MB.

---

## Lab 3 — Creating a File and Checking Timestamps

### Commands:
```cmd
echo hello world > C:\Users\Public\testfile.txt
dir /T:C C:\Users\Public\testfile.txt
dir /T:W C:\Users\Public\testfile.txt
dir /T:A C:\Users\Public\testfile.txt
```

### Output:
```
dir /T:C  →  03/23/2026  04:43 AM    14 testfile.txt   (Created)
dir /T:W  →  03/23/2026  04:43 AM    14 testfile.txt   (Modified/Written)
dir /T:A  →  03/23/2026  04:43 AM    14 testfile.txt   (Accessed)
```

### What this output means:
- **All three timestamps are identical — 04:43 AM** — makes sense. File was just created. Created = Modified = Accessed = same moment.
- **File size: 14 bytes** — "hello world" is 11 characters. `echo` command adds carriage return + newline = 2 extra bytes. Total 13-14 bytes.
- **On disk: occupies 4096 bytes** — even though file is 14 bytes, NTFS allocates one full 4096-byte cluster. 4082 bytes wasted = slack space.
- **Flag /T:C** = show Created timestamp
- **Flag /T:W** = show Written/Modified timestamp
- **Flag /T:A** = show Accessed timestamp

---

## Lab 4 — Creating and Detecting Alternate Data Streams

### Command to create hidden stream:
```cmd
echo this is hidden > C:\Users\Public\testfile.txt:hiddenstream.txt
```

### Command — normal dir (does NOT show stream):
```cmd
dir C:\Users\Public\testfile.txt
```

### Output:
```
03/23/2026  04:44 AM    14 testfile.txt
1 File(s)    14 bytes
```

### What this means:
Normal dir shows only 14 bytes. Hidden stream is completely invisible. File size does not include the stream. This is exactly what attacker achieves when hiding malware in a stream — normal user sees nothing unusual.

---

### Command — dir /R (reveals hidden streams):
```cmd
dir /R C:\Users\Public\testfile.txt
```

### Output:
```
03/23/2026  04:44 AM    14 testfile.txt
                        17 testfile.txt:hiddenstream.txt:$DATA
1 File(s)    14 bytes
```

### What this means:
- `/R` flag reveals alternate data streams.
- `testfile.txt:hiddenstream.txt:$DATA` — this is the hidden stream. Colon separates filename from stream name.
- **Size: 17 bytes** — "this is hidden" = 14 characters + newline + carriage return = 17 bytes.
- **:$DATA** at the end = stream type. $DATA is the default NTFS stream type. Every file's main content is technically stored in the unnamed $DATA stream.
- Main file still shows 14 bytes — hidden stream size not counted in file size.

---

### Command — reading the hidden stream:
```cmd
more < C:\Users\Public\testfile.txt:hiddenstream.txt
```

### Output:
```
this is hidden
```

### What this means:
Content of hidden stream is readable directly using stream path syntax `filename:streamname`. An attacker would hide malware executable here instead of text, then execute it using:
`wscript C:\Users\Public\testfile.txt:payload.vbs`

---

## Lab 5 — Checking System32 Permissions

### Command:
```cmd
icacls C:\Windows\System32
```

### Output:
```
C:\Windows\System32 NT SERVICE\TrustedInstaller:(F)
                    NT SERVICE\TrustedInstaller:(CI)(IO)(F)
                    NT AUTHORITY\SYSTEM:(M)
                    NT AUTHORITY\SYSTEM:(OI)(CI)(IO)(F)
                    BUILTIN\Administrators:(M)
                    BUILTIN\Administrators:(OI)(CI)(IO)(F)
                    BUILTIN\Users:(RX)
                    BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
                    CREATOR OWNER:(OI)(CI)(IO)(F)
```

### What each line means:

**NT SERVICE\TrustedInstaller:(F)**
TrustedInstaller has Full Control on the folder itself. TrustedInstaller is a special Windows service account that owns most system files. Even Administrators cannot modify System32 files by default — only TrustedInstaller can. This prevents malware running as Administrator from replacing system files.

**NT SERVICE\TrustedInstaller:(CI)(IO)(F)**
CI = Container Inherit — subfolders get this permission too.
IO = Inherit Only — this rule applies to contents, not the folder itself.
F = Full Control inherited by everything inside System32.

**NT AUTHORITY\SYSTEM:(M)**
SYSTEM account has Modify on the folder. SYSTEM is what the kernel and system services run as — most powerful local account.

**NT AUTHORITY\SYSTEM:(OI)(CI)(IO)(F)**
OI = Object Inherit — files inside get this permission.
CI = Container Inherit — subfolders get this permission.
SYSTEM has Full Control inherited by all contents of System32.

**BUILTIN\Administrators:(M)**
Administrators have Modify only — not Full Control. They can modify files but cannot replace files owned by TrustedInstaller without taking ownership first. This is why even running as Administrator you cannot delete system files easily.

**BUILTIN\Users:(RX)**
Regular users — Read and Execute only. Can run programs from System32, read files, but cannot write or modify anything.

### Security lesson from this output:
This permission structure is why System32 is hard to tamper with. Malware that needs to replace a System32 file must first escalate to TrustedInstaller level or use a privilege escalation vulnerability — which is why kernel exploits are so valuable to attackers.

---

## Lab 6 — Cleanup

### Command:
```cmd
del C:\Users\Public\testfile.txt
```
File deleted. When a file is deleted in NTFS, the MFT record is marked as available — the data is not immediately overwritten. This is why deleted files can often be recovered in forensic investigations using tools like Autopsy.

---

## Key Findings From Today's Labs

1. Cluster size confirmed as 4096 bytes — 14-byte file still occupies one full 4096-byte cluster
2. Three timestamps tracked independently — Created, Modified, Accessed all recorded at file system level
3. ADS completely invisible to normal dir — only /R flag reveals hidden streams
4. Successfully created, hid, and read data in an ADS — same technique malware uses
5. System32 permissions verified — TrustedInstaller owns everything, regular users read-execute only

---

*Volume 01 — Topic 5 — Subtopic 2 | Day 15 | Exam Season*
*Commit: Day 15 - NTFS File System - ADS lab, timestamps, permissions, fsutil output*