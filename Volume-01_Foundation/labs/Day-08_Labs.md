# Day 08 — Labs
Volume 1 — Computer & Internet Foundations
Topics: User Space vs Kernel Space, System Calls

---

## 🔬 Lab 1 — See System Calls of ls

```bash
strace ls 2>&1 | head -20
```

`strace` → trace system calls made by a program
`ls` → the program being traced
`2>&1` → redirect stderr to stdout (strace writes to stderr by default)
`|` → pipe — sends output of left command as input to right command
`head -20` → show only first 20 lines

Observation:
Every line = one syscall = one crossing from user space → kernel space.
Even listing a directory requires kernel permission.

---

## 🔬 Lab 2 — Count Total Syscalls of ls

```bash
strace ls 2>&1 | wc -l
```

`wc` → word count tool
`-l` → count lines only

Result on this system: **104 syscalls**

104 user space → kernel space crossings just to run `ls`.

---

## 🔬 Lab 3 — Trace Syscalls of a Live Process

```bash
# Create a background process to watch
sleep 100 &
```

`sleep 100` → do nothing for 100 seconds (creates a traceable process)
`&` → run in background so terminal stays usable

```bash
# Find its PID
ps aux | grep sleep
```

`ps aux` → show all running processes
`grep sleep` → filter lines containing "sleep"

```bash
# Trace its live syscalls
sudo strace -p <PID_NUMBER>
```

`sudo` → run as superuser (kernel requires elevated permission to trace another process)
`strace` → system call tracer
`-p` → attach to existing running process
`<PID_NUMBER>` → replace with actual PID from grep output

Press Ctrl+C to stop tracing.

```bash
# Clean up background process
kill %1
```

`kill` → send termination signal to a process
`%1` → refers to first background job running

---

## 📝 Lab Observations

| Lab | Command | Result |
|-----|---------|--------|
| Lab 1 | strace ls \| head -20 | First syscall seen = |
| Lab 2 | strace ls \| wc -l | Total syscalls = 104 |
| Lab 3 | strace -p PID | Syscalls observed = |