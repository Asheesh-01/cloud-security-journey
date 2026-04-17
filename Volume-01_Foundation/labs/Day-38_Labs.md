# Day 38 — Labs: Encryption

**Environment:** Kali Linux VM

---

## Lab 1 — Check OpenSSL
```bash
openssl version
openssl version -a
```
- `openssl` — command-line cryptography toolkit
- `version` — installed version
- `-a` — all details including build date and options

---

## Lab 2 — Generate Random Key
```bash
openssl rand -hex 32
```
- `rand` — random byte generator
- `-hex` — hexadecimal output
- `32` — 32 bytes = 256 bits = AES-256 key length
- Output shows what a proper random encryption key looks like

---

## Lab 3 — Encrypt File with AES-256
```bash
echo "This is sensitive data that needs protecting" > /tmp/plaintext.txt
openssl enc -aes-256-cbc -salt -pbkdf2 -in /tmp/plaintext.txt -out /tmp/encrypted.bin -pass pass:TestPassword123
```
- `enc` — encryption subcommand
- `-aes-256-cbc` — AES algorithm, 256-bit key, CBC mode
- `-salt` — random salt added to password-based key derivation
- `-pbkdf2` — modern secure key derivation function
- `-in` / `-out` — input and output file paths
- `-pass pass:` — password to derive key from

---

## Lab 4 — Verify Ciphertext Is Unreadable
```bash
cat /tmp/encrypted.bin
xxd /tmp/encrypted.bin | head -5
```
- `cat` — shows binary noise — confirms encryption
- `xxd` — hex dump of raw bytes
- `head -5` — first 5 lines only
- Proves original data is unrecoverable without the key

---

## Lab 5 — Decrypt File
```bash
openssl enc -aes-256-cbc -d -salt -pbkdf2 -in /tmp/encrypted.bin -out /tmp/decrypted.txt -pass pass:TestPassword123
cat /tmp/decrypted.txt
```
- `-d` — decrypt mode
- Same algorithm, salt, pbkdf2, and password required
- `cat` confirms original plaintext is fully recovered

---

## Lab 6 — Generate RSA Key Pair
```bash
openssl genrsa -out /tmp/private_key.pem 2048
openssl rsa -in /tmp/private_key.pem -pubout -out /tmp/public_key.pem
cat /tmp/public_key.pem
```
- `genrsa` — generate RSA private key
- `2048` — key length in bits (minimum acceptable)
- `rsa -pubout` — extract public key from private key
- Public key is safe to share. Private key must be kept secret.

---

## Lab 7 — Encrypt and Decrypt with RSA
```bash
echo "Secret message" > /tmp/message.txt
openssl pkeyutl -encrypt -inkey /tmp/public_key.pem -pubin -in /tmp/message.txt -out /tmp/rsa_encrypted.bin
openssl pkeyutl -decrypt -inkey /tmp/private_key.pem -in /tmp/rsa_encrypted.bin -out /tmp/rsa_decrypted.txt
cat /tmp/rsa_decrypted.txt
```
- `pkeyutl -encrypt` — encrypt with public key
- `-pubin` — input key is a public key
- `pkeyutl -decrypt` — decrypt with private key
- Full asymmetric encryption cycle demonstrated

---

## Lab 8 — Clean Up
```bash
rm /tmp/plaintext.txt /tmp/encrypted.bin /tmp/decrypted.txt
rm /tmp/private_key.pem /tmp/public_key.pem
rm /tmp/message.txt /tmp/rsa_encrypted.bin /tmp/rsa_decrypted.txt 2>/dev/null
```
- Always delete temporary key and encrypted files
- Private keys remaining on disk = permanent security risk
