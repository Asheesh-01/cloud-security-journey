# Day 81 — TCP/IP Model (4 Layers)

**Volume:** 03 — Networking From Zero
**Topic:** 3 — TCP/IP Model
**Relationship to OSI:** Simplified version — 7 OSI layers mapped to 4 TCP/IP layers

---

## 4 Layers (Top to Bottom)

### Layer 4 — Application Layer
**OSI Layers:** 7, 6, 5 (combined)
**What:** User-facing applications, protocols, encryption, compression, session management
**Protocols:** HTTP, HTTPS, FTP, SSH, Telnet, DNS, SMTP, POP3, IMAP, SIP, SNMP
**Examples:** Web browsers, email clients, SSH terminals, DNS queries
**Security Q:** What applications are running? Can I exploit them?

### Layer 3 — Transport Layer
**OSI Layer:** 4 (direct mapping)
**What:** End-to-end delivery using TCP or UDP. Ports and services.
**Protocols:** TCP, UDP, SCTP
**TCP:** Connection-oriented, reliable, ordered, slower (HTTP, SSH, FTP, SMTP)
**UDP:** Connectionless, fast, unreliable (DNS, video, gaming)
**Ports:** 65,535 total. Well-known 0-1023, registered 1024-49151, dynamic 49152-65535
**Security Q:** Is service TCP or UDP? Can I craft packets?

### Layer 2 — Internet Layer
**OSI Layer:** 3 (direct mapping)
**What:** Logical addressing and routing. IP addresses. Determines path to destination.
**Protocols:** IP (IPv4, IPv6), ICMP (ping), IGMP, ARP
**IPv4:** 32-bit (192.168.1.1), current standard
**IPv6:** 128-bit (2001:0db8:85a3::8a2e:0370:7334), future standard
**Routing:** Routing tables determine next hop toward destination
**Security Q:** What IPs on network? Can I route through my machine?

### Layer 1 — Network Interface Layer
**OSI Layers:** 2, 1 (combined)
**What:** Physical connection and hardware addressing. How computer physically attaches to network.
**Protocols:** Ethernet, WiFi (802.11), PPP, ARP
**MAC Address:** 48-bit hardware address (AA:BB:CC:DD:EE:FF). First 24 bits = manufacturer OUI.
**Ethernet:** Wired via cables. Switches manage connections.
**WiFi:** Wireless via radio signals. SSID, WPA2/WPA3.
**ARP:** Maps IP to MAC. "Who has 192.168.1.1?" → "I do, MAC is AA:BB:CC:DD:EE:FF"
**Security Q:** Can I spoof MAC? Can I intercept frames?

---

## OSI to TCP/IP Mapping
OSI (7 layers)           TCP/IP (4 layers)
──────────────           ─────────────────
7 Application      ┐
6 Presentation     ├──→  4 Application
5 Session          ┘
4 Transport        ──→  3 Transport
3 Network          ──→  2 Internet
2 Data Link        ┐
1 Physical         ├──→  1 Network Interface

---

## TCP vs UDP

| Aspect | TCP | UDP |
|--------|-----|-----|
| Connection | Connection-oriented (handshake) | Connectionless (no handshake) |
| Reliability | Guaranteed delivery | No guarantee |
| Ordering | Packets arrive in order | No ordering guarantee |
| Speed | Slower (overhead) | Faster (no overhead) |
| Header | 20+ bytes | 8 bytes |
| Flow Control | Yes (prevents overload) | No |
| Error Checking | Yes (extensive) | Yes (basic) |
| **Use Cases** | Web, email, SSH, FTP, file transfer | DNS, video streaming, gaming, VoIP |

---

## Acronym — ATIN

**A** — Application (users interact)
**T** — Transport (TCP/UDP, ports)
**I** — Internet (IP, routing)
**N** — Network Interface (MAC, cables)

Layers 4 → 3 → 2 → 1

---

## Real Example — SSH Connection
SSH to 192.168.1.50:
Layer 4 (Application): SSH protocol sends "login" command
Layer 3 (Transport): TCP port 22, 3-way handshake (SYN → SYN-ACK → ACK)
Layer 2 (Internet): IP packet destination 192.168.1.50
Layer 1 (Network Interface): ARP finds MAC, Ethernet frame through cables

---

## Security Angle — WannaCry 2017

**Attack Chain:**

Layer 4: Vulnerable SMB service (Windows file sharing)
→ Layer 3: Port 445 left open to internet (firewall failure)
→ Layer 2: No network segmentation (moved laterally)
→ Layer 1: No MAC filtering (could intercept)

**Result:** 150+ countries, 200,000+ machines, billions in damages.

**Lesson:** Defend all 4 layers, not just one.

---

## TCP 3-Way Handshake (Layer 3 Detail)

1. **SYN** — Client sends SYN packet with sequence number 100
   - "Can we talk? I'll start counting from 100."

2. **SYN-ACK** — Server responds with SYN-ACK
   - Sequence: 300
   - Acknowledgment: 101 (received client's 100, expecting 101 next)
   - "Yes, let's talk. I'll start counting from 300. I received your 100."

3. **ACK** — Client sends ACK
   - Sequence: 101
   - Acknowledgment: 301 (received server's 300, expecting 301 next)
   - "Great, I received your 300."

**Connection established. Data can flow.**

---

## When to Use OSI vs TCP/IP

**Use OSI Model when:**
- Interview theory questions
- Learning networking concepts
- Understanding detailed layer behaviors
- Academic/standardization context

**Use TCP/IP Model when:**
- Troubleshooting real networks
- Discussing actual internet protocols
- Engineering conversations
- Practical network design