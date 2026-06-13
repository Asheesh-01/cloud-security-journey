# Day 74 — Linux Command Line Mastery — Navigation + File Operations + Viewing Content

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 9.5 — Linux Command Line Mastery
**Subtopics:** Navigation Commands | File Operations | Viewing File Content
**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Subtopic 1 — Navigation Commands

### What It Is
Commands to orient yourself on a Linux system — where you are, what is here, how to move. First thing any attacker or analyst runs on a new system.

### Key Terms

**Terminal** — text-based interface. Type commands, system executes them.
**Shell (bash)** — program that reads commands and passes them to OS. Default on Kali.
**Directory** — Linux word for folder.
**Current working directory** — your present location in the file system.
**Absolute path** — starts from `/`. Works from anywhere. Example: `/home/kali/Documents`
**Relative path** — relative to current location. Example: `Documents` when you are in `/home/kali`
**Root directory `/`** — top of entire Linux file system. Everything lives under it.
**Root user** — superuser account with unlimited privileges. Different from root directory.
**Hidden file** — filename starts with dot. `ls -la` needed to see them. Attackers hide files this way.
**Inode** — data structure storing file metadata — permissions, owner, size, timestamps, disk location.

### Commands

```bash
pwd
```
Print Working Directory. Shows absolute path of current location. First command on any unknown system.

```bash
ls
```
Lists files and directories in current location. No details. No hidden files.

```bash
ls -l
```
Long format. Shows permissions, owner, group, size, modified date, name for each item.

```bash
ls -la
```
Long format + all hidden files. Use this during every investigation. Never plain ls.

```bash
ls -la /etc
```
List any directory by specifying its path. /etc = system config files, attacker target.

```bash
cd /etc
```
Change Directory. Move into specified directory. Absolute path works from anywhere.

```bash
cd ..
```
Move up one level to parent directory.

```bash
cd ~
```
Return to home directory instantly. ~ = shortcut for home.

```bash
cd -
```
Return to previous directory. Like a back button between two locations.

```bash
tree /etc -L 2
```
Visual tree of directory structure. -L 2 = two levels deep only.

### Security Angle — Post-Exploitation Navigation
Attacker sequence on compromised Linux: pwd → ls -la → cd /etc && ls -la → cd /home && ls -la → cd /var/log && ls -la. Complete system picture in 60 seconds. SOC analysts read bash history to reconstruct this exact sequence during investigation.

LinkedIn breach 2012 — attackers navigated to /etc on compromised servers, found database credentials in plaintext config files. 117 million password hashes stolen.

---

## Subtopic 2 — File Operations

### What It Is
Create, copy, move, rename, delete files and directories. Used by analysts to preserve evidence and by attackers to plant files and cover tracks.

### Key Terms

**cp** — copy. Original stays. New copy created at destination.
**mv** — move or rename. Original removed from source.
**rm** — remove. Permanent. No recycle bin. Gone immediately.
**mkdir** — make directory.
**rmdir** — remove empty directory only.
**touch** — create empty file or update timestamps on existing file.
**Wildcard `*`** — matches any characters. `*.log` = all log files.
**Recursive `-r`** — applies command to directory and all contents inside.
**Force `-f`** — skip confirmation prompts.

### Commands

```bash
mkdir ~/investigation
```
Create new directory named investigation in home directory.

```bash
mkdir -p ~/investigation/logs/auth
```
-p creates full path including all intermediate directories that do not exist yet.

```bash
touch ~/investigation/notes.txt
```
Creates empty file. If exists, updates timestamp. Attackers use touch -t to fake timestamps.

```bash
cp /var/log/auth.log ~/investigation/logs/auth/
```
Copy auth.log to investigation folder. Original untouched. Evidence preservation.

```bash
cp -r /var/log/ ~/investigation/logs/evidence/
```
-r recursive. Copies entire directory and all contents.

```bash
mv ~/investigation/notes.txt ~/investigation/case_notes.txt
```
Rename file. mv within same directory = rename.

```bash
rm ~/investigation/case_notes.txt
```
Delete file permanently. No confirmation. No recovery.

```bash
rm -rf ~/investigation/
```
Delete directory and all contents recursively, no confirmation. Most dangerous command on Linux. Always verify path before running.

### Security Angle — Covering Tracks
Attackers use: rm ~/.bash_history (delete command history), touch -t 202001010000 malware.sh (fake old timestamp), mv /tmp/malware.sh /usr/local/bin/updater (disguise malware as system binary). SOC analysts counter by checking inode change time which cannot be faked from userspace.

---

## Subtopic 3 — Viewing File Content

### What It Is
Tools for reading file contents. Different tools for different situations — file size, need to scroll, live monitoring. SOC analysts spend most time reading log files.

### Key Terms

**cat** — prints entire file to terminal at once.
**less** — pager. One screen at a time. Scroll forward and backward. Works on huge files.
**more** — older pager. Forward only. Less useful than less.
**head** — shows first N lines. Default 10.
**tail** — shows last N lines. Default 10. Newest log entries at bottom.
**tail -f** — follow mode. Stays open, prints new lines as written. Real-time log monitoring.
**stdout** — standard output. Where command results print — terminal screen by default.

### Commands

```bash
cat /etc/passwd
```
Print entire passwd file. Check for unexpected users, UID 0 accounts not named root, unexpected shells.

```bash
cat /etc/passwd | wc -l
```
Count total user accounts. Baseline this. Unexpected increase = new account created = possible persistence.

```bash
less /var/log/auth.log
```
Open auth log in pager. Space=next page, b=back, g=top, G=bottom, /term=search, q=quit.

```bash
head /var/log/auth.log
```
First 10 lines. Oldest entries.

```bash
head -n 20 /var/log/auth.log
```
First 20 lines. -n changes line count.

```bash
tail /var/log/auth.log
```
Last 10 lines. Most recent entries. Start here during investigation.

```bash
tail -n 50 /var/log/auth.log
```
Last 50 lines. Broader recent history.

```bash
tail -f /var/log/auth.log
```
Follow mode. Real-time log monitoring. Ctrl+C to stop.

```bash
tail -f /var/log/auth.log | grep "Failed"
```
Live feed filtered to failed logins only. Brute force attack shows as rapid scrolling lines with source IPs.

### Security Angle — Real-Time Detection
Cloudflare 2023 — analysts using tail -f with grep filters detected credential stuffing attack within minutes. Extracted attacker IP ranges, pushed firewall blocks before breach occurred. Real-time log monitoring = difference between catching attack in progress vs finding out weeks later.