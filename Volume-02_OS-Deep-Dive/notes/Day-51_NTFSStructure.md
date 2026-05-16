# Day 51 — NTFS Structure

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 2 — Windows File System
**Subtopic:** 1 — NTFS Structure

---

## What It Is

NTFS = New Technology File System. Standard Windows file system since NT 1993.
A structured database, not just file organization.
Every file has metadata, timestamps, permissions, and potentially multiple data streams.

## Key Terms

**Volume** — storage unit formatted with NTFS. C: drive is a volume.
**Cluster** — smallest allocation unit. Default 4KB (8 × 512-byte sectors).
  File of 1 byte still uses one full 4KB cluster.

**MFT (Master File Table)** — central database. One record per file/directory.
  Stored as $MFT at start of volume. Protected from direct user access.
  Forensic tool reads raw MFT → finds deleted files, recovers metadata.

**MFT record** — 1KB entry per file. Contains attributes (name, size, timestamps,
  permissions, data location). Small files: data stored inside record (resident).
  Large files: data in clusters elsewhere, MFT record contains pointers (non-resident).

**$Bitmap** — tracks which clusters are free (0) or in use (1).
  File deletion = mark clusters free. Data not erased until clusters reused.
  Forensic: read free clusters → find deleted file data.

**$LogFile** — transaction journal. Changes written here before applied.
  Crash recovery: replay or rollback on next boot.
  Forensic: recent file system operation history.

**$UsnJrnl** — Update Sequence Number Journal. Every file system change logged.
  Stores: file name, parent directory, change type, timestamp.
  32MB default retention. First artifact examined in Windows investigations.
  Ransomware signature: thousands of modification events in rapid sequence.

**Timestamps** — four per file:
  STANDARD_INFORMATION: Created, Modified, MFT Changed, Last Accessed.
    Visible in Windows Explorer. Modifiable by users and malware.
  FILE_NAME: same four timestamps. Modified only by specific operations.
    Harder to modify. Forensic comparison detects timestomping.

**Timestomping** — malware modifying STANDARD_INFORMATION timestamps.
  Fails to modify FILE_NAME timestamps = forensic artifact remains.

**Slack space** — unused bytes in last cluster of a file. Contains previous data.
  Malware has been found hiding data in slack space.

**Journaling** — NTFS writes to $LogFile before applying changes.
  Prevents corruption from power loss. Makes NTFS reliable.

## Forensic Relevance

MFT: deleted file records remain until MFT record reused.
$UsnJrnl: change history including create/modify/delete events for deleted files.
$Bitmap: free clusters may contain unoverwritten deleted file data.
Slack space: remnants of previous file content.
Timestamp comparison: STANDARD_INFORMATION vs FILE_NAME reveals timestomping.

## Commands (Windows 10 VM)

```cmd
fsutil volume diskfree C:
fsutil fsinfo volumeinfo C:
fsutil fsinfo ntfsinfo C:
dir /T:C C:\Windows\System32\notepad.exe
dir /T:W C:\Windows\System32\notepad.exe
dir /T:A C:\Windows\System32\notepad.exe
fsutil usn queryjournal C:
fsutil usn readdata C:\Windows\System32\notepad.exe
fsutil mft query C:
fsutil dirty query C:
fsutil fsinfo clusterinfo C:
```