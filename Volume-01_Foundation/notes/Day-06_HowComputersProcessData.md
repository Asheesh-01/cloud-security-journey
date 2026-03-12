# Day 06 — Topic 2: How Computers Process Data

**Date:** [12-03-26]
**Volume:** 01 — Computer and Internet Foundations
**GitHub:** github.com/Asheesh-01

---

## Why This Topic Exists

Everything a computer does — running Chrome, executing malware, processing a login request — comes down to one thing. The CPU processes data. To understand how attacks work at the deepest level you must understand how data is represented, moved, and processed inside the machine. Buffer overflows, fileless malware, Spectre — all of these make no sense without this foundation.

---

## Subtopic 1 — Binary Numbers

Computers use only 0 and 1 because transistors inside the CPU have two states — on and off. There is no middle state. So every number, every character, every instruction must be represented as a combination of 0s and 1s.

Each position in binary is a power of 2. Reading right to left: position 0 = 1, position 1 = 2, position 2 = 4, position 3 = 8, and so on.

To convert binary to decimal: identify which positions have a 1, add their values.

Example: 11001101
Position 7 = 128 (1)
Position 6 = 64  (1)
Position 5 = 32  (0)
Position 4 = 16  (0)
Position 3 = 8   (1)
Position 2 = 4   (1)
Position 1 = 2   (0)
Position 0 = 1   (1)
Total = 128 + 64 + 8 + 4 + 1 = 205

Security relevance: subnet masks are binary (255.255.255.0 = 11111111.11111111.11111111.00000000), Linux permissions use binary (rwx = 4+2+1), buffer overflow attacks exploit binary data boundaries.

---

## Subtopic 2 — Bits and Bytes

A bit is the smallest unit of data. It holds exactly one binary digit — 0 or 1.
A byte is 8 bits grouped together.
One byte can hold 256 different values — from 0 to 255 — because 2^8 = 256.

Storage units:
- 8 bits = 1 byte
- 1024 bytes = 1 KB (kilobyte) — because 2^10 = 1024
- 1024 KB = 1 MB (megabyte) — because 2^20 = 1,048,576
- 1024 MB = 1 GB (gigabyte) — because 2^30 = 1,073,741,824
- 1024 GB = 1 TB (terabyte) — because 2^40

Why 1024 and not 1000: computers work in binary. 2^10 = 1024 is close to 1000 so engineers borrowed the metric prefix kilo. Storage manufacturers use 1000 (decimal) to make drives look bigger. Your OS uses 1024 (binary). That is why a 1TB drive shows as ~931GB in your OS.

An IPv4 address is 32 bits. 2^32 = 4,294,967,296 possible addresses. IPv4 ran out because the number of internet-connected devices exceeded 4.3 billion. IPv6 uses 128 bits — 2^128 addresses — practically unlimited.

---

## Subtopic 3 — Number Systems

Three number systems used in computing:

Decimal — base 10, digits 0-9, what humans use naturally.
Binary — base 2, digits 0 and 1, what CPUs use internally.
Hexadecimal — base 16, digits 0-9 and A-F (A=10, B=11, C=12, D=13, E=14, F=15).

Hexadecimal exists because one byte = 8 bits = exactly 2 hex digits. Much easier to read than 8 binary digits. 11001101 in binary = CD in hex. Security work is full of hex — file hashes, memory addresses, MAC addresses, encryption keys.

Conversion rule: 4 binary digits = 1 hex digit. Always.

The 0x prefix means hexadecimal. 0b prefix means binary. No prefix means decimal.

Why hex(16) = 0x10: In decimal you run out of single digits at 9 and need two digits at 10. In hex you have 16 single digits (0 through F) so you do not need two digits until you reach sixteen. 0x10 means one group of sixteen plus zero remaining.

Little-endian byte order: your CPU stores the least significant byte first. This is why 205 stored in a 64-bit register appears as cd00000000000000 in hex — the cd (205) comes first, followed by seven empty bytes.

---

## Subtopic 4 — Character Encoding

Computers only understand numbers. Character encoding maps human characters to numbers so computers can store and display text.

ASCII: created 1963. Maps 128 characters to numbers 0-127. Covers English letters, digits, punctuation, control characters. Key values: A=65, a=97, 0=48, space=32. Gap between uppercase and lowercase is always 32.

UTF-8: created because ASCII only covers English. Supports over a million characters. Backwards compatible with ASCII — first 128 characters identical. English characters = 1 byte. French accented characters like é = 2 bytes. Hindi/Devanagari like अ = 3 bytes. Every modern system uses UTF-8.

Base64: not a character set — an encoding scheme. Represents binary data using 64 printable characters (A-Z, a-z, 0-9, +, /). Used when binary data must pass through text-only systems. NOT encryption. NOT security. Anyone can decode it instantly. Recognition pattern: string ends with = or ==.

Finding base64 in a log: decode it immediately. cGFzc3dvcmQ= decodes to "password" — a credential exposure finding that must be escalated.

Security relevance: attackers encode malicious commands in Base64 to bypass simple text scanners. SQL injection attacks use encoding to bypass WAF filters.

---

## Subtopic 5 — Instruction Cycle: Fetch, Decode, Execute

Every program is a list of instructions. The CPU processes them one by one in a fixed three-step cycle repeating billions of times per second.

Fetch: CPU reads the next instruction from RAM. The Program Counter (RIP register) holds the address of the next instruction. After fetching, Program Counter automatically advances to the next address.

Decode: The Control Unit translates the binary instruction into signals the CPU can act on. Determines what operation to perform and what data is needed.

Execute: The ALU carries out the operation. Result stored in a register. If it is a memory operation data is read or written to RAM.

Security relevance: buffer overflow attacks overwrite the return address on the stack so when a function finishes the CPU loads the attacker's address into RIP instead of the legitimate return address. The CPU then fetches and executes the attacker's code. Fileless malware injects instructions directly into a running process's memory — picked up during the Fetch step and executed as if legitimate.

---

## Subtopic 6 — CPU Registers

Registers are the fastest and smallest storage in the computer. Sit directly inside the CPU. The CPU cannot operate on RAM data directly — data must be loaded into a register first.

Speed comparison: registers = 1 cycle, L1 cache = 4 cycles, RAM = 200+ cycles.

Key registers on x86-64 (your Kali VM architecture):

RAX — accumulator. Stores function return values. Used for arithmetic.
RBX — base register. General purpose storage.
RCX — counter register. Used for loop counters.
RDX — data register. Used with RAX for large multiplication results.
RSI — source index. Points to source when copying memory.
RDI — destination index. Points to destination when copying. Holds first function argument.
RSP — stack pointer. Always points to top of the stack. Moves as data is pushed/popped.
RBP — base pointer. Points to base of current stack frame. Used to locate local variables.
RIP — instruction pointer = Program Counter. Holds address of next instruction to fetch. Most important register in security — whoever controls RIP controls what code executes next.
RFLAGS — stores comparison results. Controls conditional jumps (if statements).

Your machine: 64-bit CPU. Each register = 8 bytes = 64 bits. Confirmed by ctypes.sizeof(ctypes.c_long) = 8.

---

## Subtopic 7 — Cache Memory: L1, L2, L3

Cache is small fast memory between CPU and RAM. Stores copies of data the CPU is likely to need soon to avoid slow RAM access.

Memory hierarchy fastest to slowest:
- Registers: inside CPU, bytes, 1 cycle
- L1 Cache: inside CPU core, 48KB data + 64KB instruction, 4 cycles, private per core
- L2 Cache: inside CPU, 2048KB = 2MB, 12 cycles, private per core
- L3 Cache: on CPU chip, 18432KB = 18MB, 40 cycles, shared across all 18 cores
- RAM: separate chip, 16GB, 200+ cycles
- SSD: separate storage, 512GB, thousands of cycles

Cache hit: CPU finds data in cache — fast.
Cache miss: CPU must fetch from RAM — slow 200+ cycles.

Real cache sizes on this machine (Intel Core Ultra 5 125H):
L1 data: 48K
L1 instruction: 64K
L2: 2048K = 2MB
L3: 18432K = 18MB

Spectre (2018): exploits CPU's speculative execution and cache timing. CPU executes instructions before knowing if they are needed (performance optimisation). Attacker measures timing differences between cache hits and misses. From these timing measurements attacker reads protected memory — including passwords and encryption keys — without ever directly accessing it. Affected Intel, AMD, ARM — virtually every computer on the planet. Required OS-level patches that reduced performance by 5-30%.

Meltdown (2018): breaks isolation between user space and kernel space using speculative execution. Allows normal user program to read kernel memory. Also CVE-2018-3640/3639.

---

## Key Security Connections

- Binary → subnet masks, permissions, buffer overflow boundaries
- Hex → file hashes, memory addresses, malware analysis, MAC addresses
- Base64 → encoded malware C2 commands, JWT tokens, credential exposure in logs
- Instruction cycle → buffer overflow redirects RIP, fileless malware injects into Fetch step
- Registers → RIP control = code execution control = the goal of most exploitation
- Cache → Spectre timing attack reads protected memory through cache side channel