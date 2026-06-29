# Day 80 — OSI Model (7 Layers)

**Volume:** 03 — Networking From Zero
**Topic:** 2 — OSI Model
**Requirement:** Memorize all 7 layers. Draw by hand 100 times. Asked in every security interview.

---

## The 7 Layers (Top to Bottom)

### Layer 7 — Application Layer
**What:** Users interact here. Browsers, email, SSH, DNS.
**Protocols:** HTTP, HTTPS, FTP, SMTP, SSH, Telnet, DNS
**Attacks:** SQL injection, phishing, DNS poisoning
**PDU:** Data
**Security Q:** What application is running? Can I exploit it?

### Layer 6 — Presentation Layer
**What:** Translate data into format both sides understand. Encryption, compression, encoding.
**Protocols:** TLS/SSL, JPEG, ASCII, UTF-8
**Attacks:** Man-in-the-middle if encryption weak
**PDU:** Data
**Security Q:** Is this encrypted? Can I decrypt it?

### Layer 5 — Session Layer
**What:** Manage conversations. Start, maintain, end connections. Session state.
**Protocols:** NetBIOS, RPC, PPTP
**Attacks:** Session hijacking, session token theft
**PDU:** Data
**Security Q:** Can I steal the session token?

### Layer 4 — Transport Layer
**What:** End-to-end delivery. TCP (reliable) or UDP (fast). Ports.
**Protocols:** TCP, UDP, SCTP
**TCP:** Connection-oriented, reliable, ordered, slower
**UDP:** Connectionless, fast, unreliable, used for DNS/video/gaming
**Attacks:** Port scanning, DDoS, TCP hijacking
**PDU:** Segment (TCP) or Datagram (UDP)
**Security Q:** What ports are open? Can I connect?

### Layer 3 — Network Layer
**What:** Route data across networks using IP addresses. Determine path.
**Protocols:** IP (IPv4, IPv6), ICMP (ping), BGP, OSPF
**Attacks:** IP spoofing, DDoS, routing attacks
**PDU:** Packet
**Security Q:** What IPs are on this network? Can I route through attacker machine?

### Layer 2 — Data Link Layer
**What:** Connect devices on SAME network using MAC addresses. Switches.
**Protocols:** Ethernet, PPP, ARP, MAC addressing
**Attacks:** ARP spoofing (man-in-the-middle), MAC spoofing
**PDU:** Frame
**Security Q:** Can I spoof MAC? Can I intercept frames?

### Layer 1 — Physical Layer
**What:** Actual wires, cables, wireless signals, hardware.
**Protocols:** Ethernet cables, fiber optics, WiFi, USB
**Attacks:** Cable cutting, signal jamming
**PDU:** Bit (Signal)
**Security Q:** Can I cut cables? Can I jam signals?

---

## Encapsulation Example — Sending Email

**Application (Layer 7):** You type email
From: you@example.com

To: friend@example.com

Subject: Hello

Body: This is my message

**Presentation (Layer 6):** Add encryption
TLS_ENCRYPT(email_data)

**Session (Layer 5):** Add session info
SESSION_ID: 12345

TIMESTAMP: 2026-06-29 15:30:00

[TLS_ENCRYPT(email_data)]

**Transport (Layer 4):** Add TCP header
SOURCE_PORT: 54321

DEST_PORT: 587 (SMTP)

SEQUENCE: 1001

[SESSION_ID, TIMESTAMP, TLS_ENCRYPT(email_data)]

**Network (Layer 3):** Add IP header
SOURCE_IP: 192.168.1.100

DEST_IP: 8.8.8.8

[SOURCE_PORT, DEST_PORT, SEQUENCE, SESSION_ID, email_data]

**Data Link (Layer 2):** Add Ethernet frame
SOURCE_MAC: AA:BB:CC:DD:EE:FF

DEST_MAC: 11:22:33:44:55:66

[SOURCE_IP, DEST_IP, SOURCE_PORT, email_data]

**Physical (Layer 1):** Convert to signals
Electrical signals travel through Ethernet cable

10101101010101... (bits)

---

## Decapsulation at Destination

At recipient's mail server, process reverses:

**Layer 1:** Electrical signals received
**Layer 2:** Ethernet frame stripped, MAC verified
**Layer 3:** IP packet stripped, IP verified
**Layer 4:** TCP segment stripped, port verified
**Layer 5:** Session info verified
**Layer 6:** TLS decryption
**Layer 7:** Email application receives readable email

---

## Memorization: All People Seem To Need Data Processing

Layer 7 — **A**pplication
Layer 6 — **P**resentation
Layer 5 — **S**ession
Layer 4 — **T**ransport
Layer 3 — **N**etwork
Layer 2 — **D**ata Link
Layer 1 — **P**hysical

**Acronym:** A-P-S-T-N-D-P (top to bottom) = 7-6-5-4-3-2-1

---

## Security Angle — Equifax Breach 2017

**Layer 7 Attack:** SQL injection in Java application
- Attacker accessed database with 147 million records

**Layers Not Defended:**

Layer 6 (Presentation): No encryption on database traffic
- Attacker read data in plaintext

Layer 5 (Session): Weak session management
- Attacker maintained access for months

Layer 4 (Transport): No TLS/SSL on database connections
- Unencrypted TCP traffic

Layer 3 (Network): No network segmentation
- No firewall between web server and database
- Attacker moved directly

Layer 2 (Data Link): No MAC filtering or port security
- No access control on switch ports

Layer 1 (Physical): No cable security
- Attacker could physically access network

**Result:** All 147 million records stolen.

**Lesson:** Defend all 7 layers, not just one.