# Day 38 — Encryption

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 17 — Data Protection Basics
**Subtopic:** 1 — Encryption

---

## What It Is

Scrambles plaintext into ciphertext using a key and algorithm.
Reversible with the correct key. Protects confidentiality.
Key is secret. Algorithm is public (Kerckhoffs's principle).

## Key Terms

**Plaintext** — original unencrypted data.
**Ciphertext** — encrypted output. Unreadable without key.
**Key** — secret value used to encrypt and decrypt. Entire security depends on this.
**Algorithm** — public mathematical procedure. AES, RSA, ChaCha20.
**IV** — Initialization Vector. Random value ensuring same plaintext
  encrypted twice produces different ciphertext.

## Symmetric Encryption

Same key encrypts and decrypts.
Fast — suitable for large data.
Problem: secure key sharing required before communication.
Algorithm: AES (Advanced Encryption Standard).
AES-256 = 256-bit key = current standard.
Use for: disk encryption, database fields, backups, bulk data.

## Asymmetric Encryption

Two mathematically linked keys: public and private.
Public key encrypts. Private key decrypts.
Public key — share openly. Private key — never share.
Solves key sharing problem. Slower than symmetric.
Algorithms: RSA, ECC (Elliptic Curve).
RSA-2048 minimum. RSA-4096 for long-term security.
ECDSA-256 ≈ RSA-3072 security at smaller key size.
Use for: key exchange, digital signatures, certificate auth.

## Hybrid (TLS)

Asymmetric for key exchange → symmetric for data transfer.
Security of asymmetric + speed of symmetric.
How HTTPS works under the hood.

## Encryption Types

**At rest** — stored data. BitLocker, LUKS, FileVault, S3 SSE.
**In transit** — moving data. TLS/HTTPS.
**End-to-end (E2EE)** — provider cannot read. Signal, WhatsApp.
Both at rest AND in transit required. One without the other leaves gap.

## Real Breach — Ashley Madison 2015

37 million users. Affair dating site. Impact Team breach.
Full delete feature ($19) did not work — data remained.
Payment records with billing addresses inadequately protected.
Users identified from payment data even with fake email addresses.
Human consequence: multiple suicides attributed to breach.
Lesson: data minimization is as powerful as encryption.
Never collect data you do not need. Breach cannot expose what was never stored.

## Commands

```bash
openssl version -a
openssl rand -hex 32                                     # random AES-256 key
openssl enc -aes-256-cbc -salt -pbkdf2 -in plaintext.txt -out encrypted.bin -pass pass:Password
cat encrypted.bin && xxd encrypted.bin | head -5         # verify unreadable
openssl enc -aes-256-cbc -d -salt -pbkdf2 -in encrypted.bin -out decrypted.txt -pass pass:Password
openssl genrsa -out private_key.pem 2048                 # RSA key pair
openssl rsa -in private_key.pem -pubout -out public_key.pem
openssl pkeyutl -encrypt -inkey public_key.pem -pubin -in message.txt -out encrypted.bin
openssl pkeyutl -decrypt -inkey private_key.pem -in encrypted.bin -out decrypted.txt
```