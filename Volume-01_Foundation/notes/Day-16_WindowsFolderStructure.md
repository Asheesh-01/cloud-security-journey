# Day 16 — Windows Folder Structure

**Date:** [25-03-26]
**Volume:** 01 — Computer and Internet Foundations
**Topic:** Windows OS Basics
**Subtopic:** 3 — Windows Folder Structure

---

## What It Is in Plain English

Windows organizes all its files into a specific folder structure on the C: drive.
Every folder has a defined purpose — some hold system files, some hold user files,
some hold temporary files, some hold logs.

As a security engineer you need to know this structure by memory because during
any investigation your first question is always — where did this file come from
and should it be there?

A malware file sitting in C:\Windows\System32 looks legitimate.
The same file sitting in C:\Users\Public\Downloads is immediately suspicious.
Location tells you everything about whether something belongs.

---

## Key Terms Explained

**Directory** — same as folder. A container that holds files and other folders.

**Path** — the full address of a file on disk. C:\Windows\System32\lsass.exe.
C: is the drive, Windows is a folder, System32 is a folder inside Windows,
lsass.exe is the file.

**Root** — the top level of a drive. C:\ is the root. Everything lives somewhere
under the root.

**Environment variable** — a named value the OS stores that programs can read.
%WINDIR% stores C:\Windows. %TEMP% stores the path to the temp folder.
Used so programs do not need to hardcode paths.

**Hidden folder** — a folder with the hidden attribute set. Not shown by default
in File Explorer unless you enable Show Hidden Files. Attackers use hidden
folders to store files.

**AppData** — a hidden folder inside every user's profile that stores
application-specific data — settings, cache, local databases.

**NTUSER.DAT** — every user's personal Registry hive file stored in their profile.
Contains user-specific settings and the user-level persistence registry keys.

**Junction** — a folder that points to another folder. Used for backward
compatibility. Looks like a folder but redirects to a different path.

**pagefile.sys** — Windows swap file. When RAM fills up the kernel moves memory
pages here. Contains sensitive data that was in RAM — passwords, keys, tokens.

**Prefetch** — Windows stores prefetch files for every program run. Forensic
investigators read these to know what ran and when, even after deletion.

---

## The Complete Windows Folder Structure

### C:\ — Root of the Drive
Everything lives here. Top level of the C: drive.

**Security relevance:** Any unexpected file directly in C:\ is suspicious.
Legitimate Windows only places specific known files here. A random executable
sitting in C:\ is a finding.

---

### C:\Windows — The OS Itself
Contains all Windows operating system files.
Environment variable: %WINDIR% or %SystemRoot%

**Security relevance:** Should be owned by TrustedInstaller. Any file created
or modified here outside of a Windows Update is suspicious. Attackers drop
files here to blend in with legitimate OS files.

---

### C:\Windows\System32 — Most Important Folder in Windows
Contains the core executables and DLLs that Windows needs to run.
Every critical process lives here:

- C:\Windows\System32\smss.exe
- C:\Windows\System32\csrss.exe
- C:\Windows\System32\lsass.exe
- C:\Windows\System32\services.exe
- C:\Windows\System32\cmd.exe
- C:\Windows\System32\notepad.exe

**Your VM has 4,181 files totaling 1.9 GB in System32.**

**Security relevance:** If any process claims to be a Windows system process
but is NOT running from C:\Windows\System32 — it is malware. This is the
single most important location check in Windows investigation.

---

### C:\Windows\SysWOW64 — 32-bit Compatibility Layer
Contains 32-bit versions of system DLLs on a 64-bit system.
Confusingly named — SysWOW64 contains 32-bit files, System32 contains 64-bit.

**Security relevance:** Attackers sometimes run 32-bit malware and exploit
the naming confusion to trick investigators.

---

### C:\Windows\Temp — Windows Temporary Files
Windows and system processes write temporary files here.

**Security relevance:** Top malware drop location. All users have write access.
Any executable file permanently residing in C:\Windows\Temp is immediately
suspicious. Your VM confirmed this folder exists and was recently modified.

---

### C:\Windows\System32\drivers — Device Drivers
Contains all installed device driver files (.sys files).
These load into kernel mode — Ring 0.

**Security relevance:** Rootkits install malicious .sys files here to get
kernel mode access. A suspicious .sys file here = potential rootkit.

---

### C:\Windows\System32\config — Registry Hive Files
The Windows Registry is stored as binary files here.
Files: SAM, SYSTEM, SOFTWARE, SECURITY, DEFAULT.

**Security relevance:** The SAM file contains local user password hashes.
Locked while Windows runs. Attackers extract it offline to crack passwords.

---

### C:\Windows\Prefetch — Program Execution Evidence
Windows stores prefetch files for every program ever run.
Each prefetch file contains the program name and execution timestamp.

**Security relevance:** Even if malware is deleted, its prefetch file remains.
Forensic investigators read prefetch files to prove what ran and when.
This is real evidence that survives deletion.

---

### C:\Users — All User Profiles
Every user account on the machine has a folder here.
C:\Users\Asheesh, C:\Users\Administrator, C:\Users\Public.

---

### C:\Users\[username] — Individual User Profile
Contains everything belonging to one user.
Your VM username: vboxuser.

**Key file — NTUSER.DAT:** The user's personal Registry hive.
1 MB file storing user settings and persistence registry keys.
Investigators always analyze NTUSER.DAT during a compromise investigation.

---

### C:\Users\[username]\AppData — Hidden Folder
Hidden by default. Three subfolders:

**AppData\Local** — machine-specific app data. Browser cache, app settings.
Temp folder lives here: AppData\Local\Temp = %TEMP%

**AppData\LocalLow** — low-integrity data for sandboxed browser plugins.

**AppData\Roaming** — app data shared across domain machines. = %APPDATA%
This is the primary malware persistence location.
Writable without Administrator privileges. Hidden from casual view.
Malware installs here to survive reboots without needing elevation.

---

### C:\Users\[username]\Downloads — Initial Infection Point
Where browsers save downloaded files by default.

**Security relevance:** Primary initial infection point. Malicious email
attachments, fake installers, trojanized tools all land here first.
Always check Downloads during investigation.

---

### C:\Users\Public — Shared Folder
Accessible to all users on the machine. No password needed.
Writable by all users.

**Security relevance:** Common malware staging location. You proved this
in the NTFS lab — testfile.txt was created here because all users can write.
Malware drops payloads here to be accessible regardless of which account
is compromised.

---

### C:\Program Files — 64-bit Installed Applications
Requires Administrator privileges to write here.
Legitimate software installs here.

**Security relevance:** Malware avoids here because it needs elevation.
If malware IS in Program Files it already had Admin privileges.

---

### C:\Program Files (x86) — 32-bit Applications
32-bit software on a 64-bit system installs here.
Also requires Administrator to write.

---

### C:\ProgramData — Hidden All-User Application Data
Hidden by default. Application data shared across all users.
Environment variable: %PROGRAMDATA%

**Security relevance:** Malware stores files here when it needs data accessible
regardless of which user is logged in.

---

### C:\$Recycle.Bin — Deleted Files
When you delete a file and it goes to the Recycle Bin it moves here.
Contains a hidden subfolder per user SID.

**Security relevance:** Attackers delete tools after use but forget the
Recycle Bin still holds them. Always check the Recycle Bin during investigation.
Forensic tools recover files from here easily.

---

## Real Practical Example — Malware Infection Through Fake PDF

**Step 1** — You receive a phishing email with Invoice_March2026.pdf.exe.
You click it. The .exe extension was hidden by Windows.

**Step 2** — Executable runs from:
C:\Users\vboxuser\Downloads\Invoice_March2026.pdf.exe
Running in user mode with your privileges.

**Step 3** — Malware copies itself to:
C:\Users\vboxuser\AppData\Roaming\Microsoft\Windows\svchost.exe
Hidden folder, legitimate-sounding name. Not the real svchost.

**Step 4** — Malware creates a Registry Run key pointing to the fake svchost.exe
so it starts every time you log in. No Administrator needed.

**Step 5** — Malware downloads payload to C:\Users\Public\update.dat
Accessible to all users.

**Step 6** — Malware deletes Invoice_March2026.pdf.exe from Downloads.
Original infection file gone.

**Step 7 — SOC Investigation:**
- Checks Downloads — file gone.
- Checks AppData\Roaming — finds fake svchost.exe.
- Checks Registry Run keys — finds persistence entry.
- Checks C:\Users\Public — finds update.dat.
- Checks process list — finds fake svchost.exe running from wrong path.
- Case begins.

---

## The Four Folders Every Investigator Checks First

| Folder | Why |
|--------|-----|
| C:\Users\[name]\Downloads | Initial infection landing point |
| C:\Users\[name]\AppData\Roaming | Primary persistence and malware storage |
| C:\Users\Public | Shared staging — accessible from any account |
| C:\Windows\Temp | Download and execution staging |

Any executable file in these four locations that you cannot explain is a finding.

---

## Environment Variables — Quick Reference

| Variable | Resolves To |
|----------|-------------|
| %WINDIR% | C:\Windows |
| %TEMP% | C:\Users\[name]\AppData\Local\Temp |
| %APPDATA% | C:\Users\[name]\AppData\Roaming |
| %PROGRAMDATA% | C:\ProgramData |
| %USERNAME% | Currently logged-in username |

---

## Key Security Lessons

1. Location is everything — same filename, wrong path = malware
2. AppData\Roaming is the top persistence location — writable, hidden, no elevation needed
3. System32 has 4,181 files — any new unexplained file there is a finding
4. pagefile.sys holds RAM contents — unencrypted = passwords on disk
5. Prefetch survives deletion — proves what ran even after malware removes itself
6. NTUSER.DAT is the user's personal registry — always analyze during investigation
7. $Recycle.Bin holds deleted files — attackers forget to empty it
8. Junctions are not real folders — backward compatibility redirects
9. %TEMP% resolves to AppData\Local\Temp per user — not C:\Windows\Temp
10. vboxpostinstall.log in C:\ reveals VM presence — attacker reconnaissance reads this

---

## Connection to Previous Topics

Topic 3 covered file systems — NTFS is what stores every folder and file
in this structure. The inodes (MFT records), permissions (ACLs), and timestamps
from Subtopic 2 (NTFS) apply to every single folder listed here.

The four critical processes from Subtopic 1 (Architecture) all live in System32.
The lsass.exe that stores password hashes — C:\Windows\System32\lsass.exe.
If it runs from anywhere else — that is the first check from Subtopic 1
applied directly to this folder structure knowledge.