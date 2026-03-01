# Day 05 — How Computers Process Data
Volume 1 — Computer & Internet Foundations

## 🎯 Objective
Understand how computers represent everything as binary, how number systems work, and how characters are encoded — because every log, packet, and credential you will analyze as a cloud security engineer is just processed data at this level.

---

## 📘 Concepts Covered

### 1️⃣ Binary Numbers

Computers understand only two states:
- ON = 1
- OFF = 0

This is called **binary (Base 2)**.

Why binary?
Because electronic transistors have two stable states — on and off.
Building circuits around two states is reliable, fast, and cheap.

Everything stored anywhere — S3 bucket files, CloudTrail logs, IAM policy JSONs, RAM, disk — is ultimately binary underneath.

```
Example:
Letter "A" stored in memory = 01000001
Number 255 stored in memory = 11111111
```

Cloud Security Mapping:
- Every log entry you analyze is binary processed into readable text
- Every encrypted file is binary transformed using a key
- Every network packet is binary traveling across wires

---

### 2️⃣ Bits & Bytes

| Unit | Value |
|------|-------|
| 1 Bit | Single 0 or 1 |
| 8 Bits | 1 Byte |
| 1024 Bytes | 1 KB |
| 1024 KB | 1 MB |
| 1024 MB | 1 GB |

Why this matters in security:
- CloudTrail log files are measured in MB/GB
- RAM (where malware runs) is measured in GB
- Network traffic is measured in bytes per second
- File hashes (SHA-256) are 256 bits = 32 bytes

---

### 3️⃣ Number Systems

Three systems you will see constantly in security work:

| System | Base | Digits Used | Example |
|--------|------|-------------|---------|
| Binary | 2 | 0, 1 | 01000001 |
| Decimal | 10 | 0–9 | 65 |
| Hexadecimal | 16 | 0–9, A–F | 41 |

Why hex is everywhere in security:
- Compact representation of binary (4 bits = 1 hex char)
- Memory addresses: `0x7fff5fbff8a0`
- File hashes: `2cf24dba5fb0a30e26e83b2ac5b9e29e`
- Packet data in Wireshark
- Malware analysis output

```
Binary:    0100 0001
Hex:       4    1     →  41
Decimal:   65
Character: A
```

All four = same value. Different formats for different purposes.

---

### 4️⃣ Character Encoding

Computers store numbers, not letters.
So someone had to map numbers to characters.

**ASCII (American Standard Code for Information Interchange)**
- Original standard, 128 characters
- A = 65, B = 66, a = 97, space = 32, newline = 10

**UTF-8**
- Modern standard, supports every language
- Hindi, Arabic, Chinese, emojis — all supported
- Variable length: English = 1 byte per char, Hindi = 3 bytes per char
- Most used encoding on the web today

Security Relevance:
- Attackers use Unicode lookalike characters in phishing URLs
- Example: `pаypal.com` where `а` is Cyrillic (not Latin) — looks identical, different encoding
- Log parsers can be tricked by encoding tricks
- Understanding encoding helps you spot obfuscated malware

---

## 🔬 Practical Commands Performed

### Practical 1 — See Raw Bytes of a File

```bash
# Create a test file
echo "hello" > testfile.txt

# See raw hex bytes
xxd testfile.txt
```

Output:
```
00000000: 6865 6c6c 6f0a  hello.
```

- `68` = h, `65` = e, `6c` = l, `6c` = l, `6f` = o, `0a` = newline
- Left column = memory offset (address)
- Middle = hex values (raw data)
- Right = human-readable interpretation (information)

```bash
# Read as human-readable text
cat testfile.txt
```

Same bytes — now shown as readable text.
This is data → information in action.

---

### Practical 2 — Number System Conversions in Python

```bash
# Decimal to Binary
python3 -c "print(bin(65))"
# Output: 0b1000001

# Decimal to Hex
python3 -c "print(hex(65))"
# Output: 0x41

# Binary to Decimal
python3 -c "print(int('1000001', 2))"
# Output: 65

# What character is 65?
python3 -c "print(chr(65))"
# Output: A
```

Chain observed:
```
65 (decimal) = 41 (hex) = 1000001 (binary) = 'A' (character)
```

This is exactly how your computer stores the letter A in a file.

---

## ☁️ Cloud Security Connection

- Every CloudTrail log = text encoded in UTF-8 = bytes stored in S3
- Every IAM policy JSON = characters → ASCII/UTF-8 → binary on disk
- Every AWS Access Key = string of ASCII characters stored as bytes
- Stolen credentials travel as bytes across the network
- Hex used in memory forensics, packet analysis, hash verification

Understanding this level means you will never be confused when tools show you raw bytes, hex dumps, or encoded data.

---

## 🧠 Key Takeaways

- Computers only understand binary (0 and 1)
- 8 bits = 1 byte — the basic unit of all data
- Hex is compact binary — used everywhere in security tools
- ASCII and UTF-8 map numbers to characters
- The same value can look completely different in binary vs decimal vs hex
- Encoding tricks are used in real attacks (Unicode phishing, obfuscation)

---

## 🔜 Next Topic

Number system conversions deeper + Character encoding practical + Instruction Cycle