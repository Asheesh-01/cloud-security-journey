# Day 39 — Hashing

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 17 — Data Protection Basics
**Subtopic:** 2 — Hashing

---

## What It Is

One-way transformation of any input to fixed-size output.
Deterministic: same input = same output always.
Irreversible: cannot recover input from output.
Protects integrity and enables password verification without storing passwords.

## Key Terms

**Hash / Digest** — fixed-size output of hash function.
**Collision** — two inputs produce same hash. Broken if findable.
**Preimage resistance** — cannot find input from hash. What makes it one-way.
**Avalanche effect** — one bit change in input → ~50% of output bits change.
**Salt** — random value added per user before hashing. Defeats rainbow tables.
**Work factor** — bcrypt/Argon2 parameter controlling computation cost.
**Rainbow table** — precomputed hash-to-password lookup. Defeated by salting.
**HMAC** — hash + secret key = proves integrity AND authenticity.
  HS256 in JWT = HMAC-SHA256.

## Algorithm Status

| Algorithm | Bits | Status | Use |
|---|---|---|---|
| MD5 | 128 | Broken | File checksums only |
| SHA-1 | 160 | Broken | Deprecated |
| SHA-256 | 256 | Secure | Integrity, signatures |
| SHA-512 | 512 | Secure | High security integrity |
| bcrypt | 184 | Secure | Password hashing |
| Argon2id | Variable | Secure | Password hashing — best practice |

## Hashing vs Encryption

Hashing: one-way. Use when you never need original data — only verification.
Encryption: reversible with key. Use when you need to recover original data.

Password storage: hash (bcrypt/Argon2id). Never encrypt passwords.
If encryption key compromised → all passwords readable instantly.
If bcrypt hashes compromised → each must be cracked individually at work factor cost.

## Real Breach — LinkedIn 2012

117 million password hashes leaked. SHA-1, no salt.
SHA-1 = billions of hashes/second on modern hardware.
No salt = rainbow table cracked common passwords instantly.
No salt = cracking one instance of "password123" cracked ALL accounts using it.
~90% of hashes cracked within days of leak.
Fix after breach: salted bcrypt. Six years too late.

## Commands

```bash
echo -n "password123" | md5sum          # 128-bit, broken
echo -n "password123" | sha256sum       # 256-bit, secure
echo -n "password123" | sha512sum       # 512-bit, secure
echo -n "password124" | sha256sum       # avalanche effect demo
openssl dgst -sha256 /tmp/file          # hash a file
openssl passwd -6 password123           # SHA-512 crypt (shadow format)
python3 -c "import bcrypt; ..."         # bcrypt hash + verify
```