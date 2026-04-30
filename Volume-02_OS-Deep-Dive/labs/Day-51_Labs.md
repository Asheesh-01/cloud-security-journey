# Day 51 — Labs: NTFS Structure

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — Volume Information
```cmd
fsutil volume diskfree C:
fsutil fsinfo volumeinfo C:
```
- `fsutil` — File System Utility for NTFS operations
- `volume diskfree` — total, free, and used space
- `fsinfo volumeinfo` — file system name, serial number, flags

---

## Lab 2 — NTFS Specific Information
```cmd
fsutil fsinfo ntfsinfo C:
```
- NTFS version, bytes per cluster, bytes per sector
- MFT start cluster location, total clusters, free clusters
- MFT zone size — reserved space for MFT expansion

---

## Lab 3 — File Timestamps
```cmd
dir /T:C C:\Windows\System32\notepad.exe
dir /T:W C:\Windows\System32\notepad.exe
dir /T:A C:\Windows\System32\notepad.exe
```
- `/T:C` — Creation timestamp
- `/T:W` — Last Written (modified) timestamp
- `/T:A` — Last Accessed timestamp
- These are STANDARD_INFORMATION timestamps — user-visible and modifiable

---

## Lab 4 — USN Journal Status
```cmd
fsutil usn queryjournal C:
```
- Shows $UsnJrnl status: ID, oldest USN, newest USN, journal size
- Journal disabled = important security finding (tampered or misconfigured)

---

## Lab 5 — USN Record for Specific File
```cmd
fsutil usn readdata C:\Windows\System32\notepad.exe
```
- Shows USN journal record for one file
- Last change type, timestamp, file attributes at change time
- In real investigation: read full journal with specialized forensic tools

---

## Lab 6 — MFT Query
```cmd
fsutil mft query C:
```
- MFT size and fragmentation status
- Large MFT with many files = large volume or many deleted files

---

## Lab 7 — Dirty Bit Check
```cmd
fsutil dirty query C:
```
- Dirty bit set = volume not cleanly unmounted (crash or force shutdown)
- Dirty bit clear = clean shutdown
- Forensic: dirty bit indicates unexpected shutdown during incident

---

## Lab 8 — Cluster Size
```cmd
fsutil fsinfo clusterinfo C:
```
- Bytes per cluster for this volume
- Typically 4096 bytes on standard NTFS
- Larger clusters = more slack space per file