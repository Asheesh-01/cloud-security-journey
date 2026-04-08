# Day 28 — Labs: Integrity

**Environment:** Kali Linux VM

---

## Lab 1 — Create File and Baseline Hash
```bash
echo "this is my original data" > /tmp/testfile.txt
sha256sum /tmp/testfile.txt
```
- `echo "..." > file` — creates file with specified content
- `sha256sum` — computes SHA-256 hash of the file
- Output format: 64-character hash followed by filename
- This hash is your integrity baseline — save it

---

## Lab 2 — Tamper and Detect Change
```bash
echo "this is MODIFIED data" > /tmp/testfile.txt
sha256sum /tmp/testfile.txt
```
- Overwrites file with different content
- Compare hash output with Lab 1 — completely different despite similar text
- Proves: any modification produces a completely different hash

---

## Lab 3 — Store Hash and Verify
```bash
echo "this is my original data" > /tmp/testfile.txt
sha256sum /tmp/testfile.txt > /tmp/testfile.sha256
cat /tmp/testfile.sha256
sha256sum -c /tmp/testfile.sha256
```
- `> /tmp/testfile.sha256` — saves hash to a file
- `sha256sum -c` — check mode. Reads stored hash, recomputes, compares.
- Output: `testfile.txt: OK` when file is unmodified

---

## Lab 4 — Tamper and Fail Verification
```bash
echo "tampered content" > /tmp/testfile.txt
sha256sum -c /tmp/testfile.sha256
```
- Modifies the file after hash was stored
- `sha256sum -c` detects the mismatch
- Output: `testfile.txt: FAILED` — integrity violation detected

---

## Lab 5 — Baseline System Binaries
```bash
sha256sum /bin/ls
sha256sum /bin/bash
sha256sum /usr/bin/python3
```
- Records hashes of critical system binaries
- Store these hashes securely off the system
- If malware replaces any binary, hash comparison reveals it
- This is manual File Integrity Monitoring

---

## Lab 6 — Compare Hashing Algorithms
```bash
echo "test" | md5sum
echo "test" | sha1sum
echo "test" | sha256sum
echo "test" | sha512sum
```
- `echo "test" |` — pipes the string "test" as input to each tool
- `md5sum` — 128-bit output, 32 hex chars. Cryptographically broken.
- `sha1sum` — 160-bit output, 40 hex chars. Deprecated for security use.
- `sha256sum` — 256-bit output, 64 hex chars. Current standard.
- `sha512sum` — 512-bit output, 128 hex chars. Higher security margin.
- Same input, same algorithm always produces identical output