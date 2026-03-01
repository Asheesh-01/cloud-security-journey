# Day 04 — Core Hardware & Firmware
Volume 1 — Computer & Internet Foundations

## 🎯 Objective
Understand the physical and low-level components that power every computer before diving deeper into OS and cloud systems.

---

# 1️⃣ CPU (Central Processing Unit)

Definition:
The brain of the computer that executes instructions.

Key Functions:
- Arithmetic & logical operations
- Instruction execution cycle
- Process control

Security Relevance:
- Malware ultimately executes on CPU
- EDR tools monitor CPU-level behavior
- Cryptographic operations use CPU instructions

Cloud Mapping:
EC2 instance types define virtual CPU (vCPU).

---

# 2️⃣ RAM (Random Access Memory)

Definition:
Temporary working memory used by running programs.

Characteristics:
- Volatile (data lost when power off)
- Very fast
- Stores active processes

Security Relevance:
- Memory-based attacks
- In-memory malware
- Credential dumping from RAM

Cloud Mapping:
Instance memory size directly impacts performance & cost.

---

# 3️⃣ Disk (Storage)

Definition:
Permanent storage device (SSD/HDD).

Stores:
- OS
- Applications
- Logs
- User data

Security Relevance:
- Disk encryption prevents data theft
- Logs stored on disk enable investigations
- Misconfigured storage causes data breaches

Cloud Mapping:
EBS volumes, S3 buckets.

---

# 4️⃣ GPU (Graphics Processing Unit)

Definition:
Processor specialized for parallel computation.

Uses:
- Graphics rendering
- AI/ML workloads
- High-performance computing

Security Insight:
- Used in password cracking
- Cloud GPU instances available

Cloud Mapping:
AWS GPU-based EC2 instances.

---

# 5️⃣ Motherboard

Definition:
Main circuit board connecting all hardware components.

Functions:
- Power distribution
- Communication between CPU, RAM, Disk
- Houses firmware chip

Security Relevance:
- Firmware attacks
- Hardware-level persistence

---

# 6️⃣ BIOS / UEFI

Definition:
Firmware that starts the system before OS loads.

Boot Flow:
Power On → Firmware → Bootloader → Kernel → OS Services

Functions:
- Hardware initialization
- POST checks
- Loads bootloader

Security Relevance:
- Secure Boot prevents tampering
- UEFI rootkits exist
- Firmware compromise bypasses OS security

---

# ☁ Cloud Security Connection

Cloud servers are still physical machines.

Even virtual instances follow the same logic:
Hardware → Firmware → Bootloader → Kernel → Services

Understanding hardware builds deeper cloud confidence.

---

## 🧠 Key Takeaways

- CPU executes everything
- RAM holds active data
- Disk stores persistent data
- GPU handles parallel workloads
- Firmware runs before OS
- Security starts below the operating system

---

## 🔜 Next Topic

How Computers Process Data (Binary, Bits, Bytes, Instruction Cycle)