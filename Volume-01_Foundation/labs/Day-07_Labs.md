# Day 07 — Labs: What is an Operating System (Subtopics 1-3)

---

## Subtopic 1 — Purpose of an Operating System

### Command 1
```bash
uname -a
```
Output:
```
Linux kali 6.18.12+kali-amd64 #1 SMP PREEMPT_DYNAMIC Kali 6.18.12-1kali1 (2026-02-25) x86_64 GNU/Linux
```
What it means: shows complete kernel identification. Linux = kernel type. kali = hostname. 6.18.12+kali-amd64 = version. SMP = multi-core support active. PREEMPT_DYNAMIC = OS can interrupt any process for multitasking. x86_64 = 64-bit architecture. GNU/Linux = GNU tools + Linux kernel together form the OS.

### Command 2
```bash
cat /proc/version
```
Output:
```
Linux version 6.18.12+kali-amd64 (devel@kali.org) (x86_64-linux-gnu-gcc-15 (Debian 15.2.0-13) 15.2.0, GNU ld (GNU Binutils for Debian) 2.46) #1 SMP PREEMPT_DYNAMIC Kali 6.18.12-1kali1 (2026-02-25)
```
What it means: shows how the kernel was built. devel@kali.org = Kali team compiled this kernel. GCC 15 = compiler used. GNU ld 2.46 = linker used. Different from uname -a — uname shows what the kernel IS, /proc/version shows how it was BUILT. In forensics: if compiler or build details look unusual, kernel may have been replaced by an attacker.

### Command 3
```bash
ps aux | head -10
```
Output:
```
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.3  24552 15392 ?        Ss   21:43   0:01 /sbin/init splash
root           2  0.0  0.0      0     0 ?        S    21:43   0:00 [kthreadd]
root           3  0.0  0.0      0     0 ?        S    21:43   0:00 [pool_workqueue_release]
...
```
What it means: PID 1 = /sbin/init = systemd = first process, parent of all. All kernel threads owned by root (uid=0). STAT S = sleeping/waiting. STAT I = idle kernel thread. Brackets around name = kernel thread not user program. VSZ = virtual memory reserved. RSS = actual RAM currently used.

---

## Subtopic 2 — The Kernel

### Command 4
```bash
uname -r
```
Output: `6.18.12+kali-amd64`

What it means: kernel release version only. Clean single line. This is what you check first on any Linux machine to match against CVE databases. CVEs list affected kernel versions exactly like this.

### Command 5
```bash
cat /proc/sys/kernel/hostname
```
Output: `kali`

What it means: the kernel stores the machine hostname in its own parameter files. Not a file on disk — kernel generates this in memory. Your machine identifies itself on the network as kali. This is why terminal shows krishna㉿kali.

### Command 6
```bash
cat /proc/sys/kernel/pid_max
```
Output: `4194304`

What it means: maximum simultaneous processes = 4,194,304 = 2^22. Kernel uses powers of 2 for limits. Security: if PID numbers approach this limit the system may be under a fork bomb attack — malware creating millions of processes to crash the machine by exhausting the PID limit.

### Command 7
```bash
cat /proc/1/status | head -15
```
Output:
```
Name:   systemd
Umask:  0000
State:  S (sleeping)
Tgid:   1
Ngid:   0
Pid:    1
PPid:   0
TracerPid:      0
Uid:    0       0       0       0
Gid:    0       0       0       0
FDSize: 256
Groups:
NStgid: 1
NSpid:  1
NSpgid: 1
```
What it means: PID 1 is systemd. State S = sleeping, waiting for work, not using CPU — normal. PPid=0 means no parent — kernel created it directly. Every other process has a parent. Systemd has none. Uid=0 = root — full kernel level permissions. FDSize=256 = 256 file descriptor slots open. Owning PID 1 = owning the entire process tree.

### Command 8
```bash
ls /proc/ | head -20
```
Output:
```
1
10
1015
1017
...
```
What it means: every number is a PID of a currently running process. Kernel creates a folder for every live process. When process dies folder disappears. When new process starts new folder appears. /proc is a live real-time map of every process. Security: rootkits hide processes by intercepting system calls that read /proc — the folder still exists but the rootkit removes it from what ls shows you. Called process hiding.

---

## Subtopic 3 — User Space vs Kernel Space

### Command 9
```bash
cat /proc/sys/kernel/ostype
```
Output: `Linux`

What it means: kernel identifies its own type as Linux. Not the distribution. Not Kali. Just Linux. The kernel does not know what distribution is built on top of it.

### Command 10
```bash
cat /proc/sys/kernel/osrelease
```
Output: `6.18.12+kali-amd64`

What it means: kernel version read directly from kernel parameter files. Same value as uname -r. Two different paths to the same information — both from kernel space directly.

### Command 11
```bash
cat /proc/sys/vm/swappiness
```
Output: `60`

What it means: scale 0-100. Value 60 = kernel starts moving user space memory from RAM to swap disk at moderate RAM usage. Security: swap space on disk can contain passwords, encryption keys, session tokens that were in RAM. Unencrypted swap is a data exposure risk. Forensic investigators always check swap during memory analysis.

### Command 12
```bash
id
```
Output:
```
uid=1000(krishna) gid=1000(krishna) groups=1000(krishna),4(adm),20(dialout),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),100(users),101(netdev),103(scanner),116(bluetooth),121(lpadmin),124(wireshark),133(vboxsf),134(kaboxer)
```
What it means: uid=1000 = restricted user space. Not root (uid=0). Kernel uses numbers not names for all permission checks. Critical groups: sudo(27) = one sudo command from root access. adm(4) = can read all system logs. wireshark(124) = can capture packets without root. vboxsf(133) = inside VirtualBox VM confirmed. Attacker compromising this account gets uid=1000 but sudo group membership makes it dangerous.

### Command 13
```bash
whoami
```
Output: `krishna`

What it means: human readable version of uid=1000. Kernel resolves uid to name using /etc/passwd. Kernel always uses the number internally — this is just display convenience.

---

## Error Encountered and Fixed

Typed `kernal` instead of `kernel` in the path:
```
cat: /proc/sys/kernal/hostname: No such file or directory
```
Fix: corrected spelling to `kernel`. Lesson: terminal is exact. One wrong character = command fails. Always read the error message — it tells you exactly what went wrong.