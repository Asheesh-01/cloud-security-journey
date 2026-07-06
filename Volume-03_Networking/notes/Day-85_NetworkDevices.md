# Day 85 — Network Devices

**Volume:** 03 — Networking From Zero
**Topic:** 7 — Network Devices
**Focus:** Router, Switch, Hub, Firewall, IDS, IPS

---

## Device Comparison Table

| Device | Layer | Function | Scope | Decision | Example |
|--------|-------|----------|-------|----------|---------|
| Router | 3 | Route between networks | IP addresses | "Where is this IP?" | Cisco, Juniper |
| Switch | 2 | Connect on same network | MAC addresses | "Which port has this MAC?" | Dell, HP |
| Hub | 2 | Broadcast to all | All ports | "Send to everyone" | (Legacy, obsolete) |
| Firewall | 4+ | Filter traffic | Ports, protocols | "Is this port allowed?" | Fortinet, Palo Alto |
| IDS | 3-7 | Detect attacks | Pattern matching | "Does this look like attack?" (detects only) | Snort, Suricata |
| IPS | 3-7 | Prevent attacks | Pattern matching | "Does this look like attack?" (blocks) | Cisco IPS, Suricata |

---

## Device 1 — Router (Layer 3)

**What it does:** Routes packets between different networks using IP addresses.

**Operates at:** Layer 3 (Network Layer)

**Location:** Between networks. Gateway between your network and internet.

**Decision logic:**
Packet arrives: destination 8.8.8.8
Router checks routing table
"To reach 8.8.8.8, send through ISP link"
Router forwards to ISP

**Security role:** Controls traffic between networks. Implements access lists.

**Defense mechanism:** Routing table, access lists, ACLs.

**Failure example:** Misconfigured router allows attacker traffic through to internal network.

---

## Device 2 — Switch (Layer 2)

**What it does:** Connects devices on same network using MAC addresses.

**Operates at:** Layer 2 (Data Link Layer)

**Location:** Inside network. All devices plugged in.

**Decision logic:**
Frame arrives: destination MAC BB:BB:BB:BB:BB:BB
Switch checks MAC table
"BB:BB:BB:BB:BB:BB is on port 5"
Switch forwards frame to port 5 only

**Advantage over hub:** Only intended port receives frame (unicast). Not broadcast to all.

**Security features:**
- Port security: restrict which MACs can connect
- VLAN: logically divide network
- STP: prevent loops

**Failure example:** Attacker plugs into switch, sniffs traffic from all other ports.

---

## Device 3 — Hub (Layer 2, Legacy)

**What it does:** Broadcasts incoming frame to ALL ports.

**Operates at:** Layer 2 (Data Link Layer)

**Location:** Rarely used (replaced by switches in all modern networks).

**Decision logic:**
Frame arrives on port 1
Hub broadcasts to ports 2, 3, 4, 5, 6, 7, 8 (all others)
All devices receive frame

**Security risk:** Any device can sniff all traffic (no privacy).

**Why obsolete:** Inefficient, insecure, switches are better.

**Modern equivalent:** Switch in hub mode (for testing/troubleshooting only).

---

## Device 4 — Firewall (Layer 4+)

**What it does:** Filters traffic based on rules (ports, protocols, applications).

**Operates at:** Layer 4 (Transport), often Layer 7 (Application).

**Location:** Perimeter. Between internet and internal network.

**Decision logic:**
Packet arrives: source IP, destination port 443
Firewall checks rules
"Port 443 inbound: ALLOW"
Firewall forwards to internal network
Packet arrives: source internal, destination port 25 (SMTP)
Firewall checks rules
"Port 25 outbound: BLOCK"
Firewall drops packet

**Types:**

**Stateless Firewall:**
- Examines each packet independently
- No memory of connections
- Simple but less secure
- Examines: source IP, destination IP, port, protocol

**Stateful Firewall:**
- Tracks connection state
- Remembers established connections
- More secure
- Knows: "Is this packet part of established SSH session or new attack?"
- Examines: source, destination, port, protocol, connection state

**Next-Generation Firewall (NGFW):**
- Understands applications (Layer 7)
- Can block specific applications (YouTube, Facebook)
- Performs deep packet inspection
- Blocks threats at application level

**Example rules:**
Rule 1: Allow inbound HTTPS (443) — YES
Rule 2: Allow inbound SSH (22) — NO
Rule 3: Allow outbound SMTP (25) — NO
Rule 4: Default deny (block everything else) — YES

**Security role:** First line of defense. Prevents unauthorized access. Logs all traffic.

---

## Device 5 — IDS (Intrusion Detection System)

**What it does:** Monitors traffic for attacks. DETECTS but DOES NOT block.

**Operates at:** Layers 3-7 (passive, non-blocking).

**Location:** Passive tap or span port. Copies traffic, does not block.

**Decision logic:**
Traffic arrives: 1000 SSH login attempts in 5 seconds
IDS recognizes: "Brute force attack"
IDS generates alert: "Possible SSH brute force from 203.45.67.89"
Security team receives alert
Attacker packets already sent (attack already happened)

**Detection methods:**

**Signature detection:**
- Known attack pattern
- Example: `SELECT * FROM users` (SQL injection)
- If seen in traffic, alert
- Fast, low false positives

**Anomaly detection:**
- Learn normal traffic
- Detect deviations
- Example: "User usually 100MB/day, today 10GB" = anomaly
- Slower, more false positives

**Common IDSs:** Snort, Suricata, Zeek, IBM Qradar

**Security role:** Detection and forensics. Generates logs and alerts. No real-time blocking.

**Limitation:** Attack is detected AFTER it happens (historical).

---

## Device 6 — IPS (Intrusion Prevention System)

**What it does:** Monitors traffic for attacks. DETECTS AND BLOCKS.

**Operates at:** Layers 3-7 (inline, blocking).

**Location:** Inline (in path of all traffic). Must block to allow traffic through.

**Decision logic:**
Traffic arrives: SQL injection payload
IPS detects: "SQL injection attack"
IPS blocks packet immediately
Malicious packet never reaches database
Attack fails in real-time

**Same detection as IDS:** Signatures + anomalies.

**Difference:** IPS blocks (inline), IDS alerts (passive).

**Challenge:** Must not block legitimate traffic (false positives cause outages).

**Common IPSs:** Cisco IPS, Suricata inline mode, Palo Alto NGFW

**Security role:** Real-time protection. Blocks attacks immediately. Prevents damage.

**Advantage:** Attack blocked before reaching target.

---

## Device Placement (Network Architecture)
Internet
↓
Firewall (Layer 4+)
├─ Blocks unwanted traffic
├─ Rules: "Allow port 80, 443 inbound"
└─ Rules: "Block port 25 outbound"
↓
IPS (Layer 3-7, inline)
├─ Detects and blocks attacks
├─ Signatures: SQL injection, malware
└─ Anomalies: unusual patterns
↓
Router (Layer 3)
├─ Routes between networks
├─ Routing table: "To 8.8.8.8, use ISP link"
└─ Access lists: zone-based routing
↓
Switch (Layer 2)
├─ Connects devices on same network
├─ MAC table: "MAC BB:BB... is port 5"
├─ VLANs: logically separate departments
└─ Port security: restrict MACs per port
↓
Devices (Computers, servers, printers)

**Traffic flow:**
- **Inbound:** Firewall → IPS → Router → Switch → Device
- **Outbound:** Device → Switch → Router → Firewall → Internet

Each device adds layer of protection.

---

## IDS vs IPS Comparison

| Aspect | IDS | IPS |
|--------|-----|-----|
| Detection | Yes | Yes |
| Blocking | No | Yes |
| Placement | Passive (span port) | Inline (must block) |
| Latency | None (passive) | Minimal (inline) |
| False positives | Alert only | Block (can block legitimate) |
| Use case | Forensics, monitoring | Real-time protection |
| When to use | Every network | High-security networks |

---

## Real Incident — Target Breach (2013)

**Network architecture failure:**
Internet
↓
Firewall (over-permissive rules)
↓
Router (flat network, no segmentation)
↓
Switch
├─ HVAC vendor network (open to all)
├─ POS systems (cash registers)
└─ Corporate network (all same segment)
No IDS, no IPS, no VLAN separation.

**Attack timeline:**
1. Attacker compromises HVAC vendor
2. Gains access to Target's HVAC network
3. Firewall allows vendor full access (over-permissive)
4. Attacker moves laterally (no firewall between segments)
5. Reaches POS systems
6. Installs malware on cash registers
7. Steals credit card data for 6 months
8. 40 million cards compromised

**What would stop it:**
1. Firewall restricting vendor to only necessary ports
2. VLAN separating HVAC from POS
3. IDS detecting unusual outbound traffic from POS
4. IPS blocking lateral movement
5. Network segmentation limiting attacker scope

---

## Security Angle — Proper Network Design

**Secure architecture:**
Internet
↓
Firewall (stateful, NGFW)
Allow: inbound 80, 443 only
Allow: outbound 443, 53 only
Block: everything else
↓
IPS (inline)
Detect: SQL injection, malware
Block: suspicious patterns
↓
Router
Zone 1: DMZ (public web servers)
Zone 2: Internal (database, file servers)
Zone 3: Guest (separate WiFi)
↓
Switch + VLANs
VLAN 1: DMZ (192.168.1.0/24)
VLAN 2: Internal (192.168.10.0/24)
VLAN 3: Guest (192.168.20.0/24)
Firewall between VLANs
Port security: restrict MACs per port
STP: prevent loops

**Access control:**
- Internet → DMZ (web servers): allowed
- DMZ → Internal (database): restricted to specific queries
- Internal → Internet: restricted to specific ports
- DMZ ↔ Guest: blocked
- Guest → Internet: allowed but monitored

**Result:** Defense in depth. Multiple layers stop attackers at each stage.

---

## Devices at Each Layer Summary
Layer 7 (Application) — IPS/IDS, Firewall (NGFW)
Layer 6 (Presentation) — (none)
Layer 5 (Session) — IPS/IDS, Firewall
Layer 4 (Transport) — Firewall, IPS/IDS
Layer 3 (Network) — Router, IPS/IDS
Layer 2 (Data Link) — Switch, IPS/IDS (some models)
Layer 1 (Physical) — (none - cables)

---

## Interview Questions

**Q1:** Explain difference between IDS and IPS. When would you use each?

**A1:** 
- IDS (Intrusion Detection System): Monitors and detects attacks. Does NOT block. Generates alerts for investigation. Use for monitoring and forensics.
- IPS (Intrusion Prevention System): Monitors, detects, AND blocks attacks. Inline, real-time protection. Use for critical networks where blocking is acceptable.
- Both together: IDS logs everything, IPS blocks critical threats.

**Q2:** Design network security for company with web servers, databases, employee workstations.

**A2:**
- Firewall: Perimeter defense, allow port 80/443 inbound only
- IPS: Detect SQL injection, malware, DoS attempts
- Router: Route between DMZ (web) and Internal (database)
- VLANs: Separate web, database, employee networks
- Database: Private VLAN, only accessible from web servers
- Employee: Separate VLAN, limited internet access
- Monitoring: IDS on each segment, logs centralized

**Q3:** Target breach scenario. What would stop it?

**A3:**
- Problem: HVAC vendor on same network as POS systems
- Solution: Network segmentation (VLAN)
- Firewall: Restrict vendor to specific ports only
- IDS/IPS: Detect lateral movement from HVAC to POS
- Port security: Restrict which devices can connect to which ports
- Result: Attacker confined to HVAC segment, cannot reach POS

---

## Commands to Test

**Check router IP:**
```bash
ip route
netstat -r
```

**Check MAC table (switch):**
```bash
arp -a
```

**Check firewall rules (on Linux):**
```bash
sudo iptables -L -n
sudo ufw status
```

**Check listening ports (IPS/IDS perspective):**
```bash
ss -tulnp
```

**Ping (tests router):**
```bash
ping -c 4 8.8.8.8
```

**Trace route (sees routers in path):**
```bash
traceroute google.com
```