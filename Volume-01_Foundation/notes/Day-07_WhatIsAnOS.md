# Day 07 — Topic 3: What is an Operating System (Subtopics 1-3)

**Date:** Day 07 of Cloud Security Journey
**Volume:** 01 — Computer and Internet Foundations
**GitHub:** github.com/Asheesh-01

---

## Topic Overview

The OS is the manager between hardware and applications. Without it every application would need to directly control the CPU, manage RAM, and talk to hardware — impossible to maintain. The OS abstracts all hardware and provides services to applications through one core component — the Kernel.

Security relevance: the OS is the most powerful software on the machine. Every privilege escalation attack, every rootkit, every fileless malware — the goal is always to reach OS level or below it. Understanding what the OS controls tells you exactly what an attacker wants to own.

---

## Subtopic 1 — Purpose of an Operating System

The Operating System is software that manages all hardware resources and provides services to applications. It sits between hardware and every program you run.

Three problems it solves:

**Resource management** — multiple applications want CPU time, RAM, and disk simultaneously. The OS decides who gets what. Chrome, terminal, and VS Code run simultaneously because the OS shares CPU time between them in milliseconds.

**Hardware abstraction** — Python uses open() to read a file without knowing if the file is on SSD, HDD, or USB. The OS handles that difference invisibly.

**Security and isolation** — one application cannot read another application's memory. The OS enforces this boundary. This is called process isolation.

**Security angle:** When isolation breaks that is a vulnerability. Meltdown 2018 broke isolation between user applications and kernel memory. A normal user program could read kernel memory — passwords, encryption keys, anything. Privilege escalation means an attacker breaks out of their restricted application space and gains OS level or kernel level access.

---

## Subtopic 2 — The Kernel

The kernel is the core of the OS. It has complete control over everything on the machine. It runs at the deepest level of software — directly above hardware.

The kernel manages four things:
- Which process runs on the CPU and when
- How RAM is allocated and protected between processes
- How files are read and written to disk
- How data moves through network interfaces

**Key facts from commands:**
- Kernel version: 6.18.12+kali-amd64
- Kernel compiled by: devel@kali.org using GCC 15
- PID 1 = systemd — the first process, parent of all processes
- PPid of PID 1 = 0 — no parent, created directly by kernel
- PID 1 runs as uid=0 (root) — full kernel level permissions
- pid_max = 4194304 = 2^22 — maximum simultaneous processes

**Security angle:** Rootkits replace or modify kernel components to hide malicious processes. A bootkit replaces the boot process — init/systemd — owning the entire process tree from the moment the machine starts. Kernel space is the most valuable target for attackers.

**strace** — tool that shows every system call a program makes in real time — covered fully in Volume 6.

---

## Subtopic 3 — User Space vs Kernel Space

The kernel divides all memory into two zones:

**Kernel space** — upper memory. Only the kernel runs here. Full unrestricted access to all hardware. One crash here brings down the entire system.

**User space** — lower memory. Every application runs here. Chrome, Python, terminal, VS Code — all user space. Restricted access. Cannot touch hardware. Cannot read another application's memory.

This division is enforced by the CPU itself using privilege rings:
- Ring 0 = kernel mode — full access
- Ring 3 = user mode — restricted

**System calls** — the only legitimate doorway between user space and kernel space. Application makes a request. Kernel checks if allowed. If yes — kernel does it and returns result. If no — error returned.

System calls triggered today: open(), read(), write(), fork(), exec()

**Key facts from commands:**
- Current user: krishna, uid=1000
- Root is always uid=0
- Regular users start at uid=1000 on most Linux systems
- Kernel uses numbers (uid) not names for all permission checks
- swappiness=60 — kernel moves memory to swap at moderate RAM usage
- Swap on disk can contain sensitive data — passwords, keys, tokens

**Critical group memberships:**
- sudo group — one sudo command away from root access
- adm group — can read all system logs including auth.log
- wireshark group — can capture packets without root
- vboxsf group — confirms running inside VirtualBox VM

**Security angle:**

**Dirty COW CVE-2016-5195** — race condition in kernel memory management. User space program could write to read-only kernel memory. Any unprivileged user could become root. Affected every Linux kernel from 2007 to 2016.

**Meltdown 2018** — user space programs could read kernel space memory via speculative execution. The hardware boundary between Ring 3 and Ring 0 failed.

Privilege escalation path: attacker compromises uid=1000 user → finds kernel vulnerability or sudo misconfiguration → jumps to uid=0 → full kernel space access → everything readable, everything writable, nothing hidden.

---

## Key Terms

- **Kernel** — core of OS, complete hardware control, Ring 0
- **User space** — where applications run, Ring 3, restricted
- **Kernel space** — where kernel runs, Ring 0, unrestricted
- **System call** — controlled doorway from user space to kernel space
- **PID** — Process ID, unique number assigned by OS to every process
- **uid** — User ID, number kernel uses for all permission checks
- **Process isolation** — OS prevents one application reading another's memory
- **Privilege escalation** — attacker jumps from user space to kernel space
- **Rootkit** — malware that lives in kernel space, hides from user space tools
- **swappiness** — kernel setting for how aggressively RAM is moved to disk swap
- **/proc** — virtual filesystem the kernel generates in memory, exposes live system info
- **systemd** — modern Linux init system, PID 1, parent of all processes