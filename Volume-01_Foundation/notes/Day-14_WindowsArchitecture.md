# Day 14 — Topic 5: Windows OS Basics
# Subtopic 1 — Windows Architecture Overview

---

## What is Windows Architecture

Windows is not one single piece of software. It is a layered system where each layer has a specific job and a specific level of trust. Understanding this layering is how you understand where attacks happen and why they are dangerous.

**Key terms:**
- **Architecture** — how something is organized internally. Not what it looks like from outside — what it is made of inside and how the pieces connect.
- **Layer** — a level of the system with a specific role and specific privileges.
- **Privilege** — how much power a piece of code has. Can it access all memory? Can it talk directly to hardware?
- **Mode** — the CPU state determining what code is allowed to do. Two modes: User Mode and Kernel Mode.
- **API (Application Programming Interface)** — a set of functions one layer exposes so another layer can call it without knowing internal details.
- **HAL (Hardware Abstraction Layer)** — sits between kernel and actual hardware. Makes kernel hardware-independent.
- **Win32 Subsystem** — provides the standard Windows API that all normal applications use.
- **NTDLL.DLL** — sits at the boundary between user mode and kernel mode. When application wants kernel services, it calls NTDLL which makes the system call.
- **Executive** — upper part of Windows kernel containing core services — memory manager, process manager, I/O manager, security reference monitor.

---

## Two Main Zones

### Kernel Mode — Ring 0 — Trusted
Runs with full hardware access. Nothing in kernel mode can be stopped or overridden by user mode code.

| Component | What It Does |
|---|---|
| HAL | Sits at bottom above physical hardware. Translates generic kernel requests into hardware-specific instructions. Why same Windows kernel runs on Intel, AMD, ARM. |
| Windows Kernel | CPU scheduling, interrupt handling, synchronization |
| Executive — Memory Manager | Allocates and manages virtual memory for every process |
| Executive — Process Manager | Creates, tracks, and terminates processes and threads |
| Executive — I/O Manager | Manages all input and output — disk reads, network packets, device communication |
| Executive — Security Reference Monitor | Enforces access control — checks permissions before any resource access |
| Executive — Object Manager | Tracks all kernel objects — files, processes, threads, registry keys |
| Device Drivers | Also run in kernel mode. Buggy driver crashes entire system because it runs at Ring 0. |

### User Mode — Ring 3 — Restricted
Everything you run as a user — browser, terminal, security tools. Cannot directly access hardware. Communicates with kernel through system calls.

| Component | What It Does |
|---|---|
| Win32 Subsystem | Standard Windows API. CreateFile(), CreateProcess() etc. |
| System Processes | smss.exe, csrss.exe, services.exe, lsass.exe — critical processes, if killed Windows crashes |
| Services | Background processes started by Service Control Manager. Run without logged-in user. |
| Applications | Everything you install — Chrome, VS Code, Word. Lowest privilege. |

---

## The 4 Critical System Processes

These are the most important processes for a security analyst to know. Verify all four on any suspected compromised machine — check path, parent, and instance count.

### smss.exe — Session Manager Subsystem
- First user mode process Windows creates after kernel boots
- Starts before anything else in user mode
- Creates Windows sessions — sets up environment that desktop runs inside
- Starts csrss.exe and winlogon.exe
- **Always runs from:** `C:\Windows\System32\smss.exe`
- **Legitimate PID:** Low number — starts very early in boot
- **Observed PID today:** 360

**Security risk:** If smss.exe runs from any other path — malware impersonating a critical system process. Called process masquerading. Attackers name malware smss.exe knowing most people will not investigate a familiar name.

---

### csrss.exe — Client Server Runtime Subsystem
- User mode component of Win32 subsystem
- Manages Windows console — the terminal windows you open
- Handles process and thread creation notifications — every new process start/death, csrss.exe gets notified
- **Always runs from:** `C:\Windows\System32\csrss.exe`
- **Observed PID today:** 463

**Security risk:** If killed, Windows immediately crashes with Blue Screen of Death. Attackers inject malicious code into csrss.exe so killing it crashes the system, making removal harder. Any csrss.exe outside System32 = compromise.

---

### services.exe — Service Control Manager
- Parent of ALL Windows services
- Manages lifecycle of services — starting at boot, stopping on shutdown, restarting if crashed
- **Always runs from:** `C:\Windows\System32\services.exe`
- **Observed PID today:** 684

**Security risk:** Primary persistence target for attackers. Malware installs itself as a Windows service so it starts automatically at every boot without user action. Unexpected child processes under services.exe = red flag.

---

### lsass.exe — Local Security Authority Subsystem Service
- Authenticates users when they log in — verifies username and password
- Enforces security policies
- Generates security audit log entries — Event IDs 4624 and 4625 come from lsass.exe
- Stores credential material in memory — password hashes, Kerberos tickets
- **Always runs from:** `C:\Windows\System32\lsass.exe`
- **Always:** exactly one instance, parent is wininit.exe or services.exe
- **Observed PID today:** 704

**Security risk:** lsass.exe memory is a goldmine for attackers. Mimikatz dumps lsass.exe memory and extracts plaintext passwords and password hashes directly from it. Used in NotPetya 2017, used in countless ransomware campaigns. Windows Credential Guard was added to make lsass memory harder to dump.

**What to check:** Two lsass.exe instances = compromised. lsass.exe from wrong path = compromised. Unexpected parent process = compromised.

---

## Real Practical Example — You Double-Click Chrome

Step 1 — you double-click Chrome icon. Windows shell (explorer.exe) receives your click through Win32 subsystem.

Step 2 — shell calls CreateProcess() — a Win32 API function. Request travels through NTDLL.DLL into kernel via system call.

Step 3 — Executive's Process Manager creates new process object in kernel mode. Assigns PID — say 4821.

Step 4 — Memory Manager allocates virtual address space for PID 4821. Chrome thinks it has the whole memory map to itself.

Step 5 — I/O Manager maps Chrome executable binary from disk into virtual address space — text segment, read-only. CPU fetches instructions from here.

Step 6 — Chrome loads its DLLs — Win32 libraries, network libraries, graphics libraries. Each mapped into virtual address space.

Step 7 — Chrome creates main window by calling CreateWindowEx() through Win32 subsystem. Desktop Window Manager (dwm.exe) renders it on screen.

Step 8 — you type a URL. Keyboard driver in kernel mode processes keypress, delivers through Win32 message queue to Chrome's user mode code.

Step 9 — Chrome makes network connection. Calls Windows networking APIs. I/O Manager handles actual packet sending through network driver in kernel mode.

Step 10 — you close Chrome. Process Manager marks PID 4821 for termination. Memory Manager frees all allocated memory. All handles Chrome held are closed. PID 4821 disappears from Task Manager.

---

## Security Angles

### Kernel Mode Attacks — Most Dangerous
A rootkit that loads a malicious driver operates in kernel mode. It can:
- Hide processes from Task Manager
- Hide files from the file system
- Intercept network traffic before any security tool sees it

Detection requires comparing what kernel reports against raw kernel memory — rootkit controls what kernel reports. **Stuxnet** used a malicious printer driver to get kernel mode access.

### lsass.exe Attacks
**Mimikatz** — most common credential dumping tool. Dumps lsass memory to extract password hashes and plaintext passwords. Every SOC analyst sees alerts for suspicious lsass access regularly.

### DLL Injection
Attacker loads malicious DLL into legitimate process address space. Malicious code runs inside trusted process — Task Manager shows legitimate process name but attacker code runs inside it. Process names alone cannot be trusted.

### UAC Bypass
Even logged-in Administrator runs at medium integrity by default. UAC forces elevation to high integrity for administrative tasks. Attackers bypass UAC to escalate without triggering UAC prompt.

---

## Key Security Takeaway

On any suspected compromised Windows machine — first check these four processes:
1. Correct path — always System32
2. Correct parent process
3. Correct number of instances — lsass.exe always exactly one

Any deviation = start of investigation.

---

## Analog to Linux (from Topic 3)

| Windows | Linux Equivalent |
|---|---|
| smss.exe | systemd (init process) |
| csrss.exe | bash shell process |
| services.exe | systemd service manager |
| lsass.exe | PAM — Pluggable Authentication Modules |
| Win32 API | POSIX / glibc |
| NTDLL.DLL | glibc system call wrapper |

---

*Volume 01 — Topic 5 — Subtopic 1 | Day 14 | Exam Season*