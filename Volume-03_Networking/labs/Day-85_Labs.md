# Day 85 — Labs: Network Devices Hands-On

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Lab 1 — Understand Your Network Gateway (Router)

```bash
ip route
```

**Output:**
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.100

**Analysis:**
- Default gateway: 192.168.1.1 (your router)
- Local network: 192.168.1.0/24 (direct, no router needed)
- Gateway device: eth0

**What it means:** To reach outside your network (8.8.8.8), packets go through router 192.168.1.1.

---

## Lab 2 — Identify Devices on Your Network (Switch + ARP)

```bash
arp -a
```

**Output:**
192.168.1.1 at AA:BB:CC:DD:EE:FF [ether] on eth0
192.168.1.50 at 11:22:33:44:55:66 [ether] on eth0
192.168.1.100 at 22:33:44:55:66:77 [ether] on eth0

**Analysis:**
- Each IP has unique MAC (learned by switch)
- Switch forwarded ARP replies so you learned all MACs
- All on same network (192.168.1.0/24)

**What it means:** Switch connected all these devices and allowed them to discover each other.

---

## Lab 3 — Find Router's MAC Address (Switch learning)

From Lab 2 output:
```bash
arp -a | grep 192.168.1.1
```

Gateway IP: 192.168.1.1
Gateway MAC: AA:BB:CC:DD:EE:FF

This MAC is on your switch. When you send to gateway, switch forwards using this MAC.

---

## Lab 4 — Test Firewall Rules (View Listening Ports)

Firewall blocks or allows based on ports. Check what ports your system listens on:

```bash
ss -tulnp
```

Or:

```bash
sudo netstat -tulnp
```

**Output:**
Proto Recv-Q Send-Q Local Address State   PID/Program name
tcp   0      0      0.0.0.0:22    LISTEN  1234/sshd
tcp   0      0      127.0.0.1:25  LISTEN  5678/postfix

**Analysis:**
- Port 22 (SSH): Listening on all interfaces (0.0.0.0:22)
- Port 25 (SMTP): Listening on localhost only (127.0.0.1:25)

**Firewall perspective:**
- External access to port 22: Allowed (if firewall allows)
- External access to port 25: Blocked (localhost only)

---

## Lab 5 — Test Connectivity to Router (Router/Gateway)

Ping tests if router is reachable:

```bash
ping -c 4 192.168.1.1
```

**Output:**
64 bytes from 192.168.1.1: icmp_seq=1 ttl=64 time=2.45 ms
64 bytes from 192.168.1.1: icmp_seq=2 ttl=64 time=2.31 ms
64 bytes from 192.168.1.1: icmp_seq=3 ttl=64 time=2.44 ms
64 bytes from 192.168.1.1: icmp_seq=4 ttl=64 time=2.39 ms

**What it means:** Router is reachable, ARP worked, ping succeeded, latency ~2ms (fast, local).

---

## Lab 6 — Test Internet Connectivity Through Router

Ping external IP (tests router routing):

```bash
ping -c 4 8.8.8.8
```

**Output:**
64 bytes from 8.8.8.8: icmp_seq=1 ttl=119 time=25.45 ms
...

**What it means:** Router routed your packet to internet, Google replied, packet returned. Internet connectivity works.

**TTL:** 119 (started at 64, router decremented to 119 — routing worked).

---

## Lab 7 — Trace Route to See Routers in Path

Traceroute shows every router hop to destination:

```bash
traceroute google.com
```

**Output:**
traceroute to google.com (142.250.195.46), 30 hops max, 60 byte packets
1  192.168.1.1 (192.168.1.1)  2.45 ms  2.31 ms  2.44 ms
2  10.10.0.1 (10.10.0.1)      5.34 ms  5.40 ms  5.28 ms
3  203.45.67.1 (203.45.67.1)  12.34 ms 12.45 ms 12.56 ms
4  203.45.67.100 (203.45.67.100) 18.20 ms 18.34 ms 18.45 ms
5  142.250.195.46 (142.250.195.46) 25.34 ms 25.45 ms 25.56 ms

**Analysis:**
1. Hop 1: Your gateway (192.168.1.1) — 2ms
2. Hop 2: ISP router (10.10.0.1) — 5ms
3. Hops 3-4: Internet backbone routers — 12-18ms
4. Hop 5: Google's server (142.250.195.46) — 25ms

**What it means:** Your packet traveled through 5 routers to reach Google. Each router forwarded to next hop.

---

## Lab 8 — View Linux Firewall Rules

Check iptables (Linux firewall):

```bash
sudo iptables -L -n -v
```

Or modern:

```bash
sudo ufw status verbose
```

**Output example:**
Chain INPUT (policy DROP)
target  prot opt in   source       destination
ACCEPT  tcp  --  eth0 0.0.0.0/0    0.0.0.0/0    tcp dpt:22
DROP    tcp  --  eth0 0.0.0.0/0    0.0.0.0/0    tcp dpt:23
ACCEPT  tcp  --  eth0 0.0.0.0/0    0.0.0.0/0    tcp dpt:80

**Analysis:**
- Port 22 (SSH): ACCEPT (allowed)
- Port 23 (Telnet): DROP (blocked)
- Port 80 (HTTP): ACCEPT (allowed)

**What it means:** Firewall rules in action. These rules determine what traffic is allowed.

---

## Lab 9 — Block a Port with Firewall

Example: Block port 25 (SMTP, email):

```bash
sudo ufw deny 25
sudo ufw status
```

**Output:**
Status: active
To                         Action      From

25                         DENY        Anywhere
22/tcp                     ALLOW       Anywhere

Port 25 now blocked.

**What it means:** Firewall rule added. Any traffic to port 25 dropped.

**Unblock:**
```bash
sudo ufw allow 25
```

---

## Lab 10 — Network Diagram Drawing

Draw your network with all 6 device types:
Internet
↓
[Firewall]
Rules: Allow 80, 443 inbound
Rules: Block 25 outbound
↓
[IPS]
Signatures: SQL injection
Anomalies: Unusual traffic
Inline: Blocks attacks
↓
[Router]
Gateway: 192.168.1.1
Routes between networks
↓
[Switch]
Connects devices
MAC table: IP → MAC mappings
↓
├─ Device A (192.168.1.100) — MAC AA:AA:AA:AA:AA:AA
├─ Device B (192.168.1.50) — MAC BB:BB:BB:BB:BB:BB
└─ Device C (192.168.1.200) — MAC CC:CC:CC:CC:CC:CC

Label each device with what it does.

---

## Lab 11 — Identify Firewall Type

From Lab 8 (iptables rules), your firewall is **stateful** if:

```bash
sudo iptables -L -n | grep -i state
```

Shows connection state tracking (established, new, related) = stateful.

If only simple IP/port rules = stateless.

Modern Linux iptables = stateful.

---

## Lab 12 — Real Scenario: Block Attacker IP

Scenario: Attacker IP 203.45.67.89 scanning your ports.

Block at firewall:

```bash
sudo ufw deny from 203.45.67.89
sudo ufw status
```

**Output:**
203.45.67.89              DENY        Anywhere

Now all traffic from 203.45.67.89 blocked (firewall layer).

**What it means:** Firewall is first line of defense. Attacker blocked before reaching any device.

---

## Lab 13 — Interview Question: Design DMZ Network

**Question:** E-commerce company needs:
- Web servers (public)
- Database (private)
- Employee workstations (internal)

Design network with firewall, router, switch.

**Answer:**
Internet
↓
Firewall
Allow: inbound 80, 443 (web)
Allow: inbound SSH 22 (admin only)
Block: all other inbound
↓
Router (VLAN routing)
VLAN 1: DMZ (192.168.1.0/24)
VLAN 2: Internal (192.168.10.0/24)
VLAN 3: Guest (192.168.20.0/24)
↓
Switch + VLANs
VLAN 1 DMZ: Web servers, load balancers
Port 80, 443 open (to internet)
Can reach VLAN 2 (database, restricted)
VLAN 2 Internal: Database servers, file servers
    Cannot reach internet directly
    Firewall between VLANs restricts access

VLAN 3 Guest: Employee WiFi
    Can reach internet
    Cannot reach VLAN 1, 2

Access control:
- Internet → VLAN 1 (DMZ): Allowed (web traffic)
- VLAN 1 → VLAN 2 (Database): Restricted (specific ports)
- VLAN 2 → Internet: Blocked
- VLAN 3 → Internet: Allowed
- VLAN 3 ↔ VLAN 1,2: Blocked

---

## Lab 14 — Speed Test

Identify device type for each scenario (30 seconds):

1. "I configured rules to block port 25 outbound" → Firewall
2. "I traced route to Google, saw 5 hops" → Router
3. "I checked ARP table, found 10 devices with their MACs" → Switch (via ARP)
4. "I enabled IDS to detect SQL injection" → IDS
5. "System blocked SQL injection packet before reaching database" → IPS
6. "Device broadcasts to all ports" → Hub (legacy)
7. "I set port security to restrict which MACs can connect" → Switch
8. "Firewall rule: allow port 443, deny port 25" → Firewall
9. "Traffic routed between 192.168.1.0/24 and 10.0.0.0/24" → Router
10. "Detected brute force attack and alerted, but didn't block" → IDS

**Answers:** 1-Firewall, 2-Router, 3-Switch, 4-IDS, 5-IPS, 6-Hub, 7-Switch, 8-Firewall, 9-Router, 10-IDS

---

## Lab 15 — Target Breach Analysis

Study the Target breach (2013):

**Question:** If you were designing Target's network, how would you prevent this breach?

**Answer template:**
1. **Firewall:** Restrict vendor HVAC access to only necessary ports (example: port 5000 for temperature data only)
2. **Network segmentation:** HVAC in separate VLAN (192.168.100.0/24)
3. **Router:** Firewall rules between VLANs (HVAC ↔ POS blocked)
4. **IDS:** Detect unusual outbound traffic from HVAC
5. **IPS:** Block lateral movement attempts
6. **Switch:** Port security restrict HVAC system to designated port
7. **Result:** Attacker confined to HVAC segment, cannot reach POS

Write full response.
