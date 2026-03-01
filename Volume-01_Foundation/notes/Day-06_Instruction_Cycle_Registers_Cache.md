# Day 06 — Instruction Cycle, Registers & Cache Memory
Volume 1 — Computer & Internet Foundations

## 🎯 Objective
Understand how a CPU actually executes instructions, what registers are, and how cache memory works — because malware lives in memory, exploits manipulate registers, and cache-based attacks are real.

---

## 📘 Theory Notes

### 1️⃣ Instruction Cycle

The CPU works in a continuous loop called the **Instruction Cycle** (also called Fetch-Decode-Execute cycle).

Every program you run — every command, every malware, every cloud API call at the OS level — follows this cycle.

```
FETCH → DECODE → EXECUTE → STORE
```

**Step by step:**

| Step | What Happens |
|------|-------------|
| FETCH | CPU gets the next instruction from RAM |
| DECODE | CPU figures out what that instruction means |
| EXECUTE | CPU performs the actual operation (add, move, compare) |
| STORE | Result is written back to memory or register |

This repeats **billions of times per second**.

**Real example — when you run `cat testfile.txt`:**
1. FETCH — CPU fetches the `cat` program instruction from RAM
2. DECODE — CPU understands "open file, read bytes"
3. EXECUTE — CPU reads the bytes from disk into memory
4. STORE — Output stored temporarily, sent to terminal

**Cloud Security Relevance:**
- Malware follows this exact cycle when it executes
- Buffer overflow attacks corrupt the FETCH step by hijacking memory addresses
- Memory forensics = capturing what instructions were being executed in RAM
- Fileless malware lives entirely in this cycle — never touches disk

---

### 2️⃣ Registers

Registers are **tiny, ultra-fast storage locations inside the CPU itself**.

Think of it like this:
- Disk = warehouse (slow, huge)
- RAM = desk (medium speed, medium size)
- Cache = shelf next to you (fast, small)
- Registers = your hands (fastest, holds only what you're using right now)

**Key registers (you will see these in forensics and exploit analysis):**

| Register | Purpose |
|----------|---------|
| Program Counter (PC) | Holds address of NEXT instruction to fetch |
| Instruction Register (IR) | Holds the CURRENT instruction being decoded |
| Accumulator | Holds result of arithmetic/logic operations |
| Stack Pointer (SP) | Points to top of the stack in memory |
| General Purpose Registers | Temporary storage during execution (EAX, EBX on x86) |

**Size:** Registers hold only a few bytes — 8, 16, 32, or 64 bits depending on architecture.

**Cloud Security Relevance:**
- Buffer overflow exploits overwrite the **Stack Pointer** and **Program Counter** to redirect execution to malicious code
- When you analyze malware with a debugger — you watch register values change
- Exploit code like shellcode manipulates registers directly
- In memory forensics reports you will see register states captured at time of crash or attack

---

### 3️⃣ Cache Memory

Cache is **fast memory sitting between the CPU and RAM**.

Problem: RAM is slow compared to CPU speed. CPU can execute billions of operations per second but RAM access takes hundreds of nanoseconds. That creates a bottleneck.

Solution: Cache stores frequently used data closer to the CPU so it doesn't have to wait for RAM.

**Cache Levels:**

| Level | Speed | Size | Location |
|-------|-------|------|----------|
| L1 Cache | Fastest | 32–128 KB | Inside CPU core |
| L2 Cache | Fast | 256 KB – 1 MB | Inside CPU (near core) |
| L3 Cache | Medium-fast | 4–32 MB | Shared across CPU cores |
| RAM | Slower | 8–64 GB | Outside CPU on motherboard |

**How it works:**
1. CPU needs data → checks L1 first (cache hit = fast)
2. Not in L1 → checks L2 (slightly slower)
3. Not in L2 → checks L3
4. Not in L3 → fetches from RAM (cache miss = slower)
5. Fetched data is stored in cache for next time

**Cloud Security Relevance:**
- **Spectre and Meltdown (2018)** — real-world attacks that exploited CPU cache behavior to steal data from other processes including cloud VMs
- Cache side-channel attacks measure timing of cache hits/misses to leak secret data (passwords, encryption keys)
- Even cloud VMs on shared physical servers can be attacked through cache timing
- This is why AWS and other providers rapidly patched their entire global infrastructure in 2018

---

## 🧠 Key Takeaways

- CPU executes via Fetch → Decode → Execute → Store loop, billions of times per second
- Registers are fastest storage, hold only active data, targeted by exploits
- Cache sits between CPU and RAM to reduce speed bottleneck
- Memory hierarchy: Registers > L1 > L2 > L3 > RAM > Disk (speed order)
- Real attacks (Spectre, Meltdown, buffer overflows) target these exact components
- Malware lives in RAM and executes through this same instruction cycle

---

## 🔜 Next Topic

What is an Operating System — Kernel, User Space, Processes, Memory Management