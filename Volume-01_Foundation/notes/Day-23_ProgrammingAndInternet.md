# Day 23 — What is Programming + What is the Internet

**Volume:** 01 — Computer and Internet Foundations
**Topics:** 8 and 9

---

## Topic 8 — What is Programming

### What It Is

A program = instructions stored in a file that a computer executes.
Programming = writing those instructions in a language the computer understands.
Security use: automate log parsing, scanning, AWS config checking, anomaly detection.

### Key Terms

**Variable** — named storage location. `count = 0`
**Function** — reusable named block of code. Define once, call many times.
**Loop** — repeat instructions. `for line in file:` reads every line.
**Conditional** — make decisions. `if count > 10: alert()`
**Algorithm** — step-by-step procedure to solve a problem.
**Bug** — code error causing incorrect behavior.
  Security bugs: buffer overflow, integer overflow, null pointer dereference.

### Compiled vs Interpreted

Compiled: source → binary before execution. C, C++, Go, Rust.
Fast. Cannot read source from binary easily. Memory bugs possible.

Interpreted: source read and executed at runtime. Python, JavaScript, Ruby.
Slower. Source is readable. Memory managed automatically.

### High-Level vs Low-Level

Low-level (C, C++): direct memory access, maximum control, dangerous bugs.
Most OS kernels written in C. Most critical CVEs are C memory bugs.
EternalBlue, Heartbleed, Dirty Cow = C memory corruption.

High-level (Python): abstracts memory, safer, easier, slower.
Different bug classes: injection, logic errors, insecure deserialization.

### Why Python for Security

- Installed by default on all Linux systems
- boto3 for AWS, requests for HTTP, scapy for packets, paramiko for SSH
- All major security platforms have Python APIs
- Readable = auditable = trusted by teams
- Fast to write: SSH brute force detector in 30 lines

### Real Breach — Heartbleed CVE-2014-0160

OpenSSL C library. TLS heartbeat feature. Missing bounds check.
Client claimed 64KB payload, sent 1 byte.
Server read 64KB of its own memory — keys, passwords, session tokens.
Fix: 4 lines of C code adding one bounds check.
Impact: half a million HTTPS servers, private keys exposed.
No traces in logs — undetectable exploitation.

---

## Topic 9 — What is the Internet

### What It Is

Global network of networks. No single owner.
Billions of devices connected by agreed protocols.

### Key Terms

**Protocol** — rules for communication. HTTP, TCP, IP, DNS.
**IP address** — unique numerical device address. IPv4: 192.168.1.1
**Packet** — unit of data. Header (src IP, dst IP, port) + payload.
**Router** — forwards packets between networks based on destination IP.
**ISP** — connects you to the internet. Assigns public IP.
**DNS** — translates domain names to IP addresses.
**Client** — requests services. Your browser.
**Server** — provides services. Web server, DNS server.
**Port** — identifies specific service on a device. 0-65535.
  22=SSH, 53=DNS, 80=HTTP, 443=HTTPS, 23=Telnet.

### How Routing Works

Packet → default gateway → ISP router → next hop → destination.
Each router makes independent forwarding decision.
`traceroute [destination]` shows every hop.

### Client-Server Security Perspective

Client attacks: phishing, malicious downloads, MITM, drive-by exploits.
Server attacks: SQL injection, buffer overflow, DDoS, unpatched CVEs.
Protocol attacks: sniffing unencrypted traffic, DNS poisoning, ARP spoofing.

Colonial Pipeline: legitimate credential + legitimate VPN = attack succeeds.
The model worked correctly. The process failed.

### Real Breach — Mirai Botnet 2016

Scanned entire IPv4 internet on port 23 (Telnet).
Tried 61 default credentials on every device found.
Infected 600,000+ IoT devices.
DDoS against Dyn DNS: 1.2 Tbps. Twitter, Netflix, Reddit disrupted.
Lesson: public IP = global exposure. Default credentials = compromised.

### Key Commands
```bash
ip addr show          # your IP addresses
ip route show         # routing table and default gateway
ping -c 4 8.8.8.8     # test connectivity
traceroute google.com # see every router hop
nslookup google.com   # DNS lookup
dig google.com        # detailed DNS query
ss -tlnp              # listening ports and services
curl -v https://example.com  # full HTTP request details
ss -tnp               # active connections
curl ifconfig.me      # your public IP
```

---

## Interview Prep — in interview-prep/Vol1_QA.md

**Topic 8:**
1. Compiler vs interpreter — one language each?
2. What is a function and why does it matter for security tools?
3. Heartbleed — one sentence on the bug and why it was dangerous?
4. Why Python over C for security scripting?
5. Source code vs binary?

**Topic 9:**
6. IP address + port — how do they work together?
7. What is DNS and what happens if it is compromised?
8. Client-server model — one attack on client, one on server?
9. What did Mirai exploit and what does it prove about IoT?
10. Which command shows router hops to a destination?