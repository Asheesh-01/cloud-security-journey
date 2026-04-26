# Day 47 — Labs: System Calls

**Environment:** Kali Linux VM

---

## Lab 1 — Verify strace Installation
```bash
which strace || sudo apt install strace -y
```
- `which strace` — checks if strace is in PATH
- `||` — install if not found
- strace is pre-installed on Kali

---

## Lab 2 — Trace All Syscalls
```bash
strace ls /tmp 2>&1 | head -30
```
- `strace` — intercepts and displays every system call
- `ls /tmp` — the command being traced
- `2>&1` — strace writes to stderr, redirect to stdout for piping
- Format: syscall_name(arguments) = return_value

---

## Lab 3 — Syscall Summary
```bash
strace -c ls /tmp 2>&1
```
- `-c` — count mode, shows summary table after command completes
- Columns: % time, total seconds, calls, errors, syscall name
- Reveals which syscalls are most frequent and time-consuming

---

## Lab 4 — Filter File Operations
```bash
strace -e trace=open,openat,read,write ls /tmp 2>&1 | head -20
```
- `-e trace=` — filter to only specified syscall names
- Shows only file-related syscalls, eliminates other noise
- In malware analysis: filtering to file syscalls reveals what files are accessed

---

## Lab 5 — Trace File Paths Verbosely
```bash
strace -e trace=openat -v ls /tmp 2>&1 | head -20
```
- `openat` — modern replacement for open on Linux
- `-v` — verbose output, full string arguments not truncated
- Shows complete file paths accessed during ls execution

---

## Lab 6 — Trace Network Syscalls
```bash
strace -e trace=connect,socket,bind wget -q https://example.com -O /dev/null 2>&1 | head -15
```
- Filters to network-related syscalls only
- `wget` makes HTTP/HTTPS connection — connect() call visible
- Shows destination IP address and port in connect() arguments
- In malware analysis: reveals C2 server IP before packet analysis

---

## Lab 7 — Trace Process Creation
```bash
strace -e trace=execve,fork,clone bash -c "ls /tmp" 2>&1
```
- `execve,fork,clone` — process creation syscalls
- Shows bash starting, forking for child, execve for ls
- In malware analysis: unexpected execve calls reveal spawned processes

---

## Lab 8 — Check seccomp Status
```bash
cat /proc/$$/status | grep Seccomp
```
- `/proc/$$/status` — process status file for current shell
- `Seccomp` field: 0=no restriction, 1=strict, 2=filter mode
- Docker containers show 2 — syscalls restricted by container policy
- Your shell likely shows 0 — unrestricted syscall access