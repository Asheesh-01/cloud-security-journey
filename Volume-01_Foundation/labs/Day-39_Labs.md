# Day 39 — Labs: Hashing

**Environment:** Kali Linux VM

---

## Lab 1 — Compare Hash Algorithms
```bash
echo -n "password123" | md5sum
echo -n "password123" | sha1sum
echo -n "password123" | sha256sum
echo -n "password123" | sha512sum
```
- `echo -n` — no trailing newline, critical for correct hash
- Four algorithms, four different output lengths from identical input
- MD5: 32 hex chars. SHA-1: 40. SHA-256: 64. SHA-512: 128.

---

## Lab 2 — Avalanche Effect
```bash
echo -n "password123" | sha256sum
echo -n "password124" | sha256sum
```
- Single character difference (3→4)
- Output hashes share almost no bits
- Proves: cannot infer relationship between inputs from hash outputs

---

## Lab 3 — Determinism
```bash
echo -n "test" | sha256sum
echo -n "test" | sha256sum
echo -n "test" | sha256sum
```
- Three identical runs produce three identical outputs
- Determinism enables verification: hash stored input, hash provided input, compare

---

## Lab 4 — File Integrity with Hashing
```bash
echo "Important document contents" > /tmp/document.txt
sha256sum /tmp/document.txt > /tmp/document.sha256
echo "Important document contents TAMPERED" > /tmp/document.txt
sha256sum -c /tmp/document.sha256
```
- `sha256sum > file` — stores baseline hash
- After tampering, `-c` check outputs FAILED
- Integrity violation detected

---

## Lab 5 — Salted Hashing Demo
```bash
PASSWORD="password123"
SALT1=$(openssl rand -hex 16)
SALT2=$(openssl rand -hex 16)
echo -n "${PASSWORD}${SALT1}" | sha256sum
echo -n "${PASSWORD}${SALT2}" | sha256sum
```
- `openssl rand -hex 16` — 128-bit random salt
- Same password + different salts = completely different hashes
- Proves rainbow tables are useless against salted hashes

---

## Lab 6 — Linux Shadow Hash Format
```bash
openssl passwd -6 password123
openssl passwd -6 password123
```
- `openssl passwd -6` — generates SHA-512 crypt hash
- Run twice — different output each time due to random salt
- Format: `$6$salt$hash` — matches /etc/shadow format

---

## Lab 7 — bcrypt Hash and Verify
```bash
pip3 install bcrypt --break-system-packages -q
python3 -c "
import bcrypt
password = b'password123'
hashed = bcrypt.hashpw(password, bcrypt.gensalt(rounds=12))
print('Hash:', hashed.decode())
print('Verified:', bcrypt.checkpw(password, hashed))
print('Wrong rejected:', not bcrypt.checkpw(b'wrong', hashed))
"
```
- `bcrypt.gensalt(rounds=12)` — random salt with work factor 12
- `bcrypt.hashpw()` — hashes password with salt and work factor
- `bcrypt.checkpw()` — verifies without storing plaintext
- Returns True/False only — password never recoverable from hash

---

## Lab 8 — Why MD5 Is Broken for Passwords
```bash
echo -n "123456" | md5sum
echo -n "password" | md5sum
echo -n "qwerty" | md5sum
echo "These hashes appear in every rainbow table. Instant crack."
```
- These specific MD5 hashes are precomputed globally
- Any attacker who sees these hashes knows the passwords immediately
- LinkedIn stored 117M unsalted SHA-1 hashes — same problem, same result