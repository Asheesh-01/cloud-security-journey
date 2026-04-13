# Day 34 — Labs: Username and Password

**Environment:** Kali Linux VM

---

## Lab 1 — Locate RockYou Wordlist
```bash
ls -lh /usr/share/wordlists/rockyou.txt.gz
```
- `ls -lh` — shows file size in human-readable format
- rockyou.txt.gz — compressed wordlist shipped with Kali
- 134MB compressed, 139MB uncompressed, 14 million+ passwords

---

## Lab 2 — Decompress Wordlist
```bash
sudo gunzip /usr/share/wordlists/rockyou.txt.gz 2>/dev/null || echo "Already decompressed"
ls -lh /usr/share/wordlists/rockyou.txt
```
- `gunzip` — decompresses .gz file in place, removes compressed version
- `2>/dev/null` — suppress error if already decompressed
- `||` — if gunzip fails, print alternate message

---

## Lab 3 — View Top Passwords
```bash
head -20 /usr/share/wordlists/rockyou.txt
```
- `head -20` — first 20 lines = 20 most common real passwords
- These are tried first in every dictionary attack
- Seeing them explains why humans are the weakest authentication link

---

## Lab 4 — Count Total Entries
```bash
wc -l /usr/share/wordlists/rockyou.txt
```
- `wc -l` — word count, lines only = number of passwords
- Result: 14 million+ real passwords from real accounts

---

## Lab 5 — Search for Specific Password
```bash
grep -n "^password$" /usr/share/wordlists/rockyou.txt | head -5
grep -n "^123456$" /usr/share/wordlists/rockyou.txt | head -5
```
- `grep -n` — show line number of matches
- `^password$` — exact match: ^ = line start, $ = line end
- Line number = frequency rank — lower number = more common

---

## Lab 6 — Hash Common Passwords
```bash
echo -n "password" | md5sum
echo -n "password123" | md5sum
echo -n "123456" | md5sum
```
- `echo -n` — output without trailing newline (critical for correct hashing)
- `| md5sum` — compute MD5 hash
- These hashes appear instantly crackable against rockyou.txt

---

## Lab 7 — Demonstrate Salt Effect
```bash
echo -n "password" | sha256sum
echo -n "passwordRANDOMSALT1" | sha256sum
echo -n "passwordRANDOMSALT2" | sha256sum
```
- Same base password, different salts = completely different hashes
- Rainbow table for "password" hash is useless when salt is unknown
- Each salted hash must be cracked individually — costly at scale

---

## Lab 8 — View Shadow File Hash Format
```bash
sudo head -3 /etc/shadow
```
- Shows real Linux password hash format
- `$6$` = SHA-512, `$y$` = yescrypt, `$2b$` = bcrypt
- Format: `$algorithm$salt$hash`
- Salt embedded in the field — proper secure storage demonstrated