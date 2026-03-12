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