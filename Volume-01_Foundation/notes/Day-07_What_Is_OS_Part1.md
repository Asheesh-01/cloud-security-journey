# Day 07 — What is an Operating System (Part 1)
Volume 1 — Computer & Internet Foundations

## 🎯 Objective
Understand what an OS is, why it exists, what its 5 core jobs are, and what the kernel does — because everything in cloud security runs on an OS.

---

## 📘 Theory Notes

### 1️⃣ What is an Operating System

The OS is the middleman between you and the hardware.

Without an OS:
- CPU has no idea what to do with keyboard input
- RAM has no idea which program gets to use it
- Disk has no idea who is allowed to read which file

The OS takes all hardware complexity and hides it.
It gives every program a clean, simple way to use hardware without fighting over it.

Real analogy:
- Doctors = your programs (Chrome, terminal, Python)
- Hospital management = Operating System
- Rooms and equipment = hardware (CPU, RAM, disk)
- Without management = chaos, programs fighting over resources

Cloud Security connection:
- EC2 instance = Linux OS running on AWS hardware
- Malware = a process running inside an OS
- CloudTrail logs = OS-level events captured and sent to AWS
- Linux hardening = securing the OS that runs everything

---

### 2️⃣ Purpose of OS — 5 Core Jobs

| Job | What It Does |
|-----|-------------|
| Resource Management | Shares CPU, RAM, disk, network between all programs |
| Process Management | Creates, runs, pauses, resumes, kills processes |
| Memory Management | Gives each process its own RAM section, enforces isolation |
| File System Management | Organizes how data is stored and accessed on disk |
| Security & Access Control | Enforces who can do what — users, files, permissions |

Security relevance:
- Memory isolation = malware in one process cannot directly read another process's data
- Access control = foundation of all system and cloud security
- Process management = how you detect malware (abnormal processes)

---

### 3️⃣ Kernel

The Kernel is the core of the OS.
It is the only software with direct, unrestricted access to hardware.

Everything else asks the kernel. Kernel talks to hardware.

```
Your Programs (Chrome, terminal, VS Code)
          ↓  "please do this for me"
        KERNEL
          ↓  direct access
    HARDWARE (CPU, RAM, Disk, Network)
```

What the Kernel does:

| Function | Description |
|----------|-------------|
| Hardware communication | Only entity allowed to directly access hardware |
| Process scheduling | Manages all running processes, decides CPU time allocation |
| Memory isolation | Each process gets its own memory space, enforced by kernel |
| Privilege control | Controls sudo, root access, protected resources |

Context switching:
- System had 208 processes running on limited CPU cores
- Kernel switches between them thousands of times per second
- So fast it feels simultaneous — this is called context switching

Cloud Security relevance:

| Attack/Concept | Kernel Connection |
|----------------|------------------|
| Privilege escalation | Attacker exploits kernel vulnerability to get root |
| Rootkit | Malware that hides inside kernel — can hide files, processes, network connections |
| Container security | Docker containers share the same kernel — kernel compromise = all containers compromised |
| CVE-2022-0847 (Dirty Pipe) | Real 2022 kernel vulnerability — allowed unprivileged user to overwrite root files — AWS/GCP/Azure emergency patched entire infrastructure |

Key rule:
Unpatched kernel = known vulnerabilities still open.
Long uptime without restart = patches not applied.

---

## 🧠 Key Takeaways

- OS is the middleman between programs and hardware
- OS has 5 jobs: resource, process, memory, file system, access control
- Kernel is the core — only part with direct hardware access
- Everything else asks kernel via requests
- Kernel vulnerabilities are critical — they affect entire systems and cloud infrastructure
- Rootkits hide inside kernel — hardest malware to detect

---

## 🔜 Next Topic

User Space vs Kernel Space