# Day 02 — Computer Fundamentals

## 🎯 Objective
Build a deep understanding of what a computer actually is before moving into OS, networking, and cloud security.

---

# 1️⃣ What Is a Computer?

A computer is an electronic machine that:
- Accepts input
- Stores data
- Processes data using instructions
- Produces output

Everything in cloud computing is built on this foundation.

Cloud truth:
Cloud = Someone else’s computer.

---

# 2️⃣ Data vs Information

## Data
Raw, unprocessed facts.
Examples:
- 10101010
- Log entries
- IP address
- Password attempt count

## Information
Processed data that has meaning.

Example:
Data: 50 failed login attempts  
Information: Brute-force attack detected

Cloud Security Mapping:
Logs = data  
Security alert = information  

Security is converting data → information → decision.

---

# 3️⃣ Hardware

Physical components of a computer.

### CPU
Brain of the system.
Executes instructions.

### RAM
Temporary working memory.
Volatile (lost when power off).

### Storage (SSD/HDD)
Permanent storage.
Stores OS, programs, files.

### Motherboard
Connects all components.

### Input Devices
Keyboard, mouse, network packets.

### Output Devices
Monitor, printer, server responses.

Security insight:
Hardware-level compromise (firmware attacks) bypasses OS protections.

---

# 4️⃣ Software

Instructions that tell hardware what to do.

### Types of Software

1. System Software
- Operating System
- Device Drivers

2. Application Software
- Browser
- Office
- Security tools

3. Firmware
- BIOS / UEFI

Important truth:
Software becomes real only when CPU executes it.

---

# 5️⃣ How Computer Actually Works (Basic Flow)

1. Input received
2. Data stored in RAM
3. CPU processes instructions
4. Output generated
5. Data saved to disk (if needed)

This cycle happens billions of times per second.

---

# 6️⃣ Why This Matters for Cloud Security

Cloud services like:
- EC2 → Virtual CPU & RAM
- S3 → Virtual storage
- IAM → Software-based access control

If you don’t understand hardware and software:
You cannot secure cloud infrastructure properly.

---

# 🧠 Key Takeaways

- Computer = Input + Processing + Storage + Output
- Data becomes information through analysis
- CPU executes instructions step-by-step
- RAM is temporary, disk is permanent
- Cloud resources are virtual computers

---

# 🔜 Next Topic
How Computers Process Data (Binary, Bits, Bytes, Number Systems)