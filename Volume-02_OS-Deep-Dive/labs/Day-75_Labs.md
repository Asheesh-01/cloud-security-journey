# Day 75 — Labs: Finding Things + Text Searching

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Lab 1 — Find Shell Scripts on System

```bash
find / -name "*.sh" 2>/dev/null
```
- `find /` — search entire filesystem from root
- `-name "*.sh"` — match filenames ending in .sh
- `2>/dev/null` — discard permission denied errors

---

## Lab 2 — Find Files in Attacker Staging Areas

```bash
find /tmp /var/tmp /dev/shm -type f 2>/dev/null
```
- Three world-writable directories searched simultaneously
- `-type f` — regular files only
- Any executable file here = suspicious

---

## Lab 3 — Find All SUID Binaries

```bash
find / -perm -4000 -type f 2>/dev/null
```
- `-perm -4000` — SUID bit set
- Note all results — these are your privilege escalation candidates

---

## Lab 4 — Find SUID Binaries Outside Standard Paths

```bash
find / -perm -4000 -type f 2>/dev/null | grep -v "^/usr\|^/bin\|^/sbin"
```
- Filters out standard system paths
- Any remaining result = non-standard SUID binary = investigate immediately

---

## Lab 5 — Find Recently Modified Files

```bash
find / -mtime -1 -type f 2>/dev/null | head -30
```
- `-mtime -1` — modified in last 24 hours
- `| head -30` — first 30 results only to keep output manageable

---

## Lab 6 — Find Hidden Files in Home Directories

```bash
find /home -name ".*" -type f 2>/dev/null
```
- `-name ".*"` — filenames starting with dot = hidden files
- Attacker persistence location

---

## Lab 7 — Use locate and which

```bash
sudo updatedb
locate passwd
which python3
which nc
echo $PATH
```
- `updatedb` — refresh locate database
- `locate passwd` — fast search for passwd-related files
- `which python3` — which binary runs when you type python3
- `which nc` — check if netcat installed and where
- `echo $PATH` — inspect PATH for unexpected directories

---

## Lab 8 — Basic grep on Journal

```bash
sudo journalctl | grep "Failed"
sudo journalctl | grep -i "failed password"
sudo journalctl | grep -c "Failed"
```
- Basic failed auth search
- Case insensitive version
- Count of failed events

---

## Lab 9 — Search Config Files for Passwords

```bash
grep -ri "password" /etc 2>/dev/null | head -20
grep -rn "password" /etc 2>/dev/null -l
```
- Recursive case-insensitive search for passwords in /etc
- List filenames only version

---

## Lab 10 — IP Extraction Pipeline

```bash
sudo journalctl | grep "Failed" | grep -o '[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+' | sort | uniq -c | sort -rn | head -20
```
- Full attacker IP extraction and ranking pipeline
- Core SOC one-liner — memorize this

---

## Lab 11 — Clean SSH Config View

```bash
grep -v "^#" /etc/ssh/sshd_config
```
- Shows active SSH config lines only
- -v removes comment lines