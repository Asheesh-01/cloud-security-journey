# Volume 01 — Interview Prep
# Topic 1 — What is a Computer

---

## Question 1
What is the difference between data and information?
Give a security example of each.

## Answer
Data is raw unprocessed facts with no meaning on their own.
Information is data that has been processed and given context
so it means something actionable.

Security example of data — 192.168.1.105, failed, 3, 2AM.
These are just values. They mean nothing alone.

Security example of information — IP address 192.168.1.105
made 3 failed login attempts at 2AM on the authentication
server. This is a possible brute force attack. Investigate
immediately and check if this IP appears in threat
intelligence feeds.

A SIEM takes raw log data from thousands of sources and
processes it into information — alerts that a SOC analyst
can act on.

---

## Question 2
What is fileless malware? Why is it harder to detect than
traditional malware? Where does it live?

## Answer
Fileless malware is malware that never writes any files to
disk. It runs entirely in RAM as instructions being executed
by the CPU.

Traditional malware writes files to disk. Antivirus works
by scanning files on disk and comparing them against a
database of known malicious signatures. Because fileless
malware has no files on disk antivirus finds nothing.
The scan comes back clean even though the machine is
fully compromised.

Fileless malware lives in RAM only. When the machine reboots
RAM is completely wiped and the malware disappears. But
before reboot it can steal data, create backdoors, move
laterally to other systems, and cause full damage.

The only way to detect fileless malware is memory forensics.
Capture a dump of everything currently in RAM and analyze
it for malicious code patterns. Tool used — Volatility.
Covered in Volume 06.

---

## Question 3
A server that normally runs at 10% CPU is suddenly at 95%.
As a SOC analyst what are the three most likely security
related causes and what do you do?

## Answer
Three most likely security related causes:

Cause 1 — Crypto mining malware. Attacker compromised the
server and installed malware using the CPU to mine
cryptocurrency. Attacker earns money. Victim pays the
electricity bill.

Cause 2 — DDoS or DoS condition. Server is being flooded
with requests forcing it to process far more than normal.
CPU spikes trying to handle the flood.

Cause 3 — Malware performing intensive operations.
Ransomware encrypting files, a worm scanning the network
for new targets, or a backdoor exfiltrating large volumes
of data — all cause CPU spikes.

What I do as SOC analyst:
Step 1 — run top to identify which process is consuming CPU.
Step 2 — check if the process name is legitimate or unknown.
Step 3 — check network connections from that process using
netstat to see if it is connecting to unknown external IPs.
Step 4 — escalate to Tier 2 with findings. Do not kill
the process without authorization — killing it destroys
evidence.
Step 5 — check /var/log for related events around the time
the spike began.

---

## Question 4
What is Secure Boot? What attack does it prevent?
What happened with LoJax in 2018?

## Answer
Secure Boot is a UEFI feature that verifies every piece of
software in the boot process has a valid digital signature
from a trusted source. It checks the bootloader and kernel
before the OS loads. If anything is unsigned or modified
Secure Boot refuses to load it.

The attack it prevents is a bootkit — malware that infects
the boot process. A bootkit loads before the OS which means
it loads before antivirus and before any security tool.
It has full control of the machine from power on.

LoJax was discovered in 2018. It was the first confirmed
UEFI rootkit found in real world attacks. Developed by
APT28 — also known as Fancy Bear — a Russian military
intelligence hacking group responsible for the 2016 US
Democratic National Committee breach.

LoJax infected the UEFI firmware chip on the motherboard
itself. It survived complete OS reinstalls and hard disk
replacement. The only removal method was physically
reflashing the UEFI chip with clean firmware. Most
organizations had no process for this.

---

## Question 5
An attacker has physical access to a server for 5 minutes.
List four things they can do to compromise it permanently
even after you reinstall the OS.

## Answer
One — flash malware into the BIOS or UEFI chip. Malware
in firmware survives OS reinstalls and disk replacement.
It reloads itself every boot. Most antivirus cannot
scan BIOS.

Two — cold boot attack. Remove RAM sticks while the machine
is running or recently powered off. RAM retains data briefly
when cold. Extract encryption keys, passwords, and session
tokens directly from the RAM chips.

Three — install a hardware keylogger on the keyboard port.
Small physical device between keyboard and computer.
Records every keystroke invisibly. Completely undetectable
by any software security tool.

Four — disable Secure Boot in BIOS settings. With Secure
Boot disabled the attacker can later deploy a bootkit
remotely. The malicious bootloader will load successfully
on every boot because signature verification is off.




# Interview Prep — Volume 01 Topic 2
# How Computers Process Data

---

**Q1. What is the difference between a bit and a byte? How many possible values can one byte hold and why?**

A bit is the smallest unit of data in a computer. It holds exactly one binary digit — either 0 or 1. Nothing smaller exists.

A byte is 8 bits grouped together. One byte can hold 256 different values — from 0 to 255.

Why 256: each of the 8 bit positions can independently be 0 or 1. Total combinations = 2 x 2 x 2 x 2 x 2 x 2 x 2 x 2 = 2^8 = 256.

---

**Q2. An IPv4 address is 32 bits. How many possible IPv4 addresses exist and why did IPv4 run out?**

An IPv4 address is 32 bits long. 2^32 = 4,294,967,296 — approximately 4.3 billion possible addresses.

IPv4 ran out because the number of internet-connected devices grew beyond 4.3 billion. Every smartphone, laptop, server, IoT device, and router needs an IP address. The world exceeded this limit.

The solution is IPv6 which uses 128-bit addresses giving 2^128 possible addresses — a number so large it will not run out in any foreseeable future.

---

**Q3. You find the string cGFzc3dvcmQ= in a firewall log. What do you do and what does it say?**

The = at the end is the Base64 padding character. First thought: this is a Base64 encoded string.

First action: decode it immediately.
```python
import base64
base64.b64decode('cGFzc3dvcmQ=')
```
Result: b'password'

The finding: the word password was transmitted in a form that looks like random data but is trivially decodable. This is a credential exposure. Someone or something logged a plaintext password. This is escalated immediately in a SOC investigation — it could mean a misconfigured application is logging credentials, or an attacker planted decoy credentials. Check what process generated this log entry and at what time.

---

**Q4. Why does the character A take 1 byte in UTF-8 but the character अ takes 3 bytes?**

UTF-8 is a variable-width encoding. The number of bytes used depends on where the character sits in the Unicode standard.

A is ASCII character number 65. It falls in the first 128 characters of Unicode. UTF-8 uses exactly 1 byte for any character in this range — identical to ASCII. This ensures backward compatibility.

अ is a Devanagari character. It falls in a Unicode range that requires 3 bytes in UTF-8. Devanagari script has hundreds of characters. The UTF-8 standard assigns 3 bytes to characters in this range to fit them into the encoding scheme.

Confirmed with Python: 'A'.encode('utf-8') = b'A' (1 byte), 'अ'.encode('utf-8') = b'\xe0\xa4\x85' (3 bytes).

---

**Q5. What is Spectre? How does it use cache timing to read protected memory without directly accessing it?**

Spectre is a hardware vulnerability discovered in 2018 affecting Intel, AMD, and ARM processors — virtually every computer. It exploits two CPU performance features: speculative execution and cache timing.

Speculative execution: the CPU executes instructions before it knows if they are needed, as a performance optimisation. If it turns out those instructions were not needed, the CPU discards the results. But the data loaded during speculative execution leaves traces in cache.

The attack: an attacker tricks the CPU into speculatively executing code that accesses protected memory — memory the attacker is not supposed to read. The CPU loads that protected data into cache. The CPU then realises the speculative execution was wrong and discards the visible result. But the data is still in cache.

The timing measurement: the attacker then probes cache by accessing various memory locations and measuring how long each access takes. A cache hit (data already in cache) takes ~4 CPU cycles. A cache miss (data not in cache, must fetch from RAM) takes ~200 cycles. By measuring these timing differences the attacker deduces what data was loaded into cache — and therefore what the protected memory contained. This is a timing side channel attack.

The attacker never directly reads the protected memory. They only measure time. But time reveals the secret.



# Interview Prep — Volume 01 Topic 3
# What is Operating System



---

## Subtopic 1 — Purpose of OS

**Q1. What is an operating system and what problem does it solve?**
An operating system is software that manages hardware resources and provides services to applications. Without an OS, every application would need to know how to talk to every piece of hardware directly — impossible to maintain. The OS abstracts hardware, manages resources like CPU time and RAM between multiple programs, and enforces security isolation so one process cannot access another's memory.

**Q2. What is privilege escalation and how does it relate to the OS?**
Privilege escalation is when an attacker breaks out of their restricted user-level access and gains OS or kernel level privileges. The OS separates user space (restricted) from kernel space (full access). Privilege escalation exploits vulnerabilities in the OS to cross this boundary. Once an attacker has kernel level access they control everything — can read any file, any memory, any network traffic.

---

## Subtopic 2 — The Kernel

**Q3. What is the kernel and what does it control?**
The kernel is the core of the operating system with complete hardware control. It manages CPU scheduling (which process runs when), RAM allocation and protection (which process owns which memory), file read/write operations, and network data. It runs in Ring 0 — the highest CPU privilege level — with direct hardware access.

**Q4. What is a rootkit and how does it target the kernel?**
A rootkit is malware that replaces or hooks into kernel components to hide itself and maintain persistence. Because the kernel controls what the OS sees — processes, files, network connections — a kernel-level rootkit can hide its own process from ps, hide its files from ls, and hide its network connections from netstat. Detection requires comparing kernel memory directly against known good state because the rootkit controls the tools you would normally use to find it.

---

## Subtopic 3 — User Space vs Kernel Space

**Q5. What is the difference between user space and kernel space?**
Kernel space is Ring 0 — full hardware access — where the kernel and device drivers run. User space is Ring 3 — restricted — where all applications run. The CPU enforces this separation at hardware level. An application in user space cannot access hardware directly or read another process's memory. It must make system calls to request kernel services. This separation is the foundation of OS security — breaking it is privilege escalation.

**Q6. What is a system call? Give an example.**
A system call is the only legitimate doorway between user space and kernel space. When an application needs a kernel service — reading a file, allocating memory, creating a network connection — it makes a system call. The CPU switches from Ring 3 to Ring 0, executes the kernel function, returns the result, switches back. Example: when you run `cat /etc/passwd`, the cat program makes an `open()` system call to open the file, then `read()` system calls to read the contents, then `write()` to display output. The strace tool shows every system call a process makes.

---

## Subtopic 4 — Processes

**Q7. What is the difference between a program and a process?**
A program is a file sitting on disk — code that is not running. A process is that program loaded into RAM and actively executing, with a PID assigned by the kernel, its own memory space, a state, and an owner. Chrome.exe on disk is a program. When you open Chrome, the OS creates a process — loads the code into RAM, assigns PID 2341, allocates virtual address space. When you close Chrome, the process dies and RAM is freed. The file on disk remains unchanged.

**Q8. How does fork() and exec() work? Why does this matter for security?**
fork() creates an identical copy of the current process — the child gets a new PID but starts as a clone of the parent. exec() replaces the child process with a new program. Together they are how every process is created on Linux. Security relevance: malware that injects into a legitimate process uses these mechanisms. A suspicious parent-child relationship — Word.exe creating cmd.exe creating PowerShell.exe — reveals a macro attack chain. Forensic investigators look at process trees specifically for unexpected parent-child relationships.

---

## Subtopic 5 — Memory Management

**Q9. What is virtual memory and why does it exist?**
Virtual memory is a system where each process gets its own private address space that looks like it owns all available memory — but this is an abstraction. The MMU hardware translates virtual addresses to physical RAM addresses using page tables. It exists to solve three problems: isolation (two processes can use the same virtual address without conflict because they map to different physical locations), size flexibility (processes can use more virtual space than physical RAM exists through swapping), and fragmentation (non-contiguous physical memory appears contiguous virtually).

**Q10. What is a buffer overflow and how does it work at the memory level?**
A buffer overflow happens when a program writes more data into a fixed-size buffer than it can hold. On the stack, the buffer sits adjacent to the return address — the virtual address the CPU jumps to when the function finishes. If an attacker overflows the buffer with carefully crafted data, the extra bytes overwrite the return address. When the function returns, the CPU loads the attacker-controlled address into the RIP register and jumps there. If that address points to attacker-controlled executable memory, the attacker controls execution. NX bit protection makes the stack non-executable (rw-p not r-xp) — modern exploit techniques like ROP chain together existing executable code to bypass this.

**Q11. What is fileless malware and why is it difficult to detect?**
Fileless malware lives entirely in RAM — no file is written to disk. It injects itself into a legitimate running process's memory (heap or code injection) or runs in tmpfs which is cleared on reboot. Traditional antivirus scans files on disk — fileless malware has no disk presence to scan. Detection requires memory forensics tools like Volatility that analyse RAM directly, looking for injected code, unusual memory permissions (rwxp regions), and process hollowing indicators.

---

## Subtopic 6 — File Systems

**Q12. What is an inode and what does it store?**
An inode is a metadata record that stores everything about a file except its filename. It stores: file size, owner (uid and gid), permissions (rwx for owner, group, others), three timestamps (atime, mtime, ctime), and pointers to the actual data blocks on disk. The filename lives in the directory entry which points to the inode number. This means two filenames can point to the same inode — called a hard link. The inode number is the file's real identity on disk.

**Q13. What are the three Linux file timestamps and which one matters most to a forensic investigator?**
atime (access time) — last time the file was read. mtime (modify time) — last time the file content was changed. ctime (change time) — last time the inode metadata changed. ctime matters most to forensic investigators because it cannot be changed by normal user commands — it updates automatically whenever the inode is touched. An attacker can fake atime and mtime using the touch -t command (timestomping) to make malware look like an old system file. But ctime reveals the truth — if mtime shows 2020 but ctime shows last Tuesday at 3 AM, the file was tampered with recently.

**Q14. What is slack space and why does it matter in forensics?**
Slack space is the unused bytes in a disk block when a file is smaller than the block size. On ext4 the block size is 4096 bytes. A file of 100 bytes still occupies one full 4096 byte block — the remaining 3996 bytes is slack space. When a file is deleted, the kernel marks the block as available but does not wipe the data. That old data sits in the block until another file overwrites it. Forensic tools scan slack space to recover fragments of deleted files — evidence that an attacker thought was gone.

**Q15. What does the file command do and why is it useful in security investigations?**
The file command reads the first few bytes of a file — called magic bytes or file signature — and identifies the true file type regardless of filename or extension. Every file type has a unique signature: ELF executables start with 7f 45 4c 46, JPEG images start with ff d8 ff, ZIP files start with 50 4b 03 04. Malware disguised as invoice.pdf that is actually a Linux executable is immediately exposed by file command returning ELF 64-bit executable. This bypasses the trivial attacker trick of renaming a malicious file with an innocent extension.

---

## Subtopic 7 — Device Drivers

**Q16. What is a device driver and why does it run in kernel space?**
A device driver is software that translates between the OS kernel and a specific hardware device. The kernel defines a standard interface — drivers implement that interface for their specific hardware. Drivers run in kernel space (Ring 0) because they need direct hardware access — they must read and write hardware registers, respond to hardware interrupts, and manage DMA transfers. This also means a vulnerable or malicious driver has the same privilege as the kernel itself — complete system control.

**Q17. What is a rootkit driver and how does it work?**
A rootkit driver is a malicious kernel module that replaces or hooks into legitimate kernel functions. Because it runs at Ring 0 — the same level as the kernel — it can intercept any system call, hide any process from ps, hide any file from ls, and hide any network connection from netstat. A keylogger rootkit hooks into the keyboard driver's interrupt handler at Step 8 of key processing — before the character reaches any application — and silently copies every keystroke. Detection requires memory forensics comparing live kernel memory against known good state, because the rootkit controls the normal detection tools.

**Q18. What is an interrupt and how does the kernel use it for device communication?**
An interrupt is a signal from hardware to the CPU meaning stop current work and handle this event immediately. When you press a key, the keyboard controller sends an interrupt signal. The CPU pauses whatever process is running, saves its state, and calls the kernel's interrupt handler for that interrupt number. The kernel calls the appropriate device driver which reads and processes the hardware event. After handling, the CPU restores the paused process and continues. Interrupts allow hardware to communicate with the CPU without the CPU constantly polling (checking) every device in a loop — polling would waste 100% CPU time.

**Q19. How would you use lsmod and dmesg to investigate a potentially compromised system?**
lsmod lists all currently loaded kernel modules — device drivers running in Ring 0. Compare the output against a known good baseline from a clean identical system. Any unexpected module not in the baseline is a rootkit candidate — investigate its name, size, and what it hooks into. dmesg shows the kernel event log with timestamps since boot — look for unexpected device connections at unusual times (USB drive at 3 AM), driver loading errors, and unusual kernel messages. Together they give you a picture of what kernel code is running and what hardware events occurred — two critical forensic data sources.

**Q20. What is the significance of vendor ID and product ID in dmesg USB output?**
Every USB device has a vendor ID (16-bit number identifying the manufacturer) and product ID (16-bit number identifying the specific device model). When a USB device connects, the kernel logs both in dmesg. idVendor=046d is Logitech. idVendor=1d6b is Linux Foundation (virtual devices). These IDs uniquely identify exactly which device was connected. In a forensic investigation, dmesg USB entries with timestamps tell you precisely what device was plugged in and when — a USB drive with vendor ID matching a known attacker tool, connected at 3 AM, is hard evidence of unauthorized access even if the drive was removed before investigators arrived.