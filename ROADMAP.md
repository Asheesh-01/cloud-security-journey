# ☁️ Cloud Security Engineering — Master Roadmap 2026

> Zero to Cloud Security Engineer · No Course · No Video · Only Work
>
> 11 Volumes · 80% Practical · Job-Focused · No Shortcuts

---

<details>
<summary><b>⚠️ Honest Assessment — Read This First</b></summary>

<br>

This roadmap will get you a job. How good a job depends entirely on how strictly you execute it.

| What You Build | Outcome |
|----------------|---------|
| Volumes 1–7 + active GitHub | ₹4 – 8 LPA |
| All volumes + signature project + public presence | ₹15 – 25 LPA |
| Above + real problem solved + interviewers find you | ₹25 – 40 LPA |
| Specialist / Architect (5+ years) | ₹35 – 70 LPA+ |
| Product Company / MNC Fresher | ₹12 – 25 LPA (requires signature project) |

**The highest package is NOT guaranteed by finishing a roadmap.**
It requires a signature project + strong public presence + interview prep from Day 1.
This roadmap builds all three simultaneously.

> ℹ️ **English matters.** At ₹20+ LPA companies, your README, LinkedIn posts, and spoken communication are all evaluated. English improvement runs parallel to every volume — not optional.

</details>

---

<details>
<summary><b>🎯 Master Strategy</b></summary>

<br>

### Three Pillars — All Three Run at the Same Time

| Pillar | What It Means | How |
|--------|--------------|-----|
| **Technical** | 80% hands-on. Every topic = commands run in terminal. | Notes + Labs + Screenshots every day |
| **Public Proof** | Recruiters must find you before you apply. | GitHub daily · LinkedIn every 2 days · Hashnode per volume |
| **Interview Ready** | 5 questions per topic from Day 1. Never wait. | `interview-prep/` folder — written answers for every topic |

### Daily Non-Negotiable Rules

- 2 hours minimum every single day — no exceptions
- 1 GitHub commit every day — even a typo fix counts
- LinkedIn post every 2 days — 150 words — your own words — include GitHub link
- 1 Hashnode blog per volume completed — with screenshots and lab output
- 5 interview questions answered in writing per topic
- 10 minutes English reading + 10 minutes rewriting — every day

### The 80/20 Model

| Split | What It Means |
|-------|--------------|
| 20% Theory | Read the concept. Understand the WHY. Draw the diagram by hand. |
| 80% Practical | Run the commands. Break things. Fix them. Screenshot everything. |
| 0% Video | No YouTube. No Udemy. No shortcuts. You and the terminal. |
| 0% Passive Reading | Reading without running commands = zero learning. |

</details>

---

<details>
<summary><b>🇬🇧 English Improvement — Parallel Track (Not Optional)</b></summary>

<br>

At ₹20+ LPA companies your README, LinkedIn post, and interview communication are all evaluated.
Weak English = rejection even with strong technical skills.

### Daily Practice — 20 Minutes

- **Minutes 1–10:** Read one paragraph from any English tech blog slowly. AWS docs, Medium, or Grammarly blog. Understand every sentence.
- **Minutes 11–20:** Rewrite that same paragraph in your own words without looking. Then compare. Spot the differences.

### LinkedIn Post Structure — Write After Every Topic

```
What I learned today →
Why it matters in security →
One real example from my lab →
GitHub link
```

150 words maximum. Simple sentences. Write yourself first. Never copy AI output directly.

### GitHub README Standard — Every Project

Every README must have:
- What this project does
- What tools were used
- What you learned
- Screenshots of real output

Use Grammarly free version before publishing.

</details>

---

## 📚 Volumes

---

<details>
<summary><b>Volume 00 — Orientation & Environment Setup</b></summary>

<br>

> Complete this before anything else. This is the foundation of your entire journey.

---

<details>
<summary>Topic 1 — What is Cloud Security</summary>

<br>

- What is Cloud Security and why it matters
- Cloud vs On-Premise infrastructure
- Shared Responsibility Model — who owns what
- Identity is the new perimeter
- Misconfiguration risks
- Cloud attack surface overview

**Tools:** AWS Documentation, Cloud provider websites

</details>

<details>
<summary>Topic 2 — Understanding the Cloud Ecosystem</summary>

<br>

- What is AWS
- What is Microsoft Azure
- What is Google Cloud Platform
- Global Data Centers and Regions
- Availability Zones
- Cloud Service Models — IaaS, PaaS, SaaS

**Tools:** AWS Console (exploration only), Azure Portal (exploration), GCP Console (exploration)

</details>

<details>
<summary>Topic 3 — Career Roles & Goal Setting</summary>

<br>

- SOC Analyst — what they do daily
- Cloud Security Engineer — what they do daily
- Detection Engineer — what they do daily
- Penetration Tester — what they do daily
- Incident Responder — what they do daily
- Setting your 12–18 month target role and salary

**Tools:** LinkedIn (read real job descriptions for each role)

</details>

<details>
<summary>Topic 4 — Environment Setup</summary>

<br>

- Install Ubuntu (dual boot or VM)
- Install Git and configure username and email
- Create GitHub account and first repository
- Install VS Code
- Create your project folder structure
- Create AWS Free Tier account at aws.amazon.com/free

**Tools:** Ubuntu, Git, GitHub, VS Code, AWS Free Tier

</details>

<details>
<summary>Topic 5 — Learning Strategy & Public Learning Start</summary>

<br>

- Understand the 80/20 model
- Build the documentation habit — notes every single day
- Daily GitHub commit habit — never break the streak
- Publish your first LinkedIn post — introduce yourself and your journey
- Create an Introduction README on GitHub
- Write your personal learning rules

**Tools:** GitHub, LinkedIn, Hashnode, Markdown

</details>

---

**✅ Volume 00 Complete When:**
- Ubuntu installed and working
- Git + GitHub active
- VS Code installed
- AWS Free Tier account created
- LinkedIn profile exists
- Introduction README published on GitHub

</details>

---

<details>
<summary><b>Volume 01 — Computer & Internet Foundations</b></summary>

<br>

> Human → Computer → Internet → Cybersecurity
> Build the mental model first. Everything later depends on this.

---

<details>
<summary>Topic 1 — What is a Computer</summary>

<br>

- Data vs Information
- Hardware vs Software
- Input devices, Output devices, Storage devices
- CPU — what it does
- RAM — what it stores temporarily
- Disk — what it stores permanently
- GPU — what it does
- Motherboard — how everything connects
- BIOS and UEFI — what happens before the OS loads

**Tools:** None — concept only

</details>

<details>
<summary>Topic 2 — How Computers Process Data</summary>

<br>

- Binary numbers — why computers use 0 and 1
- Bits and Bytes
- Number systems — Binary, Decimal, Hexadecimal
- Character encoding — ASCII, UTF-8
- Instruction cycle — Fetch, Decode, Execute
- CPU Registers
- Cache memory — L1, L2, L3

**Tools:** Python (run number conversions in terminal)

</details>

<details>
<summary>Topic 3 — What is an Operating System</summary>

<br>

- Purpose of an OS
- Kernel — what it is and what it controls
- User Space vs Kernel Space
- Processes — what they are
- Memory Management — how RAM is allocated
- File Systems — how files are organized
- Device Drivers — how hardware talks to software

**Tools:** `htop`, `ps`, `strace`, `uname -r`

</details>

<details>
<summary>Topic 4 — Types of Operating Systems</summary>

<br>

- Desktop OS — Windows, macOS, Ubuntu
- Server OS — Ubuntu Server, Windows Server
- Mobile OS — Android, iOS
- Embedded OS — firmware in routers and cameras
- Real-Time OS — used in industrial systems

**Tools:** None — concept only

</details>

<details>
<summary>Topic 5 — Windows OS Basics</summary>

<br>

- Windows architecture overview
- NTFS file system
- Folder structure — C:\Windows, C:\Users, C:\Program Files
- Services — what they are and how to view them
- Registry — what it stores
- Users and Groups
- Event Viewer — where Windows logs are stored

**Tools:** Task Manager, Event Viewer, PowerShell, `cmd`

</details>

<details>
<summary>Topic 6 — Linux OS Basics</summary>

<br>

- Linux distributions — Ubuntu, Kali, CentOS, Arch
- Kernel — how to check version
- Shell — bash, zsh
- Package managers — apt, dnf, pacman
- Directory structure — /, /home, /etc, /var, /bin
- File permissions — read, write, execute

**Tools:** `bash`, `apt`, `uname`, `ls`, `pwd`

</details>

<details>
<summary>Topic 7 — What is Software</summary>

<br>

- Application software vs System software
- Open source vs Closed source
- How software is installed on Linux vs Windows

**Tools:** None — concept only

</details>

<details>
<summary>Topic 8 — What is Programming</summary>

<br>

- What is source code
- Compiled vs Interpreted languages
- High-level vs Low-level languages
- Why Python is used in security

**Tools:** Python 3 (write and run a basic script)

</details>

<details>
<summary>Topic 9 — What is the Internet</summary>

<br>

- Network of networks
- What is an ISP
- Client-Server model — who requests, who responds

**Tools:** Browser, `ping`

</details>

<details>
<summary>Topic 10 — How the Internet Works</summary>

<br>

- IP address — what it is
- Domain name — what google.com really is
- DNS — how domain names become IP addresses
- Routing — how packets travel
- Packets — how data is broken up and sent
- Ports — what they are and why they matter

**Tools:** `ping`, `traceroute`, `nslookup`

</details>

<details>
<summary>Topic 11 — What is a Network</summary>

<br>

- LAN — Local Area Network
- WAN — Wide Area Network
- MAN — Metropolitan Area Network
- PAN — Personal Area Network

**Tools:** `ifconfig`, `ip addr`

</details>

<details>
<summary>Topic 12 — What is Cybersecurity</summary>

<br>

- Why cybersecurity exists
- What are digital assets
- Threat — definition and example
- Vulnerability — definition and example
- Risk — definition and example
- Difference between the three

**Tools:** None — concept only

</details>

<details>
<summary>Topic 13 — Types of Cyber Threats</summary>

<br>

- Malware — umbrella term
- Virus — how it spreads
- Worm — self-replicating, no user action needed
- Trojan — disguised as legitimate software
- Ransomware — encrypts your files, demands payment
- Spyware — silently collects data
- Botnet — network of infected machines

**Tools:** ClamAV (scan your own system)

</details>

<details>
<summary>Topic 14 — Hacker Types</summary>

<br>

- Black Hat — malicious, illegal
- White Hat — ethical, hired to test
- Gray Hat — in between
- Script Kiddie — uses tools without understanding them
- Hacktivist — hacks for a political cause
- Nation State — government-sponsored attacker

**Tools:** None — concept only

</details>

<details>
<summary>Topic 15 — Basic Security Principles</summary>

<br>

- CIA Triad — Confidentiality, Integrity, Availability
- Defense in Depth — multiple security layers
- Least Privilege — only the access needed, nothing more
- Zero Trust — never trust, always verify

**Tools:** None — concept only

</details>

<details>
<summary>Topic 16 — Authentication & Authorization</summary>

<br>

- Authentication — proving who you are
- Authorization — what you are allowed to do after proving identity
- Username and Password
- Multi-Factor Authentication (MFA) — something you know + something you have
- Biometrics
- Tokens

**Tools:** Google Authenticator (set up on a test account)

</details>

<details>
<summary>Topic 17 — Data Protection Basics</summary>

<br>

- Encryption — scrambles data, reversible with the right key
- Hashing — one-way, cannot be reversed, used for passwords
- Encoding — not security, just format change (Base64)
- Why you hash passwords instead of encrypting them

**Tools:** `openssl` (run hash and encrypt commands in terminal)

</details>

<details>
<summary>Topic 18 — Security Roles in the Industry</summary>

<br>

- SOC Analyst — monitors alerts, triages incidents
- Security Engineer — builds and maintains security tools
- Penetration Tester — finds vulnerabilities before attackers do
- Incident Responder — handles active attacks
- Cloud Security Engineer — secures cloud infrastructure

**Tools:** None — read 3 real job descriptions for each role on LinkedIn

</details>

<details>
<summary>Topic 19 — Ethical & Legal Concepts</summary>

<br>

- Cyber laws in India — IT Act 2000
- India DPDP Act — data privacy
- Compliance — what it means in the industry
- Ethics in security — never test what you do not own
- Responsible disclosure — reporting vulnerabilities properly

**Tools:** None — concept only

</details>

<details>
<summary>Topic 20 — Security Learning Platforms</summary>

<br>

- TryHackMe — guided rooms for beginners (max 4 rooms, supplement only)
- HackTheBox — intermediate and advanced (from Volume 6 onward)
- OverTheWire Bandit — Linux command practice through puzzles (Level 0–15 only)

**Tools:** OpenVPN (to connect to lab environments)

</details>

---

**📝 Volume 01 Interview Prep — Write answers in `interview-prep/Vol1_QA.md`**

1. What is the difference between a threat, a vulnerability, and a risk? Give a real example of each.
2. Explain the CIA Triad. Give one attack that breaks each of the three properties.
3. What is the difference between authentication and authorization?
4. What is hashing? How is it different from encryption? Why do we hash passwords?
5. A user says their computer is slow. As a security analyst, what are 3 security-related causes you would investigate?

**✅ Volume 01 Complete When:**
- All 20 topics done — notes + labs + screenshots committed to GitHub
- Interview prep answers written for all 5 questions
- All commands run in terminal — no exceptions
- 1 Hashnode blog published — roadmap overview post

</details>

---

<details>
<summary><b>Volume 02 — Operating Systems Deep Dive</b></summary>

<br>

> Windows + Linux internals. This is what separates a real engineer from a beginner.

---

<details>
<summary>Topic 1 — OS Architecture</summary>

<br>

- Kernel in depth — monolithic vs microkernel
- User space vs Kernel space in detail
- System calls — how user programs talk to the kernel
- Interrupts — hardware and software interrupts
- Boot process — BIOS → UEFI → Bootloader → Kernel → Init
- BIOS vs UEFI differences

**Tools:** `dmesg`, `strace`

</details>

<details>
<summary>Topic 2 — Windows File System</summary>

<br>

- NTFS structure
- File attributes — hidden, read-only, system
- Alternate Data Streams (ADS) — how malware hides data inside files
- Permissions (ACL) — who can read, write, execute a file

**Tools:** `icacls`, `attrib`, `fsutil`

</details>

<details>
<summary>Topic 3 — Windows Users & Groups</summary>

<br>

- Local users vs Domain users (concept only)
- Built-in accounts — Administrator, Guest, SYSTEM
- User profiles — where they are stored on disk
- How to create, delete, and modify users

**Tools:** `lusrmgr.msc`, `net user`

</details>

<details>
<summary>Topic 4 — Windows Processes & Services</summary>

<br>

- Process vs Thread — the difference
- Services — background processes that run without a logged-in user
- Startup programs — what launches automatically at boot
- How attackers abuse services to persist on a system

**Tools:** Task Manager, Process Explorer, `services.msc`

</details>

<details>
<summary>Topic 5 — Windows Registry</summary>

<br>

- What the Registry is and why it exists
- Registry hives — HKLM, HKCU, HKCR, HKU, HKCC
- Keys and Values — how data is stored
- Common persistence locations that attackers write to

**Tools:** `regedit`

</details>

<details>
<summary>Topic 6 — Windows Event Logging</summary>

<br>

- Application, Security, and System log channels
- Event ID 4624 — successful login
- Event ID 4625 — failed login
- Event ID 4648 — login using explicit credentials
- Why these three Event IDs matter for security analysts

**Tools:** Event Viewer

</details>

<details>
<summary>Topic 7 — Windows Permissions & Access Control</summary>

<br>

- NTFS permissions — Full Control, Modify, Read & Execute, Read, Write
- Share permissions — how network shares are controlled
- User Account Control (UAC) — what it does and why it exists
- How attackers bypass UAC (concept)

**Tools:** `icacls`

</details>

<details>
<summary>Topic 8 — Windows Networking Basics</summary>

<br>

- IP configuration on Windows
- Windows Firewall — inbound and outbound rules
- Open ports — how to check what is listening

**Tools:** `ipconfig`, `netstat`, `netsh`

</details>

<details>
<summary>Topic 9 — Windows Security Features</summary>

<br>

- Windows Defender — how it works
- Windows Firewall — how it differs from Defender
- BitLocker — full disk encryption
- SmartScreen — application reputation filtering

**Tools:** Windows Security Center

</details>

<details>
<summary>Topic 10 — Linux File System Structure</summary>

<br>

- `/` — root of everything
- `/home` — user personal files
- `/etc` — system configuration files
- `/var` — logs and variable data
- `/usr` — user-installed programs
- `/bin` — essential system binaries
- `/sbin` — system administration binaries
- `/opt` — optional third-party software

**Tools:** `ls`, `tree`

</details>

<details>
<summary>Topic 11 — Linux File Permissions</summary>

<br>

- `r` read, `w` write, `x` execute
- Three sets — owner, group, others
- `chmod` — change permissions (numeric and symbolic)
- `chown` — change file owner
- Sticky bit — only the owner can delete the file
- SUID — file runs as the file owner
- SGID — file runs as the file group
- Security risk when SUID is set incorrectly

**Tools:** `chmod`, `chown`, `stat`

</details>

<details>
<summary>Topic 12 — Linux Users & Groups</summary>

<br>

- `useradd` — create a new user
- `userdel` — delete a user
- `passwd` — set or change a password
- `groups` — check group membership
- `/etc/passwd` — stores user account information
- `/etc/shadow` — stores hashed passwords (why it is separate from passwd)

**Tools:** `useradd`, `passwd`, `id`

</details>

<details>
<summary>Topic 13 — Linux Processes</summary>

<br>

- Foreground vs Background processes
- Signals — SIGKILL, SIGTERM, SIGHUP and what they do
- Process tree — parent and child processes
- `kill` — sending signals to processes
- `nice` — setting process priority
- How attackers hide malicious processes

**Tools:** `ps`, `top`, `htop`, `kill`, `pstree`

</details>

<details>
<summary>Topic 14 — Linux Package Management</summary>

<br>

- `apt` — Debian and Ubuntu based systems
- `dnf` / `yum` — Red Hat and Fedora based systems
- `pacman` — Arch Linux
- When to use each

**Tools:** `apt`, `dnf`

</details>

<details>
<summary>Topic 15 — Linux Services</summary>

<br>

- `systemd` — the init system used by most modern Linux
- Service states — active, inactive, failed
- Enable a service — starts at boot
- Disable a service — does not start at boot
- `journalctl` — view logs from any service

**Tools:** `systemctl`

</details>

<details>
<summary>Topic 16 — Linux Logging</summary>

<br>

- `/var/log` directory — what lives here
- `auth.log` — all authentication events
- `syslog` — general system messages
- `journalctl` — systemd journal
- How to search logs with `grep`

**Tools:** `journalctl`, `tail`, `grep`

</details>

<details>
<summary>Topic 17 — Linux Disk Management</summary>

<br>

- Partitions — how disks are divided
- Mount points — how partitions attach to the file system
- File systems — ext4, NTFS, FAT32
- `df` — check disk space usage
- `du` — check directory size

**Tools:** `lsblk`, `mount`, `df`, `du`

</details>

<details>
<summary>Topic 18 — Security Hardening</summary>

<br>

- Patch management — keep systems updated
- Firewall rules — block what is not needed
- Disable unused services — reduce attack surface
- Least privilege for users — no one gets more than they need
- Run Lynis security audit at the end of this topic:

```bash
sudo apt install lynis
sudo lynis audit system
```

Screenshot the output. Write a 200-word analysis of what it found. This is your first real security report artifact.

**Tools:** `ufw`, `systemctl`, `lynis`

</details>

---

**📝 Volume 02 Interview Prep — Write answers in `interview-prep/Vol2_QA.md`**

1. What is the Windows Registry? Name three hives and explain what each stores.
2. A Linux file has `rwxr-xr--`. Explain exactly what each set of three bits means.
3. What is SUID? Give a security risk example of SUID being set incorrectly.
4. What are Windows Event IDs 4624, 4625, and 4648? Why do security analysts care about these?
5. What is the difference between `/etc/passwd` and `/etc/shadow`? Why does this separation exist?

**✅ Volume 02 Complete When:**
- All 18 topics done — notes + labs + screenshots committed
- Lynis scan done — screenshot + 200-word analysis committed
- Interview prep answers written for all 5 questions
- 1 Hashnode blog published for this volume

</details>

---

<details>
<summary><b>Volume 03 — Networking From Zero</b></summary>

<br>

> If you don't understand packets, you can't do security. Period.

---

<details>
<summary>Topic 1 — Network Fundamentals</summary>

<br>

- What is a network
- Client-Server model — who requests, who responds
- Peer-to-Peer model — no central server

**Tools:** None — concept only

</details>

<details>
<summary>Topic 2 — OSI Model</summary>

<br>

- Layer 1 Physical — cables and signals
- Layer 2 Data Link — MAC addresses, switches
- Layer 3 Network — IP addresses, routers
- Layer 4 Transport — TCP, UDP, ports
- Layer 5 Session — managing connections
- Layer 6 Presentation — encryption, encoding, compression
- Layer 7 Application — HTTP, DNS, SMTP

Draw this by hand first. Memorize all 7 layers and what they do.

**Tools:** Paper and pen — draw it

</details>

<details>
<summary>Topic 3 — TCP/IP Model</summary>

<br>

- Network Interface Layer — maps to OSI Layer 1 and 2
- Internet Layer — maps to OSI Layer 3
- Transport Layer — maps to OSI Layer 4
- Application Layer — maps to OSI Layer 5, 6, and 7

**Tools:** None — concept only

</details>

<details>
<summary>Topic 4 — IP Addressing</summary>

<br>

- IPv4 — format and ranges
- IPv6 — why it exists, format
- Public IP vs Private IP
- Static IP vs Dynamic IP
- APIPA — what happens when DHCP fails

**Tools:** `ip addr`, `ifconfig`

</details>

<details>
<summary>Topic 5 — Subnetting</summary>

<br>

- CIDR notation — what /24 means
- Subnet masks — 255.255.255.0 explained
- Network address — first address in the range
- Broadcast address — last address in the range
- Usable host range — addresses you can assign
- Practice /24, /28, /30 by hand before using a calculator

**Tools:** `ipcalc` (use only after calculating by hand first)

</details>

<details>
<summary>Topic 6 — MAC Addressing</summary>

<br>

- MAC address format — 6 bytes, written in hexadecimal
- ARP — how IP addresses map to MAC addresses
- ARP Spoofing — how attackers use fake ARP replies

**Tools:** `arp`, `ip neigh`

</details>

<details>
<summary>Topic 7 — Network Devices</summary>

<br>

- Router — routes packets between different networks
- Switch — connects devices within the same network
- Hub — old device, broadcasts to all ports (no longer used)
- Firewall — allows or blocks traffic based on rules
- IDS — Intrusion Detection System, detects but does not block
- IPS — Intrusion Prevention System, detects and blocks

**Tools:** None — concept only

</details>

<details>
<summary>Topic 8 — Ports & Services</summary>

<br>

Memorize these 20 ports without looking:

| Port | Service |
|------|---------|
| 21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 110 | POP3 |
| 143 | IMAP |
| 443 | HTTPS |
| 445 | SMB |
| 3306 | MySQL |
| 3389 | RDP |
| 8080 | HTTP-alt |
| 8443 | HTTPS-alt |
| 27017 | MongoDB |
| 5432 | PostgreSQL |
| 6379 | Redis |
| 9200 | Elasticsearch |
| 5601 | Kibana |
| 2375 | Docker |

**Tools:** `netstat`, `ss`

</details>

<details>
<summary>Topic 9 — Transport Protocols</summary>

<br>

- TCP — connection-oriented
  - 3-way handshake: SYN → SYN-ACK → ACK
  - Reliable, ordered delivery
- UDP — connectionless
  - Faster, no guarantee of delivery
- When to use TCP — where reliability matters (HTTP, SSH, FTP)
- When to use UDP — where speed matters (DNS, video calls, gaming)

**Tools:** `tcpdump`, Wireshark

</details>

<details>
<summary>Topic 10 — Application Protocols</summary>

<br>

- HTTP — how web pages are requested and delivered
- HTTPS — HTTP with TLS encryption
- FTP — transferring files between systems
- SMTP — sending email
- POP3 and IMAP — receiving email
- DNS — resolving domain names to IP addresses
- SSH — encrypted remote access to a machine

**Tools:** `curl`, `wget`, `dig`, `ssh`

</details>

<details>
<summary>Topic 11 — DNS System</summary>

<br>

- DNS hierarchy — Root → TLD → Domain → Subdomain
- Record types:
  - A — maps domain to IPv4
  - AAAA — maps domain to IPv6
  - MX — mail server
  - CNAME — alias for another domain
  - TXT — text records (used for SPF, DKIM)
  - NS — nameserver
  - PTR — reverse lookup
- DNS resolution process step by step
- DNS cache poisoning — attack concept

**Tools:** `dig`, `nslookup`

</details>

<details>
<summary>Topic 12 — DHCP</summary>

<br>

- DORA process:
  - Discover — client broadcasts looking for a DHCP server
  - Offer — server offers an IP address
  - Request — client requests the offered IP
  - Acknowledge — server confirms the assignment
- DHCP options — gateway, DNS server, lease time

**Tools:** `dhclient`

</details>

<details>
<summary>Topic 13 — Routing</summary>

<br>

- Routing table — how the OS decides where to send packets
- Default gateway — the exit point for unknown destinations
- Static routing — manually configured routes
- Dynamic routing — routes learned automatically (OSPF, BGP concepts)

**Tools:** `ip route`, `traceroute`

</details>

<details>
<summary>Topic 14 — Firewall Basics</summary>

<br>

- Stateless firewall — checks each packet independently
- Stateful firewall — tracks connection state
- Inbound rules — what traffic is allowed in
- Outbound rules — what traffic is allowed out
- iptables chains — INPUT, OUTPUT, FORWARD

**Tools:** `iptables`, `ufw`

</details>

<details>
<summary>Topic 15 — VPN Concepts</summary>

<br>

- Tunneling — wrapping traffic inside another protocol
- Split tunneling — only some traffic goes through VPN
- IPSec VPN — works at the network layer
- SSL VPN — works at the application layer

**Tools:** OpenVPN, WireGuard

</details>

<details>
<summary>Topic 16 — Wireless Networking</summary>

<br>

- Wi-Fi standards — 802.11a/b/g/n/ac/ax
- WPA2 — current widely used standard
- WPA3 — newer and stronger
- SSID — the network name
- WPS — why it should be disabled (security risk)

**Tools:** `iwconfig`, `nmcli`

</details>

<details>
<summary>Topic 17 — Network Monitoring</summary>

<br>

- Packet capture — capturing raw network traffic
- Traffic analysis — reading and understanding what you captured
- Wireshark filters — filter by IP, protocol, or port
- `tcpdump` — command-line packet capture

**Tools:** Wireshark, `tcpdump`

</details>

<details>
<summary>Topic 18 — Network Scanning</summary>

<br>

- Host discovery — finding live hosts on a network
- Port scanning — finding open ports on a host
- Service detection — what software is running on each port
- OS fingerprinting — guessing the OS from responses

> ⚠️ Only scan networks and systems you own.

**Tools:** `nmap`

</details>

---

**📝 Volume 03 Interview Prep — Write answers in `interview-prep/Vol3_QA.md`**

1. Walk through exactly what happens when you type google.com in a browser and press Enter. Cover DNS, TCP, and HTTP.
2. What is the difference between TCP and UDP? Give a security scenario where each matters.
3. What is ARP spoofing? How would you detect it?
4. A firewall blocks port 443 outbound. What service is affected and why would a security team do this?
5. What is the difference between IDS and IPS? Where would you place each in a network?

**✅ Volume 03 Complete When:**
- All 18 topics done — notes + labs + screenshots committed
- Wireshark: captured your own HTTP and DNS traffic — identified 3-way handshake in a PCAP
- nmap: scanned your own VM — documented findings
- 20 ports memorized — tested without looking
- Interview prep answers written for all 5 questions
- 1 Hashnode blog published for this volume

</details>

---

<details>
<summary><b>Volume 04 — Python for Security</b></summary>

<br>

> Automate everything. Parse logs. Build tools. Code is your force multiplier.
> Python is not optional in 2026 cloud security.

---

<details>
<summary>Topic 1 — Programming Basics</summary>

<br>

- What is a program
- Source code vs binary
- Compiled languages vs Interpreted languages
- Why Python is useful in security work

**Tools:** Python 3, VS Code

</details>

<details>
<summary>Topic 2 — Variables & Data Types</summary>

<br>

- Integer, Float, String, Boolean
- `type()` — check the type of a variable
- Type conversion — `int()`, `str()`, `float()`

**Tools:** Python interpreter

</details>

<details>
<summary>Topic 3 — Input & Output</summary>

<br>

- `input()` — get data from the user
- `print()` — show output
- f-strings — `f"Hello {name}"`
- `format()` method

**Tools:** Python

</details>

<details>
<summary>Topic 4 — Operators</summary>

<br>

- Arithmetic — `+`, `-`, `*`, `/`, `//`, `%`, `**`
- Comparison — `==`, `!=`, `>`, `<`, `>=`, `<=`
- Logical — `and`, `or`, `not`
- Bitwise — `&`, `|`, `^`, `~`, `<<`, `>>` (important for networking)
- Assignment — `=`, `+=`, `-=`

**Tools:** Python

</details>

<details>
<summary>Topic 5 — Conditional Statements</summary>

<br>

- `if` statement
- `elif`
- `else`
- Nested conditions
- Ternary — `x if condition else y`

**Tools:** Python

</details>

<details>
<summary>Topic 6 — Loops</summary>

<br>

- `for` loop — iterate over a sequence
- `while` loop — repeat while condition is true
- `break` — exit the loop early
- `continue` — skip current iteration
- `range()` — generate a number sequence
- `enumerate()` — get index and value together

**Tools:** Python

</details>

<details>
<summary>Topic 7 — Functions</summary>

<br>

- `def` — define a function
- Parameters vs arguments
- `*args` — variable number of positional arguments
- `**kwargs` — keyword arguments
- `return` — send a value back
- Scope — local vs global variables

**Tools:** Python

</details>

<details>
<summary>Topic 8 — Data Structures</summary>

<br>

- List — ordered, changeable, allows duplicates
- Tuple — ordered, unchangeable
- Set — unordered, no duplicates
- Dictionary — key-value pairs
- When to use each

**Tools:** Python

</details>

<details>
<summary>Topic 9 — File Handling</summary>

<br>

- `open()` — open a file
- Modes — read `r`, write `w`, append `a`
- `with` statement — automatically closes the file
- Reading line by line
- Reading and writing CSV files

**Tools:** Python

</details>

<details>
<summary>Topic 10 — Error Handling</summary>

<br>

- `try` — code that might fail
- `except` — what to do if it fails
- `finally` — always runs no matter what
- `raise` — throw an error manually
- Custom exception classes

**Tools:** Python

</details>

<details>
<summary>Topic 11 — Modules & Packages</summary>

<br>

- `import` — use a module
- `from x import y`
- `pip` — install third-party packages
- `venv` — isolated environment per project
- `requirements.txt` — list of all dependencies

**Tools:** `pip`, `venv`

</details>

<details>
<summary>Topic 12 — Object Oriented Programming</summary>

<br>

- Class — blueprint for an object
- Object — instance of a class
- `__init__` — constructor method
- Inheritance — child class gets parent class methods
- Encapsulation — hiding internal details
- `self` — refers to the current object instance

**Tools:** Python

</details>

<details>
<summary>Topic 13 — Regular Expressions</summary>

<br>

- Pattern matching — find text that matches a pattern
- `re.search()` — find first match
- `re.findall()` — find all matches
- `re.sub()` — replace matches
- Groups — capture parts of a match
- Why this is critical for log parsing in security

**Tools:** `re` module

</details>

<details>
<summary>Topic 14 — Working with APIs</summary>

<br>

- HTTP methods — GET, POST, PUT, DELETE
- REST APIs — what they are
- JSON — the format APIs return
- Parsing JSON in Python
- HTTP status codes — 200, 401, 403, 404, 500

**Tools:** `requests` library

</details>

<details>
<summary>Topic 15 — Automation Scripting</summary>

<br>

- Automating repetitive tasks
- File operations at scale
- Log parsing scripts
- `argparse` — accept command-line arguments in your scripts

**Tools:** Python, `argparse`

</details>

<details>
<summary>Topic 16 — Security Libraries</summary>

<br>

- `hashlib` — MD5, SHA1, SHA256 hashing
- `cryptography` — encrypt and decrypt data
- `secrets` — cryptographically secure random tokens
- `base64` — encode and decode

**Tools:** Python

</details>

<details>
<summary>Topic 17 — AWS SDK boto3 Intro</summary>

<br>

- What boto3 is
- How to authenticate to AWS from Python
- List S3 buckets
- List EC2 instances
- Basic error handling

**Tools:** `boto3`

</details>

<details>
<summary>Topic 18 — Azure SDK Intro</summary>

<br>

- What `azure-identity` is
- Basic resource listing (awareness level only)

**Tools:** `azure-identity`

</details>

---

**🔨 Mandatory Mini Project — Build Before Moving to Volume 05**

```
Log Analyzer — Failed SSH Login Detector

What it does:
  - Reads /var/log/auth.log
  - Counts failed login attempts per IP address
  - Flags any IP with more than 5 failures
  - Output: timestamp, IP, count, status (ALERT or OK)
  - Saves report to a file

Why it matters:
  This is a real SOC task. Recruiters will ask you to explain
  every line of this script. This is your first security tool.

GitHub: Push to a separate repo named security-scripts
        Write a full README explaining what it does and how to run it.
```

**📝 Volume 04 Interview Prep — Write answers in `interview-prep/Vol4_QA.md`**

1. Write a Python function that takes a list of numbers and returns only the even ones. Explain your logic.
2. What is the difference between a list and a tuple? When would you use each?
3. What does a regular expression for matching an IP address look like? Explain each part.
4. How would you use Python to read a log file and extract all lines containing the word ERROR?
5. What is boto3? Write the Python code to list all S3 buckets in an AWS account.

**✅ Volume 04 Complete When:**
- All 18 topics done — notes + scripts + screenshots committed
- Log Analyzer project built and pushed to GitHub with full README
- Interview prep answers written for all 5 questions
- 1 Hashnode blog published for this volume

</details>

---

<details>
<summary><b>Volume 05 — Security Foundations</b></summary>

<br>

> The WHY behind everything. Never memorize without understanding the attack.
>
> ✅ Start applying for internships after this volume is complete.

---

<details>
<summary>Topic 1 — Information Security Principles</summary>

<br>

- CIA Triad in depth:
  - Confidentiality — only authorized people can see data
  - Integrity — data has not been tampered with
  - Availability — systems are up when needed
- Defense in Depth — multiple layers, assume breach at every level
- Least Privilege — minimum access needed, nothing more
- Zero Trust — never trust, always verify, even inside the network
- Need to Know — access only what your role requires

**Tools:** None — concept only

</details>

<details>
<summary>Topic 2 — Threat Landscape</summary>

<br>

- Malware types in depth
- Ransomware — how it works (Colonial Pipeline 2021 example)
- Phishing — types: spear phishing, whaling, smishing
- DoS vs DDoS — difference
- Insider Threat — trusted user causes damage (Tesla 2018 example)
- APT — Advanced Persistent Threat — long-term targeted attack
- Supply Chain attack — SolarWinds 2020 example

**Tools:** VirusTotal (look up file hashes)

</details>

<details>
<summary>Topic 3 — Vulnerability Concepts</summary>

<br>

- Vulnerability — a weakness in a system
- Exploit — code or technique that uses the weakness
- Patch — the fix released by the vendor
- CVE format — CVE-YEAR-NUMBER
- CVSS score — 0 to 10, how severity is measured
- NVD database — where every CVE is documented

**Tools:** nvd.nist.gov, cve.mitre.org

</details>

<details>
<summary>Topic 4 — Attack Surface</summary>

<br>

- Network attack surface — open ports, exposed services
- Application attack surface — web apps, APIs, login pages
- Human attack surface — phishing, social engineering
- How to map your attack surface
- How to reduce your attack surface

**Tools:** `nmap` (your own VM only)

</details>

<details>
<summary>Topic 5 — Authentication & Authorization (Deep Dive)</summary>

<br>

- Password attacks — brute force, credential stuffing, password spray
- MFA types — TOTP app, push notification, hardware key (YubiKey)
- Tokens — JWT structure, OAuth tokens
- OAuth 2.0 — how it works step by step
- SAML — enterprise single sign-on
- SSO — single sign-on concept

**Tools:** Google Authenticator

</details>

<details>
<summary>Topic 6 — Cryptography Basics</summary>

<br>

- Symmetric encryption — same key for encrypt and decrypt (AES)
- Asymmetric encryption — public key to encrypt, private key to decrypt (RSA)
- Hashing — one-way, cannot be reversed (SHA256, bcrypt)
- Digital signatures — prove the message came from who it says
- PKI — Public Key Infrastructure — how certificates work
- TLS handshake — step by step what happens when you load HTTPS

**Tools:** `openssl`

</details>

<details>
<summary>Topic 7 — Web Security Basics (OWASP Top 10)</summary>

<br>

OWASP Top 10 — 2021 edition. Learn each with a real attack example:

1. Broken Access Control
2. Cryptographic Failures
3. Injection — SQL injection, command injection
4. Insecure Design
5. Security Misconfiguration
6. Vulnerable and Outdated Components (Equifax 2017 — Apache Struts)
7. Identification and Authentication Failures
8. Software and Data Integrity Failures
9. Security Logging and Monitoring Failures
10. Server-Side Request Forgery (SSRF)

Also cover: Cookies, Sessions, JWT tokens

**Tools:** Burp Suite Community (against OWASP Juice Shop — local only, never public)

</details>

<details>
<summary>Topic 8 — Network Security Basics</summary>

<br>

- Firewalls in depth — packet filtering, stateful, next-generation
- IDS rules — signatures and anomaly detection
- IPS inline blocking — drops the packet, not just alerts
- DMZ architecture — what goes in the DMZ and why

**Tools:** Snort (concept level)

</details>

<details>
<summary>Topic 9 — Endpoint Security</summary>

<br>

- Antivirus — signature-based, compares against known malware database
- EDR — Endpoint Detection and Response, behavioral detection
- Difference between AV and EDR
- How AV evasion works (concept — important for understanding detection)

**Tools:** ClamAV (scan your own system)

</details>

<details>
<summary>Topic 10 — Identity & Access Management</summary>

<br>

- Users, Roles, Policies, Groups
- Least Privilege in practice
- PAM — Privileged Access Management
- Service accounts — what they are and why they are high-risk

**Tools:** Keycloak (local install, concept level)

</details>

<details>
<summary>Topic 11 — Logging & Monitoring</summary>

<br>

- Log sources — OS logs, application logs, network logs, cloud logs
- Centralized logging — why sending logs to one place matters
- Log retention — how long to keep logs and why
- What to log — events that matter for security
- SIEM concept intro — what it is and what problem it solves

**Tools:** ELK Stack (intro level)

</details>

<details>
<summary>Topic 12 — Incident Response Basics</summary>

<br>

- PICERL framework:
  - **P**reparation — before the incident
  - **I**dentification — detecting the incident
  - **C**ontainment — limiting the damage
  - **E**radication — removing the threat
  - **R**ecovery — restoring normal operations
  - **L**essons Learned — improving for next time
- Incident report structure — what a real report includes

**Tools:** TheHive (concept level)

</details>

<details>
<summary>Topic 13 — Security Policies</summary>

<br>

- Password policy — complexity, minimum length, rotation
- Access control policy — who gets what access
- Acceptable use policy — what employees are allowed to do
- Data classification — Public, Internal, Confidential, Restricted

**Tools:** None — concept only

</details>

<details>
<summary>Topic 14 — Compliance Intro</summary>

<br>

- NIST Cybersecurity Framework — 5 functions: Identify, Protect, Detect, Respond, Recover
- ISO 27001 — international security management standard overview
- India DPDP Act — Digital Personal Data Protection Act
- RBI Cloud Security Guidelines — for Indian banking and fintech context

**Tools:** Read official documentation for each

</details>

---

**📝 Volume 05 Interview Prep — Write answers in `interview-prep/Vol5_QA.md`**

1. What is the difference between symmetric and asymmetric encryption? Give a real-world use case for each.
2. Explain the OWASP Top 10. Pick any 3 and explain how an attacker would exploit them.
3. What is a CVE? What does CVSS score 9.8 tell you? What would you do about it?
4. Explain the CIA Triad using a real-world bank as the example for each property.
5. What is Zero Trust? Why is "trust but verify" no longer sufficient in cloud environments?

**✅ Volume 05 Complete When:**
- All 14 topics done — notes + labs + screenshots committed
- Real breach mapped for every major topic — 3 sentences each
- OWASP Juice Shop deployed locally — 3 vulnerabilities found and documented
- Interview prep answers written for all 5 questions
- 1 Hashnode blog published for this volume
- ✅ Start applying for internships now

</details>

---

<details>
<summary><b>Volume 06 — Blue Team Core</b></summary>

<br>

> SOC · Detection · Incident Response
> This is where most fresher cloud security jobs in India begin.
>
> ✅ Actively apply for SOC and Blue Team roles after this volume.

---

<details>
<summary>Topic 1 — Security Operations Center</summary>

<br>

- SOC purpose — monitor, detect, respond to threats 24/7
- SOC structure:
  - Tier 1 — alert triage and first response
  - Tier 2 — deeper investigation
  - Tier 3 — threat hunting and advanced analysis
- Alert lifecycle — generated → triaged → investigated → escalated or closed
- What a day in a SOC analyst's job looks like

**Tools:** None — concept only

</details>

<details>
<summary>Topic 2 — Log Sources</summary>

<br>

- Windows Security logs — Event IDs and what they mean
- Linux auth.log and syslog
- Firewall logs — allowed and blocked traffic
- Proxy logs — web browsing events
- Cloud logs — CloudTrail (AWS), Azure Activity Log
- What a raw log entry looks like for each source

**Tools:** Event Viewer, `journalctl`

</details>

<details>
<summary>Topic 3 — SIEM Concepts</summary>

<br>

- Log ingestion — how logs arrive at the SIEM
- Parsing — extracting fields from raw log text
- Normalization — making logs from different systems consistent
- Correlation rules — combining multiple events to detect an attack
- Alerting — when and how alerts fire
- Dashboards — visualizing security posture

**Tools:** Wazuh (install and configure), Splunk Trial, ELK

</details>

<details>
<summary>Topic 4 — Detection Engineering</summary>

<br>

- Detection rules — what logic triggers an alert
- Use cases — what specific attack are you detecting
- Alert tuning — reducing noise, eliminating false positives
- False positive vs True positive
- Sigma rules — vendor-neutral detection format, write 3 rules yourself

**Tools:** Sigma, Splunk SPL (basics)

</details>

<details>
<summary>Topic 5 — Threat Intelligence</summary>

<br>

- IOC types — IP address, domain, file hash, URL
- TTP — Tactics, Techniques, Procedures
- Cyber Kill Chain — 7 stages every attack follows
- Diamond Model — attacker, capability, infrastructure, victim
- MITRE ATT&CK framework — visit attack.mitre.org and spend 1 hour exploring

**Tools:** MISP, VirusTotal API

</details>

<details>
<summary>Topic 6 — Malware Analysis Basics</summary>

<br>

- Static analysis — examine the file without running it:
  - File hashes — MD5, SHA256
  - Strings — readable text inside a binary
  - PE headers — structure of a Windows executable
- Dynamic analysis — run in a sandbox and watch what it does
- Sandbox services — AnyRun, Any.run

**Tools:** PEStudio, AnyRun

</details>

<details>
<summary>Topic 7 — Endpoint Detection & Response</summary>

<br>

- Endpoint telemetry — process, network, and file events from every machine
- Behavioral detection — detecting attack patterns, not just signatures
- Process monitoring — watching what processes launch and what they do
- File integrity monitoring — alerts when important files change
- EDR platforms (awareness) — CrowdStrike, SentinelOne

**Tools:** OSQuery

</details>

<details>
<summary>Topic 8 — Network Traffic Analysis</summary>

<br>

- PCAP analysis — reading raw packet captures
- Protocol decoding — understanding what each packet contains
- C2 traffic patterns — what command and control traffic looks like
- Beaconing detection — regular intervals of outbound calls to an attacker

**Tools:** Wireshark, `tcpdump`

</details>

<details>
<summary>Topic 9 — Alert Triage</summary>

<br>

- Alert severity levels — Critical, High, Medium, Low
- Prioritization — what to investigate first
- Incident categorization — what type of incident is this
- Ticket workflow — how SOC teams track and document work

**Tools:** Wazuh Alerts

</details>

<details>
<summary>Topic 10 — Incident Response (Hands-On)</summary>

<br>

- Host isolation — removing a machine from the network
- Evidence collection — what to collect before touching anything
- Chain of custody — keeping evidence legally valid
- Forensic basics — disk imaging, memory capture
- Writing a real incident report — what sections to include

**Tools:** Velociraptor, TheHive

</details>

<details>
<summary>Topic 11 — Digital Forensics Basics</summary>

<br>

- Disk imaging — creating an exact copy of a drive
- File system analysis — finding deleted files and timestamps
- Timeline analysis — chronological view of what happened
- Memory forensics intro — what you can find in RAM

**Tools:** Autopsy, FTK Imager

</details>

<details>
<summary>Topic 12 — Threat Hunting</summary>

<br>

- Hypothesis-driven hunting — start with a theory, look for evidence
- Baseline vs anomaly — what normal looks like, what stands out
- MITRE ATT&CK mapping — connecting what you find to known techniques
- Threat hunt report — how to document and present your findings

**Tools:** ELK Stack, Splunk

</details>

---

**📝 Volume 06 Interview Prep — Write answers in `interview-prep/Vol6_QA.md`**

1. What is the difference between a SIEM and a SOC? Can you have one without the other?
2. Walk me through how you would triage a phishing alert in a SOC. Step by step.
3. What is a Sigma rule? Write a simple one for detecting multiple failed SSH logins.
4. What is the Cyber Kill Chain? Map a ransomware attack to each stage.
5. You see svchost.exe making outbound connections to an IP in Russia. What do you do?

**✅ Volume 06 Complete When:**
- All 12 topics done — notes + labs + screenshots committed
- Wazuh installed — 3 custom rules written — alerts generated and screenshot
- MITRE ATT&CK: 5 techniques mapped to Linux commands in a cheatsheet on GitHub
- Interview prep answers written for all 5 questions
- 1 Hashnode blog published for this volume
- ✅ Actively applying for SOC Analyst fresher roles now

</details>

---

<details>
<summary><b>Volume 07 — Cloud Security Core</b></summary>

<br>

> AWS first. Then Azure. Then GCP. This is your specialization.
>
> ✅ Aggressively apply for cloud security roles after this volume.

> ⚠️ AWS Free Tier account must be active before starting.
> Create at aws.amazon.com/free — set a billing alert at $5 immediately.

---

<details>
<summary>Topic 1 — Cloud Computing Basics</summary>

<br>

- IaaS — you manage the OS and everything above
- PaaS — you manage the application and data only
- SaaS — provider manages everything, you just use the software
- Shared Responsibility Model — exact boundary between provider and customer responsibility

**Tools:** AWS Console

</details>

<details>
<summary>Topic 2 — Cloud IAM</summary>

<br>

- Users — individual identities
- Groups — collection of users with shared permissions
- Roles — temporary permissions assumed by users or services
- Policies — JSON documents defining what is allowed or denied
- Least Privilege in IAM — only what is needed, nothing more
- MFA on root account — mandatory, no exceptions
- Service accounts — IAM roles for applications
- Assume Role — how one service takes on another's permissions

**Tools:** AWS IAM, Azure Entra ID (awareness), GCP IAM (awareness)

</details>

<details>
<summary>Topic 3 — Cloud Networking</summary>

<br>

- VPC — Virtual Private Cloud, your isolated network in AWS
- Public subnet — resources here can reach the internet
- Private subnet — resources here cannot be reached from the internet
- Route Tables — how traffic is directed
- Internet Gateway — allows internet access from public subnet
- NAT Gateway — allows outbound internet from private subnet without exposing it
- Security Groups — stateful firewall at the instance level
- NACLs — stateless firewall at the subnet level

**Tools:** AWS VPC

</details>

<details>
<summary>Topic 4 — Cloud Compute</summary>

<br>

- EC2 instance types — compute, memory, storage optimized
- Key pairs — how SSH access to EC2 works
- Instance Metadata Service (IMDS) — what it is and why it is a risk
- IMDSv1 vs IMDSv2 — why v2 is required (prevents SSRF attacks)
- EC2 hardening — disable root login, disable password auth, key-only access

**Tools:** EC2, Docker (intro)

</details>

<details>
<summary>Topic 5 — Cloud Storage Security</summary>

<br>

- S3 buckets — object storage
- Bucket policies — JSON access control
- Block Public Access — the setting that prevents accidental public exposure
- ACLs vs Bucket Policies — when to use each
- Encryption at rest — SSE-S3, SSE-KMS
- Encryption in transit — enforce HTTPS only

**Tools:** Amazon S3

</details>

<details>
<summary>Topic 6 — Cloud Logging & Monitoring</summary>

<br>

- CloudTrail — records every API call in your AWS account
- CloudWatch — metrics, logs, and alarms
- VPC Flow Logs — network traffic in and out of your VPC
- S3 access logs — who accessed what object and when
- How to read a CloudTrail event in JSON format

**Tools:** CloudTrail, CloudWatch

</details>

<details>
<summary>Topic 7 — Container Security</summary>

<br>

- Container image scanning — find vulnerabilities before deployment
- Dockerfile best practices — do not run as root, use minimal base image
- Runtime risks — what can go wrong when a container is running
- Secrets in containers — never hardcode credentials in images

**Tools:** Trivy, Docker Scout

</details>

<details>
<summary>Topic 8 — CI/CD Security</summary>

<br>

- Secrets management in pipelines — never store secrets in code
- SAST in CI — scan code for vulnerabilities on every push
- Dependency scanning — check libraries for known CVEs
- Branch protection — require review before merging to main

**Tools:** GitHub Actions, GitLab CI (awareness)

</details>

<details>
<summary>Topic 9 — Infrastructure as Code Security</summary>

<br>

- Terraform basics — what it is and how it works
- Secure Terraform templates — what mistakes to avoid
- State file — never commit it to Git
- Drift detection — when real infrastructure differs from your code
- Checkov — automated IaC security scanner

**Tools:** Terraform, Checkov

</details>

<details>
<summary>Topic 10 — Cloud Attack Techniques</summary>

<br>

- Credential theft — stealing IAM access keys
- SSRF to metadata service — how attackers get EC2 IAM credentials
- Privilege escalation in IAM — how attackers gain more permissions
- Misconfiguration abuse — public S3 buckets, open security groups
- Lateral movement — moving between accounts and services

**Tools:** Prowler, ScoutSuite

</details>

---

**🔨 Mandatory AWS Free Tier Labs — Complete All 6**

1. Create IAM user with least privilege — S3 read to one bucket only. Verify it cannot do anything else.
2. Create S3 bucket → make it public → verify the risk → block all public access → verify private. Screenshot both states.
3. Enable CloudTrail → perform 10 actions in the console → read the JSON logs and identify each action.
4. Launch EC2 → SSH in → harden (disable root login, disable password auth, key only) → terminate.
5. Run Prowler: `pip install prowler` then `prowler aws` — read and understand the findings report.
6. Break an IAM policy (give admin) → run Prowler → see the finding → fix it → run again → confirm resolved.

**⭐ Signature Project — Separate GitHub Repo: `aws-security-scanner`**

```
Python script using boto3 that scans for:
  - Public S3 buckets
  - Overly permissive IAM policies
  - EC2 instances with port 22 open to 0.0.0.0/0
  - CloudTrail disabled in any region
  - MFA not enabled on root account

Output: JSON + HTML report with findings, severity, and remediation steps

This is what Prowler does commercially.
Every ₹20+ LPA interviewer will ask you to walk through this.
```

**📝 Volume 07 Interview Prep — Write answers in `interview-prep/Vol7_QA.md`**

1. What is the AWS Shared Responsibility Model? Who is responsible for what in IaaS vs SaaS?
2. An S3 bucket was made public accidentally. How do you detect it, contain it, and prevent it in future?
3. What is SSRF? How does it apply to the AWS EC2 metadata service (IMDSv1 vs IMDSv2)?
4. Write an IAM policy in JSON that allows read-only access to one specific S3 bucket.
5. What does CloudTrail capture? What does CloudWatch capture? Are they the same?

**✅ Volume 07 Complete When:**
- All 10 topics done — notes + labs + screenshots committed
- All 6 AWS Free Tier labs completed
- Signature project pushed to GitHub with full README
- Interview prep answers written for all 5 questions
- 1 Hashnode blog published for this volume
- ✅ Aggressively applying for cloud security roles now

</details>

---

<details>
<summary><b>Volume 08 — Automation & DevSecOps</b></summary>

<br>

> Engineers who automate get hired. Engineers who only click get filtered.

---

<details>
<summary>Topic 1 — Scripting Automation</summary>

<br>

- Automating repetitive security tasks with Python
- Log parsing pipeline
- File processing at scale
- Sending email alerts from scripts

**Tools:** Python

</details>

<details>
<summary>Topic 2 — Version Control Advanced</summary>

<br>

- Git branching — main, dev, feature branches
- Pull requests and code review
- `.gitignore` — what to never commit
- `git-secrets` — prevent committing credentials

**Tools:** Git, GitHub, `git-secrets`

</details>

<details>
<summary>Topic 3 — Data Formats</summary>

<br>

- JSON — structure, nested objects, arrays
- YAML — used in Ansible, Kubernetes, GitHub Actions
- XML — older format, still common in enterprise
- `jq` — parse and filter JSON from command line

**Tools:** `jq`, Python `json` module

</details>

<details>
<summary>Topic 4 — API Security Basics</summary>

<br>

- Authentication methods — API key, Bearer token, OAuth
- Rate limiting and why it matters
- API key rotation
- Testing APIs with Postman

**Tools:** Postman

</details>

<details>
<summary>Topic 5 — Building Security Tools</summary>

<br>

- CLI tools with `argparse`
- Log analysis tools
- REST API security checker

**Tools:** Python, FastAPI

</details>

<details>
<summary>Topic 6 — Automation Frameworks</summary>

<br>

- Ansible — what it does and why it matters
- Playbook structure — hosts, tasks, variables
- Idempotency — running the same playbook twice gives the same result
- Write a security hardening playbook for Linux

**Tools:** Ansible

</details>

<details>
<summary>Topic 7 — Task Scheduling</summary>

<br>

- Cron syntax — `minute hour day month weekday`
- Schedule security scans to run automatically
- Alert via email or Slack when a scan finds something

**Tools:** `crontab`

</details>

<details>
<summary>Topic 8 — Documentation</summary>

<br>

- Markdown advanced — tables, code blocks, badges, collapsible sections
- README best practices
- Architecture diagrams — draw your project architecture
- Runbook writing — step-by-step procedures for handling incidents

**Tools:** Markdown, draw.io

</details>

<details>
<summary>Topic 9 — Secrets Management</summary>

<br>

- Never hardcode credentials in code
- Environment variables — basic approach
- AWS Secrets Manager — the right approach in cloud
- HashiCorp Vault — enterprise-grade secrets management
- `.env` files — local development only, never commit

**Tools:** AWS Secrets Manager, `python-dotenv`

</details>

<details>
<summary>Topic 10 — IaC Security</summary>

<br>

- Terraform security — common misconfigurations
- Checkov — scan Terraform files for security issues
- `tfsec` — another IaC security scanner
- Drift detection automation

**Tools:** Checkov, `tfsec`

</details>

<details>
<summary>Topic 11 — CI/CD Security Pipeline</summary>

<br>

- GitHub Actions workflow structure
- Secrets scanning in the pipeline
- Container image scanning with Trivy in CI
- SAST tools — Semgrep, Bandit (Python)
- Fail the pipeline if critical findings are found

**Tools:** GitHub Actions, Trivy

</details>

---

**🔨 Mandatory Mini Project — Add to `aws-security-scanner` Repo**

```
Automated Security Scan Pipeline via GitHub Actions

Runs on every code push:
  - git-secrets (scan for hardcoded credentials)
  - Checkov (IaC scan if Terraform files present)
  - Trivy (container scan if Dockerfile present)
  - Generates security report as GitHub Actions artifact
  - Fails the pipeline if critical findings are found

Show this running live in an interview.
```

**📝 Volume 08 Interview Prep — Write answers in `interview-prep/Vol8_QA.md`**

1. What is DevSecOps? How is it different from traditional security?
2. A developer accidentally commits an AWS access key to GitHub. What do you do immediately? Then what?
3. What is Checkov? Run it and explain one finding you got from your Terraform code.
4. Write a cron job that runs a Python script every day at 2 AM.
5. What is the difference between SAST and DAST? Give a tool example for each.

**✅ Volume 08 Complete When:**
- All 11 topics done — notes + scripts + screenshots committed
- Automated pipeline running in GitHub Actions on your aws-security-scanner repo
- Interview prep answers written for all 5 questions
- 1 Hashnode blog published for this volume

</details>

---

<details>
<summary><b>Volume 09 — Projects & Portfolio</b></summary>

<br>

> Your GitHub is your resume. Every project must be interview-ready.

---

| # | Project | What You Build | Tools |
|---|---------|---------------|-------|
| 1 | Linux Security Hardening | Hardened Linux server from scratch — users, permissions, SSH keys, ufw, Fail2Ban, Lynis report | Linux, ufw, Fail2Ban, Lynis |
| 2 | Network Security Scanner | Python script: discover hosts, scan ports, flag risky open ports, generate report | Python, nmap, python-nmap |
| 3 | Log Analyzer (Enhanced) | Parse auth.log + syslog, detect brute force and privilege escalation, CLI tool with `--report` flag | Python, argparse, regex, pandas |
| 4 | Web Security Lab | Deploy OWASP Juice Shop locally, find 5 vulnerabilities, show attack and fix for each, write pentest report | Docker, Juice Shop, Burp Suite |
| 5 | SOC Home Lab | Full Wazuh SIEM, 2 agents, simulate 3 attacks, write IR report for each | Wazuh, ELK, Metasploit (local only) |
| 6 | AWS Security Scanner ⭐ | Signature project from Volume 07 — fully polished with CI/CD pipeline added | boto3, Python, GitHub Actions, Prowler |
| 7 | Multi-Cloud Comparison | Deploy same workload on AWS + Azure, apply IAM least privilege on both, run security scan on both, write comparison report | AWS, Azure, Checkov, Prowler |
| 8 | Cloud Threat Hunt Report | Analyze CloudTrail logs from your own account, map 5 API calls to MITRE ATT&CK techniques, write formal threat hunt report | CloudTrail, MITRE ATT&CK, Python |

---

**Every project README must include:**
- What this project does and why it exists
- Architecture diagram
- Tools used
- How to run it
- Sample output or screenshots

**Rules:**
- Quality over quantity — 3 excellent projects beat 10 mediocre ones
- Pin your top 3 repos on your GitHub profile
- Write a Hashnode blog post for each major project

</details>

---

<details>
<summary><b>Volume 10 — Career & Interview Preparation</b></summary>

<br>

> The technical work is done. Now convert it to a job offer.

---

<details>
<summary>Resume Rules</summary>

<br>

- Maximum 1 page — no exceptions for freshers
- Top: Name, LinkedIn URL, GitHub URL, Hashnode URL, email, phone
- Skills: only what you can answer 3 interview questions about — nothing more
- Projects: 3 maximum — 2 bullet points each (what it does + what security problem it solves)
- Education: degree, year, CGPA if above 7.5
- Certifications: list only completed ones — never "in progress"
- No objective statement — leads with skills and projects instead
- Template: Canva Simple Resume — clean, ATS-friendly

</details>

<details>
<summary>LinkedIn Optimization</summary>

<br>

| Section | What to Write |
|---------|--------------|
| Headline | Cloud Security Engineer \| AWS \| Linux \| Blue Team \| B.Tech 2026 |
| About | 3 paragraphs: who you are + what you are building + what you are looking for. 150 words max. |
| Featured | Pin: GitHub repo + Hashnode blog + your best LinkedIn post |
| Skills | Cloud Security, AWS, Linux, Python, Networking, SIEM, Incident Response |
| Posts | Every 2 days during active learning. 150 words. Your own words. |

**Connection strategy:** 5 cloud security professionals per week. Comment on their posts before connecting.

</details>

<details>
<summary>When to Apply — Exact Trigger Points</summary>

<br>

| After Volume | What to Apply For |
|-------------|-------------------|
| Volume 05 complete | Cybersecurity internships, SOC internships at small and mid companies |
| Volume 06 complete | SOC Analyst fresher, Blue Team intern, MSSP companies |
| Volume 07 complete | Cloud Security intern, AWS Security Engineer fresher |
| Volume 09 complete | Detection Engineer junior, Cloud Security Engineer, Security Automation Engineer |
| Specialization done | Product companies, MNCs, highest package roles |

**Where to Apply:**
- LinkedIn Jobs — set alerts for: cloud security intern, SOC analyst fresher, security analyst trainee
- Naukri.com — enable Actively Looking flag
- Instahyre — better for product companies and startups
- Company career pages directly — Paytm, Razorpay, Wipro, TCS, Infosys, Swiggy, Flipkart
- MSSP companies — Tata Communications, Secureworks India, IBM Security, Wipro CyberDefense

</details>

<details>
<summary>Top 20 Interview Questions</summary>

<br>

| # | Question | Volume |
|---|----------|--------|
| 1 | CIA Triad — give an attack against each property | Vol 1 |
| 2 | Difference between vulnerability and risk | Vol 1 |
| 3 | What happens when you run sudo on Linux | Vol 2 |
| 4 | Windows Event IDs 4624 and 4625 — what do they mean | Vol 2 |
| 5 | Walk through a TCP 3-way handshake | Vol 3 |
| 6 | Calculate subnet mask for /24 and /28 | Vol 3 |
| 7 | Python — read a file and count lines containing ERROR | Vol 4 |
| 8 | Hashing — why not just encrypt passwords | Vol 5 |
| 9 | OWASP Top 10 — pick 3 and explain the attack | Vol 5 |
| 10 | What is a SIEM — what does Wazuh do | Vol 6 |
| 11 | MITRE ATT&CK — give an example technique | Vol 6 |
| 12 | 1000 failed SSH logins in 5 minutes — what do you do | Vol 6 |
| 13 | AWS Shared Responsibility Model | Vol 7 |
| 14 | Write an IAM policy JSON for S3 read-only | Vol 7 |
| 15 | What does CloudTrail log | Vol 7 |
| 16 | S3 bucket made public — your response | Vol 7 |
| 17 | SSRF and the AWS metadata service attack | Vol 7 |
| 18 | DevSecOps — what does shift left mean | Vol 8 |
| 19 | Developer commits AWS key to GitHub — immediate steps | Vol 8 |
| 20 | Walk through your AWS Security Scanner project | Vol 9 |

</details>

<details>
<summary>Certifications — Realistic Timeline</summary>

<br>

| Certification | When to Take | Cost | Priority |
|---------------|-------------|------|----------|
| Google Cybersecurity Certificate | After Vol 5 | Free (audit) | Optional |
| CompTIA Security+ | After Vol 6 | ₹25,000 | HIGH |
| AWS Cloud Practitioner | After Vol 7 | ₹8,000 | HIGH |
| CompTIA CySA+ | 6 months after Security+ | ₹25,000 | MEDIUM |
| AWS Security Specialty | 12–18 months hands-on | ₹25,000 | HIGH |
| Azure AZ-500 | After AWS Security Specialty | ₹15,000 | MEDIUM |

</details>

</details>

---

<details>
<summary><b>Volume 11 — Specialization Track (Choose ONE after Volume 10)</b></summary>

<br>

> Complete Volumes 1–10 before starting this.
> Choose ONE path only. Depth beats breadth. The market pays highest for specialists.

---

<details>
<summary>Option A — Cloud Security Architecture · ₹18–40 LPA</summary>

<br>

- AWS Security Hub — centralized security findings management
- AWS Config — compliance as code, Config Rules, auto-remediation
- AWS GuardDuty — threat detection, Lambda auto-response
- AWS Security Lake — centralized cloud security data lake
- Zero Trust Architecture on AWS
- CSPM — Prisma Cloud, Wiz (concepts)

**Signature Project:** Full AWS security architecture with auto-remediation using Lambda + GuardDuty + Config

**Target Roles:** Cloud Security Architect, AWS Security Engineer

</details>

<details>
<summary>Option B — Detection Engineering · ₹15–35 LPA</summary>

<br>

- Sigma rule writing — advanced detection logic
- YARA rules — malware signature writing
- Splunk SPL — deep query language for detection
- Elastic SIEM — detection rules in Kibana
- Custom threat intelligence feeds
- Purple team exercises — run the attack and build the detection simultaneously

**Signature Project:** Detection rule library for 10 MITRE ATT&CK techniques with test cases

**Target Roles:** Detection Engineer, Threat Hunter

</details>

<details>
<summary>Option C — Cloud Penetration Testing (Defensive Focus) · ₹20–45 LPA</summary>

<br>

- AWS enumeration techniques — what attackers look for
- Pacu — AWS exploitation framework (defensive study only)
- CloudGoat — intentionally vulnerable AWS lab by Rhino Security Labs
- Azure attack paths — BloodHound for Azure
- Container escape techniques and defenses

**Signature Project:** Full cloud pentest report on CloudGoat environment

**Target Roles:** Cloud Penetration Tester, Red Team (Cloud), Security Consultant

</details>

</details>

---

## 🔧 Tools Reference

<details>
<summary><b>Security Tools — When and Why</b></summary>

<br>

| Tool | Volume | Purpose |
|------|--------|---------|
| htop | Vol 1–2 | Real-time process monitoring |
| Wireshark | Vol 3, 6 | Packet capture and analysis |
| nmap | Vol 3, 9 | Network scanning — your own systems only |
| Wazuh | Vol 6 | Open-source SIEM — primary lab tool |
| Lynis | Vol 2 | Linux security audit — run monthly |
| Burp Suite Community | Vol 5 | Web testing — OWASP Juice Shop only |
| Prowler | Vol 7 | AWS security scanner |
| Checkov | Vol 8 | IaC security scanner for Terraform |
| Trivy | Vol 7–8 | Container image vulnerability scanner |
| Autopsy | Vol 6 | Digital forensics |
| OSQuery | Vol 6 | Query your endpoint like a database |
| git-secrets | Vol 8 | Prevents committing credentials to Git |
| Sigma | Vol 6 | Write vendor-neutral detection rules |
| OpenSSL | Vol 5 | Cryptography — encryption, hashing, certificates |
| Ansible | Vol 8 | Automation and Linux hardening playbooks |
| strace | Vol 1–2 | System call tracer — see kernel boundary |

</details>

---

## 🔴 Final Rules

<details>
<summary><b>10 Rules That Decide Your Outcome — Read Every Morning</b></summary>

<br>

**RULE 1:** No video lectures. No Udemy. No YouTube tutorials. Only terminal, documentation, and this roadmap.

**RULE 2:** Every day you do not commit to GitHub is a day a recruiter sees an empty contribution graph.

**RULE 3:** English is a technical skill. Write every README, post, and report like a professional.

**RULE 4:** You are not studying security. You are building a product — yourself as a Cloud Security Engineer.

**RULE 5:** The streak is everything. One missed day kills momentum. Two becomes a habit. Three means starting over.

**RULE 6:** Never list a skill you cannot answer three interview questions about. Period.

**RULE 7:** Your GitHub is read by recruiters before your resume. Treat it accordingly.

**RULE 8:** Before asking a question — try for 20 minutes yourself. Then ask with your attempt included.

**RULE 9:** Your only competitor is yesterday's version of you. Ignore everyone else.

**RULE 10:** The highest package goes to the person with the best story — clear journey + signature project + public presence + ability to explain what they know. Build all four simultaneously.

</details>

---

*Every day you execute this roadmap, you get closer.*
*Every day you don't, someone else does.*