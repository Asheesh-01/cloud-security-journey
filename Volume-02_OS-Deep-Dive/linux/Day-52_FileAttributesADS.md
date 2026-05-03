# Day 52 — File Attributes and Alternate Data Streams

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 2 — Windows File System
**Subtopic:** 2 — File Attributes and ADS

---

## What It Is

File attributes: property flags controlling file visibility and behavior.
ADS: multiple independent data streams per file. Hidden from normal view.
Both are actively abused by attackers for hiding malware.

## File Attributes

**R** — Read-only. Cannot modify or delete normally.
**H** — Hidden. Invisible in Explorer and basic dir. Use dir /A:H to find.
**S** — System. Combined with H: requires dir /A:SH to find.
**A** — Archive. Set when file created/modified. Cleared by backup software.
**E** — Encrypted via EFS. Only owner and recovery agents can decrypt.
**C** — Compressed by NTFS. Cannot be both compressed and encrypted.

Attacker use: attrib +H +S malware.exe → invisible to basic investigation.
Detection: attrib command, dir /A:H, dir /A:SH.

## Alternate Data Streams (ADS)

NTFS allows multiple named data streams per file.
Default stream: visible, normal content. Named streams: hidden.
Syntax: filename:streamname
Reported file size = default stream only. ADS adds nothing visible.
ADS can contain any data including executables.

Zone.Identifier: legitimate ADS Windows adds to downloaded files.
Format: [ZoneTransfer] / ZoneId=3 (internet) or 0 (local) etc.
Used by SmartScreen and UAC to warn about internet-origin files.
Absence on file that should be downloaded = forensic indicator.

## Attacker Use of ADS

Payload storage: malware in ADS of legitimate Windows system file.
File scans clean. ADS contains malware. LOLBAS executes it.
Data staging: collect stolen data in ADS before exfiltration.
Zone.Identifier removal: prevents SmartScreen from flagging files.

APT groups documented using ADS to store payloads in Windows system files.
Parent process in logs = trusted Windows binary = minimal forensic footprint.

## Detection Commands

```cmd
dir /R filename          -- reveals all streams including ADS
dir /A:H                 -- find hidden files
dir /A:SH                -- find hidden + system files
attrib filename          -- show all attribute flags
more < file:streamname   -- read ADS content
```

## Commands (Windows 10 VM)

```cmd
attrib C:\Windows\System32\notepad.exe
dir /A:H C:\Windows\
dir /A:SH C:\
echo "Normal" > test.txt
echo "Hidden" > test.txt:hidden_stream
dir test.txt                       -- ADS invisible
dir /R test.txt                    -- ADS revealed
more < test.txt:hidden_stream      -- read ADS
attrib +H +S innocent.txt         -- hide file
dir /A:SH innocent.txt            -- find hidden file
attrib -H -S innocent.txt         -- restore
```