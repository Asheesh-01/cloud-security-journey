# Day 08 — Labs: What is an Operating System (Subtopics 4-5)

---

## Subtopic 4 — Processes

### Command 1
```bash
ps aux | grep -v grep | head -20
```
Output:
```
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.1  0.3  24548 15340 ?        Ss   22:04   0:00 /sbin/init splash
root           2  0.0  0.0      0     0 ?        S    22:04   0:00 [kthreadd]
root           3  0.0  0.0      0     0 ?        S    22:04   0:00 [pool_workqueue_release]
...
```
What it means: all running processes with resource usage. grep -v grep removes the grep process itself from results — -v means invert match. PID 1 = systemd = parent of all user processes. PID 2 = kthreadd = parent of all kernel threads. Kernel threads in brackets have VSZ=0 RSS=0 — they live in kernel space not user space memory.

### Command 2
```bash
pstree | head -20
```
Output:
```
systemd-+-ModemManager---3*[{ModemManager}]
        |-NetworkManager---3*[{NetworkManager}]
        |-VBoxClient---VBoxClient---4*[{VBoxClient}]
        |-lightdm-+-Xorg---{Xorg}
        |         |-lightdm-+-xfce4-session-+-Thunar
        ...
```
What it means: process hierarchy as a visual tree. systemd at root. +- means child process. 3*[{ModemManager}] means 3 threads inside ModemManager — curly braces = threads. lightdm created Xorg (display server) and xfce4-session (desktop). VBoxClient/VBoxService processes confirm running inside VirtualBox VM. Security use: reveals attack chains — suspicious parent-child relationships visible immediately.

### Command 3
```bash
cat /proc/$$/status | head -10
```
Output:
```
Name:   bash
Umask:  0002
State:  S (sleeping)
Tgid:   1657
Pid:    1657
PPid:   1620
TracerPid:      0
Uid:    1000    1000    1000    1000
Gid:    1000    1000    1000    1000
```
What it means: $$ = PID of current shell = 1657. Shell is bash. State S = sleeping, waiting for next command — normal. PPid 1620 = terminal emulator that created this bash shell. Uid 1000 = krishna = user space restricted. TracerPid 0 = no debugger attached.

### Command 4
```bash
echo $$
```
Output: `1657`

What it means: $$ is a special shell variable that always holds the PID of the current shell process. Shell replaces $$ with 1657 before running any command that uses it. This is why cat /proc/$$/status works — shell turns it into cat /proc/1657/status.

### Command 5
```bash
cat /proc/$$/cmdline
```
Output: `/usr/bin/bash`

What it means: the exact command used to launch this shell process. Full path /usr/bin/bash. Security use: malware may name itself bash but run from /tmp/bash instead of /usr/bin/bash. Reading cmdline reveals the real path. Same name, wrong path = malware.

### Error encountered and fixed
```
cat /proc/$$/status | head-10
head-10: command not found
```
Fix: added space between head and -10. Terminal is exact — head-10 is treated as one word, a command name. head -10 is the command head with argument -10. Always space between command and its flags.

---

## Subtopic 5 — Memory Management

### Command 6
```bash
free -h
```
Output:
```
               total        used        free      shared  buff/cache   available
Mem:           4.3Gi       1.0Gi       365Mi        27Mi       3.2Gi       3.3Gi
Swap:          3.1Gi          0B       3.1Gi
```
What it means:
- total 4.3Gi — Kali VM allocated 4.3GB by VirtualBox. Physical machine has 16GB but VM only gets a portion.
- used 1.0Gi — 1GB used by running processes.
- free 365Mi — raw unused RAM. Looks low but misleading — see available.
- buff/cache 3.2Gi — kernel using 3.2GB to cache recently read files. Not wasted. Kernel reclaims this instantly when processes need more RAM.
- available 3.3Gi — real usable RAM for new processes. This is the number that matters.
- Swap total 3.1Gi, used 0B — swap exists but nothing is being paged to disk. System healthy.

### Command 7
```bash
cat /proc/$$/maps | head -20
```
Output:
```
56042496a000-56042499b000 r--p 00000000 08:01 263123    /usr/bin/bash
56042499b000-560424a75000 r-xp 00031000 08:01 263123    /usr/bin/bash
560424a75000-560424aaf000 r--p 0010b000 08:01 263123    /usr/bin/bash
560424aaf000-560424ab3000 r--p 00145000 08:01 263123    /usr/bin/bash
560424ab3000-560424abc000 rw-p 00149000 08:01 263123    /usr/bin/bash
560424abc000-560424ac8000 rw-p 00000000 00:00 0
56044ea4b000-56044ebb4000 rw-p 00000000 00:00 0         [heap]
7fba02780000-7fba027a8000 r--p 00000000 08:01 262741    /usr/lib/x86_64-linux-gnu/libc.so.6
7fba027a8000-7fba02910000 r-xp 00028000 08:01 262741    /usr/lib/x86_64-linux-gnu/libc.so.6
...
```
Format explanation: start_address-end_address permissions offset device inode filename

Permission characters:
- r = readable
- w = writable
- x = executable
- p = private (not shared)
- - = not that permission

Key regions:
- r-xp /usr/bin/bash = text segment — executable bash code, read and execute only, NX protection prevents writing
- rw-p /usr/bin/bash = data segment — bash global variables, read-write, not executable
- rw-p [heap] = heap — dynamic allocations, read-write, not executable (NX bit active)
- r-xp libc.so.6 = C standard library executable code — every Linux program uses libc
- rw-p libc.so.6 = libc data segment

Security: heap is rw-p not r-xp — NX bit prevents executing code injected into heap. Return-oriented programming (ROP) bypasses this by chaining existing r-xp regions.

### Command 8
```bash
cat /proc/meminfo | grep -E "MemTotal|MemFree|MemAvailable|SwapTotal|SwapFree"
```
Output:
```
MemTotal:        4522440 kB
MemFree:          375308 kB
MemAvailable:    3469304 kB
SwapTotal:       3254268 kB
SwapFree:        3254268 kB
```
What it means:
- MemTotal 4522440 kB = 4.3GB total RAM in kilobytes
- MemFree 375308 kB = 366MB raw free — fluctuates constantly, ran twice and got different values
- MemAvailable 3469304 kB = 3.3GB truly available — includes reclaimable cache
- SwapTotal 3254268 kB = 3.1GB swap partition
- SwapFree 3254268 kB = entire swap free, zero paging happening

grep -E flag = extended regex, allows | to mean OR between patterns.

### Command 9
```bash
vmstat -s | head -15
```
Output:
```
      4522440 K total memory
      1067792 K used memory
      2555960 K active memory
      1262152 K inactive memory
       360464 K free memory
       208220 K buffer memory
      3184980 K swap cache
      3254268 K total swap
            0 K used swap
      3254268 K free swap
         9080 non-nice user cpu ticks
           51 nice user cpu ticks
         4248 system cpu ticks
      2193453 idle cpu ticks
         1687 IO-wait cpu ticks
```
What it means:
- active memory 2555960 K = recently used, kernel keeps in RAM
- inactive memory 1262152 K = not used recently, first candidate for swap
- swap cache 3184980 K = pages brought back from swap, kept in RAM as cache
- used swap 0 K = confirmed no active paging
- idle cpu ticks 2193453 = very high, VM mostly idle, healthy
- IO-wait ticks 1687 = low, disk not a bottleneck
- system cpu ticks 4248 = kernel work since boot