# Day 34 — Username and Password: Why Insufficient Alone

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 16 — Authentication and Authorization
**Subtopic:** 2 — Username and Password

---

## What It Is

Username = identifier, not secret. Password = supposed proof of identity.
Only works when: secret, complex, unique, and stored securely (hashed).

## Key Terms

**Plaintext storage** — passwords stored as-is. Breach = instant exposure.
LinkedIn 2012: 117M passwords. RockYou 2009: 32M plaintext passwords.

**Password hashing** — one-way function. Only hash stored, never original.
Login: hash input, compare to stored hash. Match = authenticated.

**Brute force** — try every combination. GPU: billions of hashes/second.
8-character password crackable in hours with modern hardware.

**Dictionary attack** — wordlist of common/leaked passwords. rockyou.txt = 14M entries.

**Credential stuffing** — leaked username+password combos tried on other services.
Works because 60-65% of users reuse passwords.

**Password spraying** — one common password against many accounts.
Avoids lockout by staying under per-account threshold.

**Rainbow table** — precomputed hash-to-password lookup. Defeated by salting.

**Salt** — random value added to password before hashing.
Same password + different salt = different hash. Stored with hash.
Makes rainbow tables useless — each hash must be cracked individually.

**Have I Been Pwned** — haveibeenpwned.com — checks email/password against
known breach data. Troy Hunt. Free service.

## Why Passwords Fail

Humans choose predictably: common words, capitalized first letter,
numbers at end, special char before end.
Password reuse across sites: one breach = many accounts compromised.
Short length: 8 chars brute forced in hours. 16 chars: effectively safe.
rockyou.txt: 14M real passwords. Any match = instant crack.

## Real Breach — RockYou 2009

32 million passwords stored in plaintext. Entire DB dumped and published.
File: rockyou.txt — now standard penetration testing wordlist.
Shipped with Kali: /usr/share/wordlists/rockyou.txt.gz
Teaches: human password patterns are entirely predictable.
Fix: password managers (random unique passwords) + MFA (stolen password not enough).

## /etc/shadow Hash Format

$6$ = SHA-512. $y$ = yescrypt (modern Kali). $2b$ = bcrypt.
Format: $algorithm$salt$hash
Salt stored alongside hash — rainbow tables useless without matching salt.

## Commands

```bash
ls -lh /usr/share/wordlists/rockyou.txt.gz
sudo gunzip /usr/share/wordlists/rockyou.txt.gz 2>/dev/null || echo "Already decompressed"
ls -lh /usr/share/wordlists/rockyou.txt
head -20 /usr/share/wordlists/rockyou.txt
wc -l /usr/share/wordlists/rockyou.txt
grep -n "^password$" /usr/share/wordlists/rockyou.txt | head -5
echo -n "password" | md5sum
echo -n "password" | sha256sum
echo -n "passwordRANDOMSALT1" | sha256sum
echo -n "passwordRANDOMSALT2" | sha256sum
sudo head -3 /etc/shadow
```