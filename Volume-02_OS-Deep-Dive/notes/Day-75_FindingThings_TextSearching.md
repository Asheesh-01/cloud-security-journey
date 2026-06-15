# Day 75 — Finding Things + Text Searching

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 9.5 — Linux Command Line Mastery
**Subtopics:** 4 — Finding Things | 5 — Text Searching
**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Subtopic 4 — Finding Things

### What It Is
Search entire file system for files matching specific criteria — name, permission, owner, time modified. Core skill for security investigation and privilege escalation detection.

### Key Terms

**find** — real-time file system search. Always current. Slower on large systems.
**locate** — searches pre-built database. Fast but may be stale. Run `sudo updatedb` to refresh.
**which** — finds which binary runs when you type a command. Searches $PATH.
**whereis** — finds binary, source, and manual page locations.
**$PATH** — directories shell searches for commands. Attacker can prepend malicious directory to hijack commands.
**SUID** — file runs as file owner not as user who ran it. Owner root + SUID = any user gets root execution.
**-perm -4000** — find files with SUID bit set.
**-mtime -1** — modified in last 24 hours. -mtime -7 = last 7 days.
**2>/dev/null** — redirect error output to discard. Keeps output clean by hiding permission denied errors.

### Commands

```bash
find / -name "*.sh" 2>/dev/null
```
Find all shell scripts on system. Attacker planted scripts appear here.

```bash
find /tmp /var/tmp /dev/shm -type f 2>/dev/null
```
Find all files in world-writable directories. Attacker staging areas. Any executable here = suspicious.

```bash
find / -perm -4000 -type f 2>/dev/null
```
Find all SUID binaries. First command for privilege escalation check. Non-standard path result = backdoor.

```bash
find / -perm -4000 -type f 2>/dev/null | grep -v "^/usr\|^/bin\|^/sbin"
```
SUID binaries outside standard system paths only. Filters noise. High suspicion results only.

```bash
find / -mtime -1 -type f 2>/dev/null
```
Files modified in last 24 hours. Shows attacker activity timeline.

```bash
find /etc -mtime -7 -type f 2>/dev/null
```
Config files modified in last 7 days. Unexpected changes = attacker modified system.

```bash
find /home -name ".*" -type f 2>/dev/null
```
Hidden files in home directories. Attacker persistence scripts hidden here.

```bash
locate passwd
```
Fast database search. Run sudo updatedb first if recently created files not appearing.

```bash
which python3
which nc
```
Find which binary runs for command. Detects PATH hijacking — malicious binary placed earlier in PATH.

```bash
echo $PATH
```
Shows full PATH. Check for unexpected writable directories prepended by attacker.

### Security Angle — Equifax 2017
Attackers used find commands to locate config files containing database credentials in non-standard locations. `find / -name "*.properties"` and `find / -name "*.conf"` revealed plaintext passwords across the server in seconds. 147 million records stolen.

SUID abuse: if find binary has SUID set (misconfiguration), attacker runs `find . -exec /bin/bash -p \;` for instant root shell.

---

## Subtopic 5 — Text Searching with grep

### What It Is
Search file content line by line for matching patterns. Most used command in daily SOC work. Turns millions of log lines into targeted evidence in seconds.

### Key Terms

**grep** — Global Regular Expression Print. Reads input line by line, prints lines matching pattern.
**-r** — recursive. Search all files in directory and subdirectories.
**-i** — case insensitive. Matches regardless of upper/lower case.
**-n** — line numbers. Prints line number with each match.
**-l** — list files only. Prints filenames containing match, not the matching lines.
**-v** — invert. Prints lines that do NOT match.
**-c** — count. Prints number of matching lines instead of the lines themselves.
**-A n** — print n lines after match. Context after suspicious event.
**-B n** — print n lines before match. Context before suspicious event.
**-o** — print only matching part of line, not full line. Used with regex to extract specific data like IP addresses.
**Pattern** — text or regex grep searches for.
**Pipe |** — chains commands. Output of left command becomes input of right command.

### Commands

```bash
sudo journalctl | grep "Failed"
```
All failed authentication events from systemd journal (Kali equivalent of auth.log).

```bash
sudo journalctl | grep -i "failed password"
```
Case insensitive search. More reliable across different log formats.

```bash
sudo journalctl | grep -in "failed" | head -20
```
Case insensitive + line numbers. First 20 matches only.

```bash
grep -r "password" /etc 2>/dev/null
```
Recursive search for plaintext passwords in all config files under /etc.

```bash
grep -ri "password" /etc 2>/dev/null
```
Same but case insensitive. Catches Password, PASSWORD, passwd.

```bash
grep -rn "password" /etc 2>/dev/null
```
Adds line numbers. Output: filename:linenumber:content. Copy directly into evidence notes.

```bash
grep -r "password" /etc 2>/dev/null -l
```
List filenames only. Clean overview of which files contain passwords before diving deeper.

```bash
sudo journalctl | grep -c "Failed"
```
Count of failed auth events. 5 = forgot password. 500 = brute force.

```bash
sudo journalctl | grep "Failed" | grep -o '[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+' | sort | uniq -c | sort -rn
```
Full IP extraction pipeline. Extracts attacker IPs from failed login lines, counts each, ranks by frequency. Core SOC one-liner.

```bash
grep -v "^#" /etc/ssh/sshd_config
```
Show only active SSH config lines. -v removes comment lines starting with #.

```bash
sudo journalctl | grep -A 3 -B 3 "Failed"
```
Show 3 lines before and after each failed login. Context for investigation.

### Security Angle — Dyn DDoS 2016
SOC analysts used grep-based log analysis to extract source IPs from firewall and DNS logs during the largest DDoS attack ever recorded at that time. Mirai botnet — hundreds of thousands of IoT devices. grep pipelines extracted attacker IPs and built block lists in real time while attack was ongoing.