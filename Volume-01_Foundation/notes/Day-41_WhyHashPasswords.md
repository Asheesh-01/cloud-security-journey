# Day 41 — Why Hash Passwords Instead of Encrypting Them

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 17 — Data Protection Basics
**Subtopic:** 4 — Why Hash Passwords

---

## The Core Question

Login only needs to VERIFY a password. Not RECOVER it.
If you only need to verify, you do not need reversibility.
If you do not need reversibility, do not use a reversible operation.

## Why Encryption Is Wrong for Passwords

Encryption requires a key. Key must be stored server-side.
Attacker dumps database → finds key → decrypts ALL passwords instantly.
One stolen key = entire database exposed simultaneously.
Key management problem: where to store, how to rotate, what if lost.

## Why Hashing Is Right for Passwords

No key exists. Nothing to steal that unlocks all at once.
Attacker gets hashes → must crack individually.
bcrypt work factor 12 → ~3 attempts/second → computationally expensive.
Cracking 1M hashes does not become easier than cracking 1 — linear cost.

## Correct Password Storage Flow

**Registration:**
1. User provides password
2. bcrypt.hashpw(password, gensalt(rounds=12))
3. Store returned hash string — password never stored
4. Hash is self-contained: $2b$12$[salt][hash]

**Login:**
1. User provides password
2. Retrieve stored hash from database
3. bcrypt.checkpw(provided_password, stored_hash)
4. Returns True or False — no decryption, no key

**Password reset:**
Send reset link with time-limited token — NEVER email existing password.
App that emails existing password = stored reversibly = critical finding.

## Red Flags

App emails existing password → plaintext or encrypted storage.
Password reminder shows actual password → same problem.
Password has maximum length < 72 chars → may not be hashed.
App says password is "too long" → likely stored in fixed plaintext field.

## Real Breach — Adobe 2013

153M records. 3DES-ECB encryption. Plaintext hints column.
ECB mode: same input = same ciphertext.
Attackers saw all users sharing a password — identical ciphertext.
Hints column revealed passwords directly for obvious hints.
Researchers determined most common password without cracking anything.
bcrypt with unique random salt: identical passwords = unrelated hashes.
No pattern visible. No bulk exposure from single mode failure.

## bcrypt Hash Format

`$2b$12$[22 chars salt][31 chars hash]`
Algorithm ($2b$) + work factor ($12$) + salt + hash in one string.
Self-contained — no external key or config needed for verification.

## When to Encrypt Credentials (Not User Passwords)

API keys, OAuth tokens, third-party service passwords your app must use.
These must be recovered in plaintext to send to another service.
Use: proper secrets manager (AWS Secrets Manager, HashiCorp Vault).
Never hardcode the encryption key. Rotate it. Log every decryption.
This is different from user passwords — which must always be hashed.

## Commands

```bash
python3 -c "import bcrypt; ..."    # full registration and login cycle
python3 -c "from cryptography.fernet import Fernet; ..."  # encryption failure demo
python3 -c "import bcrypt; ..."    # same password, different hashes (salt effect)
python3 -c "import bcrypt, time; ..."  # work factor impact on speed
python3 -c "import hashlib; ..."   # Adobe ECB pattern visibility demo
```