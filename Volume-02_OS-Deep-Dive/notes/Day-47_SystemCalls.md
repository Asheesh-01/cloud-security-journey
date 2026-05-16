# Day 47 — System Calls

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 1 — OS Architecture
**Subtopic:** 3 — System Calls

---

## What It Is

The only legal gateway from user space (Ring 3) to kernel space (Ring 0).
Every security-relevant action goes through a system call.
All syscalls are observable — the foundation of behavioral security detection.

## Key Terms

**syscall number** — unique integer per syscall. RAX register on x86-64.
read=0, write=1, open=2, close=3, execve=59, connect=42, fork=57.

**execve** — syscall 59. Execute a program. Every process launch.
EDR tools hook execve to detect all program execution.

**open/openat** — syscalls 2/257. Open a file. All file access starts here.
open("/etc/shadow") from unexpected process = credential access attempt.

**connect** — syscall 42. Outbound network connection.
C2 communication fires connect before first packet leaves machine.

**ptrace** — syscall 101. Observe/control another process.
Used by debuggers. Also used for process injection by malware.
Hardened systems restrict ptrace.

**seccomp** — restricts which syscalls a process can make.
Docker uses seccomp filters to limit containerized process syscalls.
/proc/$$/status Seccomp field: 0=off, 1=strict, 2=filter.

**strace** — tool that intercepts and displays syscalls of a process.
Format: syscall_name(arguments) = return_value
Used for: malware analysis, debugging, verifying security controls.

**auditd** — system-wide kernel syscall monitoring daemon.
Writes events to audit log. Foundation of Linux compliance monitoring.
Used in Volume 6 for threat hunting.

## System Call Flow

1. User program calls library function (fopen, printf, connect)
2. glibc translates to syscall number + arguments
3. Program places syscall number in RAX, args in other registers
4. Executes syscall CPU instruction → CPU switches to Ring 0
5. Kernel reads RAX, calls corresponding kernel function
6. Kernel performs operation (checks permissions, accesses hardware)
7. Returns result in RAX → CPU switches back to Ring 3
8. Program resumes with return value

## Behavioral Detection vs Signature Detection

Signature: matches file hash. Change one byte = bypass.
Behavioral: matches syscall patterns. Harder to change.

Detection examples:
- execve("/bin/sh") after network input → possible RCE
- open("/etc/shadow") from non-root non-auth process → credential theft
- connect() to unknown IP from web server process → C2 communication
- ptrace() on unowned process → process injection attempt

## Commands

```bash
which strace || sudo apt install strace -y
strace ls /tmp 2>&1 | head -30           # all syscalls for ls
strace -c ls /tmp 2>&1                   # syscall summary table
strace -e trace=open,openat,read,write ls /tmp 2>&1 | head -20
strace -e trace=openat -v ls /tmp 2>&1 | head -20
strace -e trace=connect,socket,bind wget -q https://example.com -O /dev/null 2>&1 | head -15
strace -e trace=execve,fork,clone bash -c "ls /tmp" 2>&1
sudo strace -p [PID] 2>&1               # attach to running process
sudo systemctl status auditd 2>/dev/null
cat /proc/$$/status | grep Seccomp
```