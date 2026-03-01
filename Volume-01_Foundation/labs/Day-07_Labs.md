# Day 07 — Labs
Volume 1 — Computer & Internet Foundations
Topics: Purpose of OS, Kernel

---

## 🔬 Lab 1 — See OS Managing Processes

```bash
# See all processes OS is managing right now
htop
```

Observe:
- Every line = one process
- CPU% column = how much CPU time OS allocated to this process
- MEM% column = how much RAM OS allocated
- OS is managing all of them simultaneously via context switching

Press `q` to exit.

```bash
# Count exactly how many processes OS is managing
ps aux | wc -l
```

Result on this system: 208 processes

---

## 🔬 Lab 2 — See OS Memory Management

```bash
# See OS memory allocation
free -h
```

Observe:
- Total = total RAM available
- Used = OS has allocated this to running processes
- Free = available for new processes
- OS is managing every byte of this in real time

---

## 🔬 Lab 3 — See OS File System Management

```bash
# See all filesystems OS is managing
df -h
```

Observe:
- Each line = one filesystem mounted
- OS manages all storage here
- Size, used, available per filesystem

---

## 🔬 Lab 4 — See OS Access Control

```bash
# See who you are — OS identity check
whoami
id
```

Observe:
- whoami = your username
- id = your user ID, group ID, all groups you belong to
- OS checks this identity before every file access and command

---

## 🔬 Lab 5 — See the Kernel

```bash
# See your kernel version
uname -r

# Full system info including kernel
uname -a

# See how long kernel has been running
uptime

# See kernel messages (kernel talking about hardware)
dmesg | tail -20

# See kernel modules loaded
lsmod | head -20
```

Observe:
- uname -r = your kernel version (important for CVE tracking)
- uptime = if very long, patches may not be applied
- dmesg = kernel's own log of hardware and system events
- lsmod = extensions loaded into kernel (rootkits can appear here)

---

## 🔬 Lab 6 — Spot Suspicious Processes (Real Technique)

```bash
# Sort processes by CPU usage — top consumers
ps aux --sort=-%cpu | head -10

# Sort by memory usage
ps aux --sort=-%mem | head -10
```

Observe:
- In real threat hunting you look for processes with abnormally high CPU (cryptominer)
- Strange process names trying to look like system processes
- Processes running from unusual locations

---

## 📝 Lab Observations

| Lab | Command | Result |
|-----|---------|--------|
| Lab 1 | ps aux \| wc -l | 208 processes |
| Lab 2 | free -h | Total RAM = |
| Lab 3 | df -h | Filesystems = |
| Lab 4 | whoami | Username = |
| Lab 5 | uname -r | Kernel version = |
| Lab 6 | ps aux --sort=-%cpu | Top process = |