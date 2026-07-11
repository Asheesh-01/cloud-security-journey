# Day 87 — Transport Protocols (TCP vs UDP)

**Volume:** 03 — Networking From Zero
**Topic:** 9 — Transport Protocols
**Layer:** Layer 4 (Transport)
**Critical:** Understand 3-way handshake in detail. Asked in every security interview.

---

## TCP Overview

Transmission Control Protocol. Connection-oriented, reliable, ordered delivery.

**Characteristics:**
- Establishes connection first (3-way handshake)
- Every packet numbered (sequence number)
- Every packet acknowledged
- Resends if lost
- Packets arrive in order
- Slower due to overhead
- Used when accuracy critical

**Use cases:** Email, banking, file transfer, SSH, HTTP/HTTPS, FTP

---

## UDP Overview

User Datagram Protocol. Connectionless, no guarantees.

**Characteristics:**
- No connection setup
- Send data immediately
- No sequence numbers
- No acknowledgments
- No retransmission
- Faster, low overhead
- Used when speed critical

**Use cases:** DNS, video streaming, online gaming, VoIP, DHCP

---

## TCP 3-Way Handshake (Critical)

### Connection Establishment

**Step 1: SYN (Client → Server)**

Client initiates:

Source: 192.168.1.100:54321
Destination: 192.168.1.50:22
Flag: SYN
Sequence: 1000 (random)
Payload: (empty)

Means: "I want to connect. My sequence starts at 1000."

**Step 2: SYN-ACK (Server → Client)**

Server responds:
Source: 192.168.1.50:22
Destination: 192.168.1.100:54321
Flag: SYN, ACK
Sequence: 2000 (random)
Acknowledgment: 1001 (client's seq + 1)
Payload: (empty)

Means: "I acknowledge your 1000 (expecting 1001 next). My sequence is 2000."

**Step 3: ACK (Client → Server)**

Client confirms:
Source: 192.168.1.100:54321
Destination: 192.168.1.50:22
Flag: ACK
Sequence: 1001 (continues from 1000)
Acknowledgment: 2001 (server's seq + 1)
Payload: (empty)

Means: "I acknowledge your 2000 (expecting 2001 next). Connection established."

### States During Handshake

| Step | Client State | Server State |
|------|--------------|--------------|
| Before | CLOSED | LISTEN |
| After SYN | SYN_SENT | LISTEN |
| After SYN-ACK | SYN_SENT | SYN_RECEIVED |
| After ACK | ESTABLISHED | ESTABLISHED |

### Why Sequence Numbers

Sequence numbers detect:
- **Packet loss:** If sequence jumps (client sent 100-109, received ack for 100, then 110), server knows 101-109 missing
- **Out-of-order delivery:** Server knows correct order by sequence numbers
- **Duplicates:** If receives same sequence twice, it's duplicate
- **Attacks:** Random sequence numbers prevent attackers predicting next number (SYN cookies defense)

### Sequence Number Example
Client sends: seq=1000, data="HELLO" (5 bytes)
Next seq will be: 1000 + 5 = 1005
Server acknowledges: ack=1005 (means "I received all data up to 1004, expecting 1005 next")
Client sends: seq=1005, data="WORLD" (5 bytes)
Next seq will be: 1005 + 5 = 1010
Server acknowledges: ack=1010

---

## Data Transfer (After ESTABLISHED)

Once connection established, data flows both directions with constant acknowledgments:
Timeline:
T=0ms   Client SYN: seq=1000
T=2ms   Server SYN-ACK: seq=2000, ack=1001
T=4ms   Client ACK: seq=1001, ack=2001 → ESTABLISHED
T=6ms   Client sends: seq=1001, data="USER" (4 bytes)
T=8ms   Server ACK: ack=1005 (received 1001-1004)
T=10ms  Server sends: seq=2001, data="PASSWORD:" (10 bytes)
T=12ms  Client ACK: ack=2011 (received 2001-2010)

**Every packet acknowledged. No loss goes undetected.**

---

## TCP Connection Closing (Graceful)

**Step 1: Client sends FIN**
Flag: FIN
Sequence: 1150

Means: "I'm done sending data."

**Step 2: Server sends FIN-ACK**
Flag: FIN, ACK
Acknowledgment: 1151 (acknowledges FIN)
Sequence: 2500

Means: "I acknowledge your FIN. I'm also done sending."

**Step 3: Client sends ACK**
Flag: ACK
Acknowledgment: 2501

Means: "I acknowledge your FIN. Goodbye."

**Connection closed. Both sides know transmission ended gracefully.**

---

## TCP Connection States (Full List)

| State | Meaning | Duration |
|-------|---------|----------|
| CLOSED | No connection | N/A |
| LISTEN | Server waiting for connections | Until connection or shutdown |
| SYN_SENT | Client sent SYN, waiting for SYN-ACK | ~1-30 seconds |
| SYN_RECEIVED | Server sent SYN-ACK, waiting for ACK | ~1-30 seconds |
| ESTABLISHED | Connection active, data flowing | Variable (minutes to hours) |
| FIN_WAIT_1 | Sent FIN, waiting for FIN or FIN-ACK | ~1-60 seconds |
| FIN_WAIT_2 | Received ACK of FIN, waiting for FIN | ~1-60 seconds |
| CLOSE_WAIT | Received FIN from peer, waiting for app close | Variable |
| LAST_ACK | Sent FIN-ACK, waiting for final ACK | ~1-30 seconds |
| TIME_WAIT | Both sent FIN, waiting for timeout | 2 minutes (RFC 1122) |

### Why TIME_WAIT (2 minutes)

Old packets from closed connection may arrive late. TIME_WAIT prevents:
- Old packet arriving to new connection using same port numbers
- Confusion between connections

After 2 minutes, port is safe to reuse.

---

## TCP vs UDP Comparison

| Aspect | TCP | UDP |
|--------|-----|-----|
| Setup | 3-way handshake | None, send immediately |
| Reliability | Guaranteed delivery | Best effort |
| Ordering | Packets arrive in order | Unordered possible |
| Sequence numbers | Yes | No |
| Acknowledgments | Yes, every packet ACKed | No |
| Retransmission | Yes, if lost | No, lost forever |
| Speed | Slower (overhead) | Faster (minimal overhead) |
| Use TCP | Email, banking, files, SSH, HTTP | Use UDP | DNS, video, gaming |

---

## UDP Connectionless Example

**DNS Query:**
Client sends immediately (no setup):
"What is IP for google.com?"
Server responds (or doesn't):
"142.250.195.46"
Done. No sequence numbers, no ACK, no guaranteed delivery.
Total time: ~50ms (vs TCP which takes 6ms just for handshake)

**If response lost?** Client times out and sends new query. UDP doesn't retransmit.

---

## Security Angle — SYN Flood Attack

### Attack Mechanism

Attacker sends thousands of SYN packets:
Step 1: Attacker sends SYN (step 1 of handshake)
Step 2: Server responds with SYN-ACK (step 2)
Step 3: Attacker never sends ACK (blocks step 3)

Server now has half-open connection in SYN_RECEIVED state, waiting for ACK that never comes.

**Problem:** Server has limited connection table. Thousands of SYN packets fill table.

**Result:** Legitimate users cannot connect. Server cannot accept new connections.

### Defense

1. **SYN Cookies:** Server doesn't store half-open state, encodes state in sequence number
2. **Rate limiting:** Drop SYN packets if rate exceeds threshold
3. **Firewall:** Drop SYN from same source if volume suspicious
4. **Connection limits:** Limit SYN_RECEIVED per IP

---

## Real Incident — Mirai Botnet 2016

Infected 600,000+ IoT devices (IP cameras, DVRs, routers).

Attack: Sent millions of UDP DDoS packets to Dyn (major DNS provider).

Result: Took down Twitter, Netflix, GitHub, Airbnb for hours.

Root cause: Default credentials on IoT devices, weak UDP security model.

---

## Sequence Number Attacks

### Sequence Prediction (Old Attack, Patched)

If attacker guesses sequence numbers, could inject packets without being seen (session hijacking).

Modern systems use random sequence numbers (cryptographic PRNG), making prediction impossible.

### IP Spoofing in UDP

UDP has no connection establishment, so attacker can forge source IP:
Attacker sends UDP packet spoofing victim's IP:
Source: 192.168.1.100 (spoofed, actually attacker)
Destination: DNS server
DNS server responds to victim's IP with large answer (amplification).
Attacker sends thousands of these, victim's network flooded.

TCP makes spoofing harder (3-way handshake requires responding to ACK from real source).

---

## Commands

**View TCP connections:**
```bash
ss -tulnp
netstat -tulnp
```

**View connection states:**
```bash
ss -tan | grep ESTABLISHED
```

**Capture TCP handshake (see in next topic lab):**
```bash
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-syn != 0'
```

**Test TCP connection:**
```bash
nc -zv 192.168.1.50 22
```

**Test UDP (DNS):**
```bash
nc -zu 8.8.8.8 53
```