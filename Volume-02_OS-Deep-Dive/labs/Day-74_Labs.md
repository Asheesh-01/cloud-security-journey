# Day 74 — Labs: Linux Navigation + File Operations + Viewing Content

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04
**All commands run in Kali terminal**

---

## Lab 1 — Print Current Location

```bash
pwd
```
- `pwd` — Print Working Directory — shows absolute path of where you are right now

---

## Lab 2 — List Files Basic and Detailed

```bash
ls
ls -l
ls -la
```
- `ls` — basic list, no details, no hidden files
- `-l` — long format, shows permissions, owner, size, date
- `-la` — long format plus all hidden files — use this during every investigation

---

## Lab 3 — List Specific Directory

```bash
ls -la /etc
```
- Lists /etc directory — system config files — attacker target
- Specify any path to list that directory without navigating into it

---

## Lab 4 — Navigate the File System

```bash
cd /etc
pwd
cd ..
pwd
cd ~
pwd
cd -
```
- `cd /etc` — move into /etc
- `pwd` — confirm location
- `cd ..` — move up one level
- `cd ~` — return home
- `cd -` — return to previous location

---

## Lab 5 — View Directory Tree

```bash
tree /etc -L 2
```
- `tree` — visual directory structure
- `-L 2` — two levels deep only

---

## Lab 6 — Create Investigation Directory Structure

```bash
mkdir ~/investigation
mkdir -p ~/investigation/logs/auth
ls -la ~/investigation/
```
- `mkdir` — create directory
- `-p` — create full path including intermediate directories
- Verify structure with ls -la

---

## Lab 7 — Create and Copy Files

```bash
touch ~/investigation/notes.txt
cp /var/log/auth.log ~/investigation/logs/auth/
ls -la ~/investigation/logs/auth/
```
- `touch` — create empty file
- `cp` — copy auth.log to investigation folder, original untouched
- Verify copy exists

---

## Lab 8 — Rename and Move Files

```bash
mv ~/investigation/notes.txt ~/investigation/case_notes.txt
ls -la ~/investigation/
```
- `mv` within same directory = rename
- Verify old name gone, new name exists

---

## Lab 9 — View File Content Methods

```bash
cat /etc/passwd
cat /etc/passwd | wc -l
head -n 20 /var/log/auth.log
tail -n 20 /var/log/auth.log
less /var/log/auth.log
```
- `cat` — full file dump
- `wc -l` — count lines
- `head -n 20` — first 20 lines
- `tail -n 20` — last 20 lines, most recent
- `less` — pager mode, q to quit

---

## Lab 10 — Real-Time Log Monitoring

```bash
tail -f /var/log/auth.log
```
Open new terminal, run any sudo command, watch it appear in the first terminal live.
Ctrl+C to stop following.

---

## Lab 11 — Live Filtered Monitoring

```bash
tail -f /var/log/auth.log | grep "Failed"
```
- Live feed filtered to failed authentication events only
- Try a wrong sudo password in another terminal and watch it appear

---

## Lab 12 — Clean Up

```bash
rm -rf ~/investigation/
ls ~/
```
- `rm -rf` — delete directory and all contents, no confirmation
- Always verify path before running
- Confirm investigation directory is gone