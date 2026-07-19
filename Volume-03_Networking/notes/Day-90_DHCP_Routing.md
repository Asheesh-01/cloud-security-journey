# Day 90 — DHCP and Routing

**Volume:** 03 — Networking From Zero
**Topics:** 12 — DHCP + 13 — Routing
**Focus:** How devices get IP addresses automatically and how packets find their destination.

---

## DHCP (Dynamic Host Configuration Protocol)

### What It Does

Automatically assigns IP addresses and network configuration to devices when they connect.

Analogy: Hotel front desk. You arrive (connect), get a room number (IP) for your stay (lease time). Check out (disconnect) = room number available again.

### DORA Process (Four Steps)

**Step 1 — Discover (Client → Broadcast)**

Client has no IP. Sends broadcast to entire network:
- Source IP: 0.0.0.0 (no IP yet)
- Destination: 255.255.255.255 (broadcast, all devices)
- Message: "I need an IP address. My MAC is AA:BB:CC:DD:EE:FF"

**Step 2 — Offer (DHCP Server → Client)**

Server checks available pool, responds:
- Offers IP: 192.168.1.105
- Includes: gateway (192.168.1.1), DNS (8.8.8.8), subnet mask (255.255.255.0), lease time (86400 seconds)

**Step 3 — Request (Client → Broadcast)**

Client accepts offer (still broadcasts to inform other DHCP servers):
- "I accept 192.168.1.105 from server 192.168.1.1"

**Step 4 — Acknowledge (DHCP Server → Client)**

Server confirms assignment:
- "192.168.1.105 is yours for 24 hours."
- Client configures IP, gateway, DNS, subnet mask

**Total time: less than 1 second.**

### DHCP Options Received

| Option | Value | Purpose |
|--------|-------|---------|
| IP Address | 192.168.1.105 | Device's assigned address |
| Subnet Mask | 255.255.255.0 | Which part is network vs host |
| Gateway | 192.168.1.1 | Router for external traffic |
| DNS Server | 8.8.8.8 | Name resolution server |
| Lease Time | 86400 seconds | How long IP is valid (24 hours) |

### DHCP Ports

- Port 67 = DHCP server (receives Discover and Request)
- Port 68 = DHCP client (receives Offer and Acknowledge)

---

## DHCP Security Risks

### DHCP Starvation Attack

Attacker sends thousands of DHCP Discover packets with fake MAC addresses.

Server assigns IP from pool for each fake request.

Pool exhausted. Real devices cannot get IPs. DoS attack.

**Defense:** DHCP snooping limits DHCP requests per switch port.

### Rogue DHCP Server Attack

Attacker runs fake DHCP server on network.

Client broadcasts Discover. Both real and rogue server respond.

If rogue server responds first, client accepts rogue's offer.

Rogue sends attacker's IP as gateway and DNS server.

All traffic routes through attacker (MITM). All DNS queries go to attacker.

**Defense:** DHCP snooping designates only trusted ports to receive DHCP Offers.

---

## Routing

### What It Does

Decides path data takes from source to destination across networks.

Analogy: Google Maps for network packets. Routing table = map. Default gateway = highway on-ramp. Routers = intersections.

### Routing Table

Every device has routing table. OS checks it before sending any packet.
Destination       Gateway          Interface    Metric
0.0.0.0/0         192.168.1.1      eth0         100
192.168.1.0/24    0.0.0.0          eth0         0
127.0.0.0/8       0.0.0.0          lo           0

**Reading each row:**

- `0.0.0.0/0` = default route (any destination not matching other rows) → send to gateway 192.168.1.1
- `192.168.1.0/24` = local network → send directly (no gateway, `0.0.0.0` = direct delivery)
- `127.0.0.0/8` = loopback → stays local on `lo` interface

**Longest prefix match:** Most specific route wins. /28 preferred over /24 preferred over /0.

### How Routing Works (Step by Step)

You visit google.com (142.250.195.46):

1. OS checks routing table: "Is 142.250.195.46 in 192.168.1.0/24?" No.
2. OS checks routing table: "Specific route for 142.250.195.46?" No.
3. OS uses default route: send to gateway 192.168.1.1
4. Gateway checks its routing table, forwards to ISP router
5. Each router forwards to next hop until reaching Google's network
6. Google's router: "Is 142.250.195.46 in my network?" Yes. Delivers directly.

### Default Gateway

Router connecting your network to outside world.

All traffic not matching local network goes here first.

If gateway is down = cannot reach internet, even if local network works.

---

## Static vs Dynamic Routing

### Static Routing

Administrator manually enters routes:

```bash
ip route add 10.0.0.0/24 via 192.168.1.254
```

**Characteristics:**
- Never changes unless manually edited
- Predictable and simple
- Does not adapt if link fails
- Used in: small networks, specific traffic paths

### Dynamic Routing

Routers automatically learn routes from each other using routing protocols.

**Characteristics:**
- Adapts automatically when links fail
- Scales to large networks
- More complex to configure
- Used in: corporate networks, ISPs, internet

### OSPF (Open Shortest Path First)

Interior routing protocol. Used within organization.

All routers share complete network map (Link State Database).

Calculates shortest path using Dijkstra algorithm.

Automatically reroutes if link fails.

Used in: corporate networks, data centers.

### BGP (Border Gateway Protocol)

Exterior routing protocol. Used between organizations and ISPs.

Powers internet routing — every ISP and major company uses BGP.

Routers share reachability information (which networks they can reach).

Highly policy-based — organizations control which routes they accept and advertise.

Used in: ISPs, large enterprises, AWS, Azure, GCP.

---

## Security Angle — Real Incidents

### Amazon Route 53 BGP Hijack (2018)

1. Attacker announced fake BGP route claiming faster path to Amazon's DNS (205.251.196.0/24)
2. Some ISPs accepted the fake route without verification
3. DNS queries for MyEtherWallet.com routed to attacker's DNS server
4. Attacker's DNS responded with attacker's IP (fake wallet website)
5. Users entered credentials on fake site
6. Attacker stole approximately $152,000 in cryptocurrency

**Duration:** 2 hours before corrected.

**Root cause:** BGP has no built-in authentication. Routers accept announcements without verification.

**Defense:** RPKI (Resource Public Key Infrastructure) validates BGP route announcements cryptographically.

### China Telecom BGP Leak (2010)

China Telecom accidentally announced BGP routes for 15% of entire internet for 18 minutes.

Traffic from hundreds of countries routed through China (including US military and government traffic).

**Root cause:** Misconfigured router, no route filtering.

**Defense:** BGP route filtering, RPKI validation, prefix limits.