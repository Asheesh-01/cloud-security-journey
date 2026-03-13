# Day 08 — Topic 3: What is an Operating System (Subtopics 4-5)

**Date:** Day 08 of Cloud Security Journey
**Volume:** 01 — Computer and Internet Foundations
**GitHub:** github.com/Asheesh-01

---

## Subtopic 4 — Processes

A process is a program that is currently running. Not the program file on disk — that is just code. When the OS loads that code into RAM and starts executing it, it becomes a process.

Every process has:
- A PID — unique number assigned by the kernel
- A parent process — the process that created it
- Its own isolated memory region in RAM
- A state — running, sleeping, stopped, zombie
- An owner — the uid of the user who started it

**Process tree:**
Every process except PID 1 is created by another process. PID 1 (systemd) is the root. Systemd creates the desktop. Desktop creates the terminal. Terminal creates the shell. Shell creates every command you run.

**Fork and exec:**
- fork() — parent creates an identical copy of itself. The copy is the child process.
- exec() — child replaces itself with the new program to run.
When you type ls — shell forks, child execs ls, ls runs, finishes, dies. Milliseconds.

**Real practical example — you type `python3 script.py` in terminal:**

Step 1 — you press Enter. Bash (PID 1657) calls fork(). A copy of bash is created — new PID assigned, say 1821.
Step 2 — child process 1821 calls exec(python3). The bash copy replaces itself with the Python interpreter.
Step 3 — Python interpreter loads your script from disk into the text segment.
Step 4 — Python starts executing. Variables go on the stack. Large objects go on the heap.
Step 5 — script finishes. Python process 1821 exits. Kernel reclaims its memory. PID 1821 folder disappears from /proc.
Step 6 — bash 1657 gets notified its child died. Displays the next prompt. Waiting for your next command.

**Key outputs from commands:**
- Your bash shell: PID 1657, PPid 1620 (terminal emulator), State S (sleeping)
- $$ variable = current shell PID = 1657
- /proc/$$/cmdline = /usr/bin/bash — full path that launched your shell
- pstree shows systemd at root, lightdm creating Xorg and xfce4-session, VBoxClient processes confirming VM

**Security angle:**
Every malware is a process. Detecting malware = finding the suspicious process.
Suspicious signs:
- Wrong parent: Word.exe creating cmd.exe creating powershell.exe = macro attack chain
- Wrong path: svchost.exe running from C:\Users\ instead of C:\Windows\System32\
- Process with no TTY making network connections
- Process owner is root but started from user application

Process injection — attacker inserts malicious code into legitimate running process. Code runs under legitimate process name and permissions. Antivirus sees svchost.exe — looks safe. Memory contains attacker code. Covered Volume 6.

---

## Subtopic 5 — Memory Management

The kernel gives each process its own virtual address space — a private map that looks like the process owns all RAM by itself. The Memory Management Unit (MMU) inside the CPU translates virtual addresses to real physical RAM addresses.

Two processes can both think they own address 0x7fff0000 — but mapped to completely different physical RAM locations. They never see each other's memory.

**Process memory regions low to high address:**
- Text segment — program code, read only, CPU fetches instructions here
- Data segment — global and static variables, exists for process lifetime
- Heap — dynamic memory (malloc), grows upward
- Stack — local variables, function arguments, return addresses, grows downward

Heap grows up. Stack grows down. They grow toward each other. If they meet = crash or exploit.

**Virtual memory and swap:**
When total RAM demand exceeds physical RAM, kernel moves least recently used pages to swap partition on disk (paging). Brought back when needed — causes slowdown.

**Real practical example — you open Firefox:**

Step 1 — double click Firefox. Desktop forks, child execs Firefox binary. Kernel assigns PID 2341.
Step 2 — kernel allocates virtual address space for PID 2341. Firefox thinks it owns all memory.
Step 3 — kernel maps Firefox binary from disk into text segment. Read only. CPU fetches instructions here.
Step 4 — Firefox loads libraries — libssl, libc, graphics libraries. Each mapped into virtual address space.
Step 5 — Firefox opens tabs. Requests heap memory using malloc(). Kernel expands heap upward.
Step 6 — every function call pushes stack frame. Local variables, arguments, return addresses on stack. Stack grows downward.
Step 7 — 20 tabs open. Firefox using 800MB RAM. 16GB available. No problem.
Step 8 — 15 more heavy apps open. Total RAM demand near limit. Kernel finds Firefox tabs unused for 10 minutes. Moves those pages to swap disk. Firefox does not know.
Step 9 — you click old tab. Page fault — memory not in RAM. Kernel fetches from swap. Tab takes 2 seconds. That delay = swap fetch.
Step 10 — Firefox closed. Kernel frees all memory for PID 2341. /proc/2341 disappears.

**Key outputs from commands:**
- Kali VM has 4.3GB RAM total (VirtualBox only allocates portion of physical 16GB)
- Used: 1GB, Available: 3.3GB, Swap: 3.1GB total, 0 used
- Bash memory map shows: text segment (r-xp), data segment (rw-p), heap (rw-p), libc loaded
- r-xp = readable and executable — text segment, code lives here
- rw-p = readable and writable — heap and data, no execute (NX protection)
- Heap address: 56044ea4b000-56044ebb4000
- libc loaded at: 7fba02780000 — every Linux process uses this C standard library

**Security angle:**
Buffer overflow — fixed 64 byte stack buffer, attacker sends 128 bytes. Extra 64 bytes overwrite return address on stack. CPU loads attacker address into RIP. Attacker controls execution. RIP from Topic 2 + stack from this subtopic = complete picture of how buffer overflows work.

Fileless malware — lives entirely in heap or injected into another process memory. No disk trace. Only memory forensics finds it. Tool: Volatility — covered Volume 6.

Use-after-free — process frees memory but keeps pointer. Attacker tricks process into using freed pointer. Memory reallocated to attacker data. Process executes attacker data as code. Responsible for many browser CVEs.

NX bit — marks heap and stack as non-executable (rw-p not r-xp). Prevents attacker from injecting shellcode into heap and executing it. Return-oriented programming (ROP) bypasses NX by chaining existing executable code regions.

---

## Key Terms

- **Process** — a program currently running in memory with a PID
- **PID** — Process ID, unique number kernel assigns to every running process
- **fork()** — system call that creates a copy of the current process
- **exec()** — system call that replaces a process with a new program
- **Virtual address space** — private memory map each process gets from kernel
- **MMU** — Memory Management Unit, CPU component that translates virtual to physical addresses
- **Text segment** — read-only executable region containing program code
- **Heap** — dynamic memory region, grows upward, managed by malloc/free
- **Stack** — local variables and return addresses, grows downward
- **Paging** — moving memory pages between RAM and swap disk
- **Page fault** — process accesses memory that is currently in swap, kernel fetches it back
- **Buffer overflow** — input exceeds buffer size, overwrites adjacent memory including return address
- **NX bit** — No-Execute protection, marks heap and stack as non-executable
- **ROP** — Return-Oriented Programming, exploit technique that chains existing executable code
- **Fileless malware** — lives only in RAM, no disk trace, evades antivirus
- **Process injection** — attacker inserts malicious code into legitimate running process
- **pstree** — command showing parent-child process relationships as a tree
- **$$** — shell variable containing PID of current shell process
- **/proc/PID/maps** — file showing complete virtual memory map of a process
- **/proc/PID/cmdline** — command that launched a process including all arguments