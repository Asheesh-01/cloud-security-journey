# Day 09 — What is an Operating System (Part 3)
Volume 1 — Computer & Internet Foundations

## 🎯 Objective
Understand what processes are, how the OS manages them, parent-child relationships, and why this is critical for malware detection and threat hunting.

---

## 📘 Theory Notes

### 1️⃣ Program vs Process

| Program | Process |
|---------|---------|
| Static — sitting on disk | Dynamic — loaded into RAM and running |
| Just instructions written in a file | Program in execution |
| Does nothing on its own | Has PID, memory, CPU time, owner |
| One copy on disk | Can have multiple instances running simultaneously |

Real example:
- `/bin/bash` on disk = program
- Three open terminal windows = three separate bash processes

---

### 2️⃣ What a Process Contains

| Component | What it is |
|-----------|-----------|
| PID | Process ID — unique number OS assigns to every process |
| PPID | Parent Process ID — who created this process |
| State | Running, Sleeping, Stopped, Zombie |
| Memory space | Its own isolated section of RAM |
| CPU time | How much processor time it is getting |
| Owner | Which user started it — determines its permissions |

---

### 3️⃣ Process States

```
NEW → READY → RUNNING → WAITING → READY (loop)
                  ↓
              TERMINATED
```

| State | Meaning |
|-------|---------|
| Running | Currently using CPU right now |
| Sleeping | Waiting for something — not using CPU |
| Stopped | Paused, not executing |
| Zombie | Finished but parent has not acknowledged it yet |

Security relevance:
A process that should be sleeping but is constantly Running = suspicious.
Could be cryptominer or malware doing active work in background.

---

### 4️⃣ Parent and Child Processes

Every process is created by another process — forms a tree.

PID 1 = systemd = parent of everything on Linux.
Every process on the system is a child or descendant of PID 1.

```
PID 1 (systemd)
├── PID 234 (sshd)
├── PID 445 (bash)
│   └── PID 891 (ls) ← child of bash, dies after ls finishes
└── PID 502 (python3)
```

Lab result — current shell PID = 2944
Meaning 2943 processes were created before this shell since last boot.

How process creation works:
- Open terminal → bash process starts
- Run ls → bash creates child process for ls
- ls finishes → child process dies
- bash continues running

Security relevance:
Malware often spawns unexpected child processes.
Seeing a legitimate program spawn an unexpected child = red flag.

Classic attack example:
Word document spawning PowerShell child process = malware execution.
In cloud: compromised web server spawning bash shell = active attack indicator.

---

## 🧠 Key Takeaways

- Program = static instructions on disk. Process = program in execution with PID + RAM + CPU time
- Every process has a PID, PPID, state, memory space, and owner
- All processes form a tree rooted at PID 1 (systemd)
- Process states matter — abnormal Running state = suspicious
- Parent-child relationships are key to detecting malware behavior
- Unexpected child processes under legitimate parents = red flag in threat hunting

---

## 🔜 Next Topic

Memory Management