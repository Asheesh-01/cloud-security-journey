# Day 41 — Labs: Why Hash Passwords

**Environment:** Kali Linux VM

---

## Lab 1 — Full Registration and Login Flow
```python
python3 -c "
import bcrypt
password = b'mySecurePassword123'
stored_hash = bcrypt.hashpw(password, bcrypt.gensalt(rounds=12))
print('Stored:', stored_hash.decode())
print('Correct login:', bcrypt.checkpw(b'mySecurePassword123', stored_hash))
print('Wrong password:', bcrypt.checkpw(b'wrongPassword', stored_hash))
print('Password never stored. No key exists. Nothing to steal.')
"
```
- `bcrypt.hashpw()` — hashes with random salt at work factor 12
- `bcrypt.checkpw()` — verifies without any decryption operation
- Correct password returns True. Wrong password returns False.

---

## Lab 2 — Encryption Failure Demo (Key Theft)
```python
python3 -c "
from cryptography.fernet import Fernet
key = Fernet.generate_key()
cipher = Fernet(key)
password = b'mySecurePassword123'
encrypted = cipher.encrypt(password)
print('Encrypted:', encrypted[:40].decode(), '...')
decrypted = cipher.decrypt(encrypted)
print('Decrypted with stolen key:', decrypted.decode())
print('One stolen key decrypts entire database instantly.')
"
```
- Fernet symmetric encryption — demonstrates reversibility
- `cipher.decrypt()` recovers original with key
- Key theft = instant exposure of all passwords simultaneously

---

## Lab 3 — Salt Makes Identical Passwords Unrelated
```python
python3 -c "
import bcrypt
password = b'password123'
h1 = bcrypt.hashpw(password, bcrypt.gensalt(rounds=12))
h2 = bcrypt.hashpw(password, bcrypt.gensalt(rounds=12))
h3 = bcrypt.hashpw(password, bcrypt.gensalt(rounds=12))
print('Same password, three different hashes:')
print(h1.decode())
print(h2.decode())
print(h3.decode())
"
```
- Same password + different random salt each time = completely different hashes
- No attacker can identify which users share a password
- Contrast with Adobe ECB where identical passwords had identical ciphertext

---

## Lab 4 — Work Factor Impact
```python
python3 -c "
import bcrypt, time
password = b'test'
for rounds in [4, 8, 10, 12]:
    start = time.time()
    bcrypt.hashpw(password, bcrypt.gensalt(rounds=rounds))
    elapsed = time.time() - start
    print(f'rounds={rounds}: {elapsed:.3f}s — {1/elapsed:.0f} attempts/second max')
"
```
- Lower rounds = faster = easier to brute force
- rounds=12: approximately 3 attempts/second = strong defense
- Never use rounds below 10 in production
- Increase rounds as hardware gets faster — target 100-500ms

---

## Lab 5 — Adobe ECB Pattern Visibility
```python
python3 -c "
import hashlib
users = [('alice','123456'),('bob','password'),('carol','123456'),('dave','letmein'),('eve','123456')]
print(f'{\"User\":<8} {\"ECB-style ciphertext\":<35}')
for user, pwd in users:
    print(f'{user:<8} {hashlib.md5(pwd.encode()).hexdigest():<35}')
print()
print('alice, carol, eve have identical ciphertext = same password revealed')
print('bcrypt: all three would have completely different hashes')
"
```
- Simulates Adobe ECB pattern — same input = same output
- Identical values immediately reveal which users share passwords
- bcrypt unique salt eliminates this pattern entirely

---

## Lab 6 — bcrypt Hash Format Breakdown
```python
python3 -c "
import bcrypt
stored = bcrypt.hashpw(b'securepassword', bcrypt.gensalt(rounds=12))
s = stored.decode()
print('Full hash:', s)
print()
print('Format: \$algorithm\$rounds\$salt+hash')
print('Self-contained — no external key needed for verification')
print('bcrypt.checkpw() reads everything it needs from this string')
"
```
- `$2b$` = bcrypt algorithm version
- `$12$` = work factor (2^12 iterations)
- Remaining 53 chars = 22-char salt + 31-char hash
- Everything needed for verification is embedded — no external dependencies