# Day 14 — Labs
# Topic 5: Windows OS Basics — Subtopic 1: Windows Architecture Overview

---

## Lab Environment
- Host: Ubuntu 24.04 — Acer Swift Go 14
- VM: Windows 10 inside VirtualBox
- Tool used: Task Manager (Ctrl+Shift+Esc)

---

## Lab 1 — Observing Critical System Processes in Task Manager

### Steps performed:
1. Booted Windows 10 VM in VirtualBox
2. Opened Task Manager using Ctrl+Shift+Esc
3. Clicked Details tab
4. Located all four critical system processes

### Output — Process PIDs observed:

| Process | PID | Expected Location | Instances Found |
|---|---|---|---|
| smss.exe | 360 | C:\Windows\System32\smss.exe | 1 — normal |
| csrss.exe | 463 | C:\Windows\System32\csrss.exe | 1 — normal |
| services.exe | 684 | C:\Windows\System32\services.exe | 1 — normal |
| lsass.exe | 704 | C:\Windows\System32\lsass.exe | 1 — normal |

### What these PIDs tell us:
- smss.exe PID 360 — low number confirms it started very early in boot sequence, right after kernel loaded. Expected.
- csrss.exe PID 463 — started after smss.exe launched it during session setup. Normal.
- services.exe PID 684 — started after session established. Parent of all Windows services. Normal.
- lsass.exe PID 704 — single instance confirmed. This is what we always verify first. Normal.

### Security verification checklist:
- smss.exe — one instance only — confirmed
- csrss.exe — one instance only — confirmed
- services.exe — one instance only — confirmed
- lsass.exe — one instance only — confirmed — critical, must always be exactly one

---

## Key Observation

All four critical processes showed normal PIDs, single instances, and no unexpected behavior. This is what a healthy Windows boot looks like.

In a real SOC investigation this is the first check on any suspected Windows machine — verify path, parent, and instance count of these four processes. Any deviation starts an investigation.

---

*Volume 01 — Topic 5 — Subtopic 1 | Day 14 | Exam Season*
*Commit: Day 14 - Windows Architecture Overview - Task Manager observation lab*