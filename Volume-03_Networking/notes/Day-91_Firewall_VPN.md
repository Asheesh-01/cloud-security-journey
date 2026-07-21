# Day 91 — Firewall Basics and VPN Concepts

**Volume:** 03 — Networking From Zero
**Topics:** 14 — Firewall Basics + 15 — VPN Concepts
**Focus:** How firewalls filter traffic and how VPNs create encrypted tunnels.

---

## Firewall

### What It Does

Security device filtering network traffic based on defined rules. Every packet checked on entry and exit. Allowed or dropped based on rules.

Analogy: Airport security. Every passenger (packet) checked. ID verified (IP, port, protocol). Allowed through or denied based on policy.

---

## Stateless Firewall

Examines each packet independently. No memory of previous packets.

Decision based on: source IP, destination IP, port, protocol.

**Limitation:** Cannot distinguish legitimate response from attacker spoofing response. Attacker can craft packet with source port 80 and stateless firewall may allow it.

---

## Stateful Firewall

Tracks connection state table. Knows which connections legitimately established.

Connection states tracked:
- **NEW** — first packet of new connection (SYN in TCP)
- **ESTABLISHED** — connection established, data flowing
- **RELATED** — packet related to existing connection (FTP data channel)
- **INVALID** — does not match any known connection, often malicious

**Advantage:** Unsolicited packets rejected even if they look legitimate. Attacker cannot spoof response without matching state table entry.

State table example:

Source IP Source Port Dest IP Dest Port State
192.168.1.100 54321 142.250.195.46 443 ESTABLISHED


Only packets matching this entry allowed back. Unknown unsolicited packets from 142.250.195.46 rejected.

---

## iptables Chains

Three main chains:

**INPUT chain:**
- Rules for traffic coming TO this machine
- Applied when destination IP = this machine's IP
- Example: web server allowing port 443

**OUTPUT chain:**
- Rules for traffic LEAVING this machine
- Applied when source IP = this machine's IP
- Example: blocking outbound SMTP (prevent spam)

**FORWARD chain:**
- Rules for traffic PASSING THROUGH this machine
- Applied when this machine is routing (acting as router)
- Destination IP = different machine

**Packet flow:**

Incoming packet
↓
[PREROUTING] ← NAT happens here
↓
Is destination this machine?
├─ YES → [INPUT chain] → Local process
└─ NO → [FORWARD chain] → [POSTROUTING] → Out

Outgoing packet
↓
[OUTPUT chain]
↓
[POSTROUTING] ← NAT happens here
↓
Out to network


---

## Inbound vs Outbound Rules

**Inbound rules (INPUT chain):**
- Control traffic entering your machine
- Example: Allow port 443 from internet

**Outbound rules (OUTPUT chain):**
- Control traffic leaving your machine
- Example: Block port 25 outbound (prevent spam)

**Default policies:**
- **Default deny (recommended):** Block everything unless rule explicitly allows
- **Default allow (not recommended):** Allow everything unless rule explicitly blocks

Default deny = most secure. Attacker must find specific allowed port. Nothing open by accident.

---

## Example Firewall Rule Set (Web Server)

Default policy: DENY all incoming
Default policy: ALLOW all outgoing

Rule 1: ALLOW ESTABLISHED connections (responses to our requests)
Rule 2: ALLOW TCP inbound port 443 from any (HTTPS)
Rule 3: ALLOW TCP inbound port 22 from 10.0.0.5 only (SSH, admin only)
Rule 4: DENY TCP inbound port 3306 (MySQL, never expose)
Rule 5: DENY TCP inbound port 5432 (PostgreSQL, never expose)
Rule 6: DENY everything else


Traffic results:
- Internet → port 443: ALLOWED (Rule 2)
- Internet → port 22: DENIED (no matching rule, default deny)
- Admin (10.0.0.5) → port 22: ALLOWED (Rule 3)
- Any → port 3306: DENIED (Rule 4)
- Server responses: ALLOWED (Rule 1, ESTABLISHED state)

---

## VPN (Virtual Private Network)

### What It Does

Creates encrypted tunnel between two points across untrusted network (internet).

Analogy: Armored car on public road. Anyone sees the car (traffic exists) but nobody sees contents (encrypted). Travels on public roads (internet) but cargo is private.

---

## VPN Tunneling

Without VPN:

Your packet: [IP header: you → google.com] [Data: GET /index.html]
Travels across internet visible to ISP and anyone between you and destination


With VPN:

Original: [IP header: you → google.com] [Data: GET /index.html]
Encrypted: [VPN header: you → VPN server] [ENCRYPTED: original packet]


ISP sees: traffic from you to VPN server only.
VPN server decrypts and sends original packet to google.com.
Google sees: traffic from VPN server IP, not your IP.

---

## Split Tunneling vs Full Tunnel

**Split tunneling:**
- Only specific traffic goes through VPN (work resources)
- Personal traffic goes directly to internet
- Advantage: Faster for personal use, less VPN load
- Security risk: Personal traffic bypasses company controls

**Full tunnel:**
- ALL traffic goes through VPN
- Nothing bypasses VPN
- Advantage: All traffic monitored and protected
- Consideration: Slower, more VPN bandwidth required

---

## IPSec VPN

Operates at Layer 3 (Network). Encrypts entire IP packet.

**Two modes:**
- **Transport mode:** Encrypts payload only, not IP header. Used end-to-end.
- **Tunnel mode:** Encrypts entire original packet, adds new IP header. Used for VPN tunnels.

**Two protocols:**
- **AH (Authentication Header):** Provides integrity and authentication. No encryption.
- **ESP (Encapsulating Security Payload):** Provides encryption + integrity + authentication.

**Use case:** Site-to-site VPN (connecting two office networks permanently).

---

## SSL VPN

Operates at Layer 7 (Application). Uses TLS/SSL encryption.

Works through HTTPS (port 443). Rarely blocked by firewalls.

No special client software needed — browser-based option available.

**Use case:** Remote access VPN (employees connecting from home).

---

## IPSec vs SSL VPN

| Aspect | IPSec | SSL VPN |
|--------|-------|---------|
| OSI Layer | 3 (Network) | 7 (Application) |
| Encryption | ESP protocol | TLS/SSL |
| Client | Required | Browser or client |
| Port | Various (500, 4500) | 443 (HTTPS) |
| Use case | Site-to-site | Remote access |
| Firewall traversal | May be blocked | Rarely blocked |

---

## OpenVPN and WireGuard

**OpenVPN:**
- Open-source VPN using TLS/SSL encryption
- Highly configurable
- Works on all platforms
- Slower due to complexity

**WireGuard:**
- Modern VPN protocol (2018)
- Faster than OpenVPN
- Simpler codebase (4000 lines vs 100,000 lines)
- Uses modern cryptography (ChaCha20, Curve25519)
- Increasingly standard choice

---

## Real Incidents

### Colonial Pipeline (2021)

One VPN account. No MFA. Password found in dark web dump from previous breach.

Attacker logged in legitimately. Deployed DarkSide ransomware.

Result: 45% US East Coast fuel supply disrupted for 5 days. $4.4 million ransom paid.

**Root cause:** VPN without MFA. One password = full network access.

**Defense:** MFA on all VPN accounts. VPN access logs monitored. Least privilege on VPN accounts.

### FireEye Breach (2020)

Unpatched Pulse Secure VPN vulnerability (CVE-2019-11510).

Attacker read credentials from VPN server. Logged in legitimately.

Stole classified offensive security tools used by red teams worldwide.

**Root cause:** Unpatched VPN software.

**Defense:** Patch VPN software immediately. VPN is perimeter — vulnerabilities in VPN = vulnerabilities in perimeter.

---

## Security Checklist

**Firewall:**
- Default deny incoming
- Only explicitly needed ports allowed
- Database ports never exposed externally
- SSH restricted to admin IPs only
- Regular rule audit (remove old, unused rules)

**VPN:**
- MFA on all VPN accounts
- VPN software patched immediately
- VPN access logs monitored and alerted
- Least privilege (VPN users access only needed resources)
- Idle session timeouts configured
- Geo-blocking on VPN (block login from unexpected countries)