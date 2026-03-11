# Day 05 — What is a Computer

**Date:** [10-03-2026]
**Volume:** 01 — Computer and Internet Foundations
**Topic:** What is a Computer

---

## Why This Topic Exists

Everything in cloud security runs on computers. Every server you
protect, every VM you run, every AWS EC2 instance you launch is
a computer at its core. If you do not understand what a computer
actually is at the hardware level you will struggle to understand
why security works the way it does.

When a security alert fires saying a process is consuming abnormal
CPU, or memory usage spiked suddenly, or disk is full unexpectedly
you need to know exactly what CPU, memory, and disk are and what
abnormal looks like. Without this foundation those alerts are
just words.

Real world — in 2017 WannaCry ransomware hit 200,000 computers
across 150 countries. It exploited how Windows manages memory and
network communication at a low level. Security engineers who
understood computer internals could analyze exactly what the
malware was doing. Those who only knew surface level security
could not.

---

## Subtopic 1 — Data vs Information

Data is raw unprocessed facts with no meaning on their own.
Information is data that has been processed and given context
so it means something.

Example:
Data — 192.168.1.105, 3, 2AM, failed
Information — IP address 192.168.1.105 made 3 failed login
attempts at 2AM. Investigate — possible brute force attack.

The numbers and words in the data line mean nothing alone.
Once you add context and process them they become information
that tells you something actionable.

Security relevance:
A SIEM takes raw data from thousands of log sources and processes
it into information — alerts, dashboards, reports. CloudTrail in
AWS records every API call as raw JSON data. A security engineer
writes rules that process that raw data into information —
someone called DeleteBucket at 3AM from an unknown IP.
That is actionable. The raw JSON alone told you nothing.

---

## Subtopic 2 — Hardware vs Software

Hardware is anything physical you can touch.
CPU, RAM, hard disk, keyboard, monitor, motherboard, network card.
Physical components. If it breaks you replace it physically.

Software is instructions that tell hardware what to do.
Operating system, applications, scripts, malware.
Instructions stored as data. If it breaks you rewrite or reinstall.

They depend on each other completely.
Hardware without software is an expensive paperweight.
Software without hardware has nothing to run on.

Security relevance:
Software attacks — malware, ransomware, SQL injection, phishing.
These attack the instructions running on the machine. Most attacks
a SOC analyst deals with are software attacks.

Hardware attacks — physical access to server, USB drop attack,
hardware keylogger physically attached to keyboard. Require
physical access. Less common but extremely dangerous because
software defenses cannot stop them.

Firmware attacks — the boundary between hardware and software.
Firmware is software permanently written into hardware chips
like BIOS or UEFI. Firmware attacks survive even if you wipe
and reinstall the operating system. The malware lives in the
hardware chip itself.

Real example — 2018 Spectre and Meltdown. Hardware level
vulnerabilities in Intel and AMD CPU design. Not software bugs.
Flaws in the physical CPU. No software patch could fully fix them
because the problem was in the hardware itself.

---

## Subtopic 3 — Input, Output, Storage Devices

Input devices — send data INTO the computer.
Keyboard, mouse, microphone, webcam, network card receiving data.

Output devices — send data OUT of the computer.
Monitor, speakers, printer, network card sending data.

Storage devices — store data permanently.
Hard disk, SSD, USB flash drive. Data stays when power is off.

Some devices are both — network card sends and receives.
USB drive reads and writes. Touch screen inputs and displays.

Security relevance:
Input device attack — keylogger records every keystroke.
Every password typed goes to the attacker.

Output device attack — screen capture malware takes screenshots
every few seconds and sends them to the attacker.

Storage device attack — ransomware encrypts everything on
storage devices and demands payment for decryption key.

Target breach 2013 — malware installed on Point of Sale card
readers — input devices. Malware captured credit card data as
customers swiped. 40 million credit card numbers stolen through
a compromised input device.

DLP — Data Loss Prevention — monitors all three. Watches what
enters, what leaves, and what is stored. Blocks sensitive data
from being copied to USB or emailed externally.

---

## Subtopic 4 — CPU

CPU stands for Central Processing Unit. The brain of the computer.
Every instruction that any software runs is executed by the CPU.

Speed measured in GHz — GigaHertz.
1 GHz means 1 billion cycles per second.
Intel Core Ultra 5 125H runs up to 4.5GHz.

Modern CPUs have multiple cores.
Each core is an independent processing unit.
18 cores means 18 streams of instructions simultaneously.

Security relevance:
High CPU usage alert — server normally at 10% suddenly at 95%
is suspicious. Common cause is crypto mining malware — uses your
CPU to mine cryptocurrency for the attacker.

Fileless malware — runs entirely in CPU and RAM without writing
to disk. Traditional antivirus cannot find it. Only exists as
CPU instructions being executed. Disappears on reboot.

Spectre and Meltdown 2018 — hardware vulnerabilities in CPU
design. Allowed malicious programs to read memory they should
not access. Affected almost every CPU made in the last 20 years.
Patches slowed CPU performance by up to 30%.

---

## Subtopic 5 — RAM

RAM stands for Random Access Memory.
Temporary workspace of the computer.
Everything currently running lives in RAM.

RAM is fast — CPU reads from RAM in nanoseconds.
Reading from hard disk takes milliseconds.
RAM bridges the speed gap between fast CPU and slow disk.

RAM is volatile — data disappears when power is cut.
Volatile means requires power to hold data.
Kali VM has 4.3GB RAM allocated.

Security relevance:
Reason 1 — Fileless malware lives in RAM only. Never writes
to disk. Antivirus scans files on disk and finds nothing.
Memory forensics — examining contents of RAM — is the only
way to find it.

Reason 2 — Passwords and encryption keys live in RAM while
in use. Memory scraping attack extracts passwords, encryption
keys, and session tokens directly from memory.
Target breach — malware scraped RAM on Point of Sale terminals
to steal credit card numbers before they were encrypted.

Reason 3 — RAM usage spikes are security indicators. Server
suddenly at 95% RAM when it normally uses 20% means something
is wrong. Could be malware or denial of service attack.

Memory dump — exact copy of everything in RAM at a specific
moment. Used in digital forensics to investigate what was
running during an incident. Tool: Volatility. Covered in
Volume 06 Digital Forensics.

---

## Subtopic 6 — Disk

Disk is permanent storage. Non-volatile — data survives without
power. Opposite of RAM which is volatile.

HDD — Hard Disk Drive. Spinning magnetic platters.
Slower. Cheaper per GB. Mechanical failure risk.

SSD — Solid State Drive. Flash memory chips. No moving parts.
Much faster. More reliable.

NVMe — Non-Volatile Memory Express. Fast connection standard
for SSDs. Your laptop uses NVMe SSD.

HDD reads at 100 to 150 MB/s.
NVMe SSD reads at 3000 to 7000 MB/s.

Security relevance:
Reason 1 — Log files live on disk. /var/log on Linux,
Event Viewer on Windows, CloudTrail logs in S3 on AWS.
Attacker who deletes logs destroys evidence.
Log forwarding to SIEM exists to prevent this.

Reason 2 — Malware lives on disk. Antivirus scans disk files
looking for malicious signatures. Disk forensics finds malware
and identifies all files it created or modified.

Reason 3 — Ransomware targets disk. WannaCry 2017 encrypted
disk contents on 200,000 machines across 150 countries.

Reason 4 — Disk full stops log writing. No logs means no SIEM
alerts. Attacker can intentionally fill disk to blind security
monitoring before launching real attack. Called denial of
logging attack.

Disk imaging — bit for bit copy of entire disk. Created before
investigation so original evidence is never touched.
Covered in Volume 06 Digital Forensics.

---

## Subtopic 7 — GPU

GPU stands for Graphics Processing Unit.
Originally designed to render graphics.

CPU — few cores, each extremely powerful. Sequential tasks.
GPU — thousands of cores, less powerful individually.
Parallel tasks — thousands of calculations simultaneously.
Modern GPU has 3000 to 10000 cores.

Security relevance:
Password cracking — GPU calculates billions of hashes per second.
Tool: Hashcat. Password hash that takes CPU 10 years to crack
can be cracked by GPU in hours. Covered in Volume 05.

Crypto mining malware — attackers compromise machines and use
victim GPU to mine cryptocurrency. Monitoring GPU usage on
servers is a security task. Servers do not normally use GPU
heavily — unexpected high GPU usage is a red flag.

Machine learning for security — AWS GuardDuty uses machine
learning models trained on GPU to detect unusual behavior
in AWS accounts. Configured in Volume 07 and Volume 11.

AWS GuardDuty — AWS service that continuously monitors your
account for malicious activity using machine learning.
Analyzes CloudTrail logs, VPC Flow Logs, and DNS logs.

---

## Subtopic 8 — Motherboard

Motherboard is the main circuit board. Physical backbone that
connects every component. CPU, RAM, disk, GPU, network card,
USB ports — everything connects through the motherboard.

Key components:
CPU socket — where CPU sits.
RAM slots — usually 2 or 4 slots.
PCIe slots — where GPU and network cards connect.
PCIe — Peripheral Component Interconnect Express.
SATA ports — where hard drives connect.
M.2 slot — where NVMe SSDs connect directly.
BIOS/UEFI chip — firmware that starts computer before OS.
USB controllers — manage all USB ports.

Security relevance:
Physical access to motherboard means game over.
Attacker can reset BIOS password, perform cold boot attack,
install hardware keyloggers, or replace components.

Cold boot attack — attacker removes RAM sticks while powered
or recently powered off. RAM retains data briefly when cold.
Attacker reads encryption keys and passwords from RAM chips.

Supply chain attack — compromised motherboard from factory.
2018 Bloomberg report — alleged spy chips implanted on server
motherboards. Compromised motherboard from factory is nearly
impossible to detect and gives permanent access.

---

## Subtopic 9 — BIOS and UEFI

BIOS — Basic Input Output System. Older standard from 1980s.
Text only. Limited features. Max disk size 2TB. Slow boot.

UEFI — Unified Extensible Firmware Interface. Modern replacement.
Graphical interface. Mouse support. Supports disks over 2TB.
Faster boot. Has Secure Boot feature. All modern computers
use UEFI. Terms BIOS and UEFI used interchangeably today.

Boot sequence in exact order:
Step 1 — Power supply sends power to motherboard.
Step 2 — BIOS or UEFI firmware starts from chip on motherboard.
Step 3 — POST runs — Power On Self Test. Checks all hardware.
Step 4 — BIOS looks for bootable device.
Step 5 — BIOS loads bootloader from bootable device.
Step 6 — Bootloader loads OS kernel into RAM.
Step 7 — Kernel takes control. OS starts. Login screen appears.

Secure Boot — UEFI feature that checks every piece of software
in boot process has valid digital signature from trusted source.
If bootloader or kernel is unsigned or modified Secure Boot
refuses to load it. Prevents bootkits.

Security relevance:
BIOS malware survives OS reinstalls and disk replacement.
Only removal is reflashing BIOS chip with clean firmware.
Most antivirus tools cannot scan BIOS.

LoJax 2018 — first confirmed UEFI rootkit found in the wild.
Developed by APT28 — Advanced Persistent Threat 28 — also
known as Fancy Bear. Russian military intelligence hacking group.
Also responsible for 2016 US Democratic National Committee breach.
LoJax infected UEFI firmware and survived complete OS reinstalls.

Secure Boot bypass — BootHole vulnerability 2022 allowed
attackers to bypass Secure Boot on millions of Linux and
Windows machines.

BIOS password bypass with physical access — remove small
battery on motherboard for 30 seconds resets all BIOS settings
including password. This is why physical server security matters.

Verified output from Kali VM:
Vendor — innotek GmbH — VirtualBox virtual BIOS.
Secure Boot — not active in VM. Normal for VirtualBox.
Boot mode — legacy BIOS mode in VM. Real laptop uses UEFI.

---

## Key Lessons

1. Every computer component is a potential attack surface.
2. Fileless malware lives in RAM not disk — memory forensics
   is the only detection method.
3. BIOS malware is the most persistent — survives everything.
4. Physical access to hardware bypasses most software security.
5. High CPU or RAM usage on idle server = investigate immediately.
6. Disk full = logs stop = security monitoring blind.
7. GPU is used by attackers for password cracking and crypto
   mining — monitor GPU usage on servers.

---

## Connection to Previous Topics

Volume 00 taught what cloud security is and where it operates.
Topic 1 builds the hardware foundation everything else sits on.
You cannot secure what you do not understand.
Every cloud service — EC2, S3, Lambda — runs on physical
hardware in AWS(Amazon Web services) data centers. Same components. Same attack
surfaces. Larger scale.