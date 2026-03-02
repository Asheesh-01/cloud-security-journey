# Day 08 — What is an Operating System (Part 2)
Volume 1 — Computer & Internet Foundations

## 🎯 Objective
Understand how OS separates user programs from the kernel using two memory zones, and how programs communicate with hardware through system calls.

---

## 📘 Theory Notes

### 1️⃣ User Space vs Kernel Space

The CPU divides memory into two protected zones.

**Kernel Space:**
- Protected memory zone where only the kernel runs
- Direct hardware access, no restrictions
- If something crashes here — entire system crashes
- This is why kernel bugs are so critical

**User Space:**
- Everything else runs here — terminal, Chrome, Python, malware
- Cannot touch hardware directly
- Cannot access kernel memory
- Must send requests to kernel to get anything done

```
┌─────────────────────────────┐
│         USER SPACE          │
│  Chrome  Terminal  Python   │
│  Malware  VS Code  SSH      │
├─────────────────────────────┤
│        KERNEL SPACE         │
│  Process mgmt  Memory mgmt  │
│  Device drivers  Scheduler  │
├─────────────────────────────┤
│          HARDWARE           │
│    CPU    RAM    Disk       │
└─────────────────────────────┘
```

---

### 2️⃣ System Calls (Syscalls)

The only way user space programs communicate with kernel space.

How it works when Python opens a file:
1. Python calls open() function
2. That triggers a syscall — formal request to kernel
3. CPU switches from user mode → kernel mode
4. Kernel handles the file access
5. Result returned to Python
6. CPU switches back to user mode

This crossing happens thousands of times per second — invisibly.

Result from lab:
Even a simple `ls` command makes **104 syscalls**.
104 user space → kernel space crossings just to list a directory.

---

### 3️⃣ CPU Privilege Rings

The separation is enforced by the CPU chip itself — not just software.

| Ring | Name | Who Uses It |
|------|------|-------------|
| Ring 0 | Kernel mode | OS Kernel only — full trust, full access |
| Ring 3 | User mode | All user programs — restricted |

Ring 0 = complete hardware control.
Ring 3 = must ask Ring 0 for everything.

---

### 4️⃣ Security Relevance

**Isolation:**
Malware running in user space cannot directly read kernel memory.
Cannot touch other processes' memory.
Kernel enforces hard boundaries at hardware level.

**Privilege Escalation:**
Attacker starts in user space (Ring 3).
Exploits a kernel vulnerability.
Jumps to kernel space (Ring 0).
Now has full system control — game over.
This is exactly what CVE-2022-0847 (Dirty Pipe) allowed.

**Container Escape:**
Docker containers run in user space.
A container escape attack breaks out of user space isolation.
Reaches kernel space or other containers.
Critical cloud security concern — one container compromising entire host.

**Why strace matters in security:**
Security engineers use strace to:
- Analyze what system calls malware is making
- Detect suspicious kernel requests
- Understand what a program is actually doing under the hood

---

## 🧠 Key Takeaways

- OS divides memory into User Space and Kernel Space
- User space programs cannot touch hardware or kernel memory directly
- System calls are the only legal crossing point between the two zones
- CPU enforces this via hardware rings — Ring 0 (kernel) vs Ring 3 (user)
- Simple `ls` makes 104 syscalls — every one crosses the boundary
- Privilege escalation = jumping from Ring 3 to Ring 0 illegally
- Container escape = breaking user space isolation in cloud environments

---

## 🔜 Next Topic

Processes