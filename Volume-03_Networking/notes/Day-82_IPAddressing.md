# Day 82 — IP Addressing

**Volume:** 03 — Networking From Zero
**Topic:** 4 — IP Addressing (IPv4 Focus)

---

## IPv4 Format

32-bit address written as 4 octets in dotted decimal notation.
192.168.1.100
192 = 11000000 (octet 1, 0-255)
168 = 10101000 (octet 2, 0-255)
1   = 00000001 (octet 3, 0-255)
100 = 01100100 (octet 4, 0-255)

Total addresses: 2^32 = 4,294,967,296 (roughly 4.3 billion)

---

## IP Address Classes

Determined by first octet:

| Class | First Octet | Network Bits | Host Bits | Example | Hosts |
|-------|------------|--------------|-----------|---------|-------|
| A | 1-126 | 8 | 24 | 10.0.0.0 | 16.7M |
| B | 128-191 | 16 | 16 | 172.16.0.0 | 65K |
| C | 192-223 | 24 | 8 | 192.168.0.0 | 254 |
| D | 224-239 | N/A | N/A | 224.0.0.0 | Multicast |
| E | 240-255 | N/A | N/A | 240.0.0.0 | Reserved |

---

## Private IP Ranges (RFC 1918)

Reserved for internal use only. Not routable on internet.

| Class | Range | CIDR | Hosts |
|-------|-------|------|-------|
| A | 10.0.0.0 to 10.255.255.255 | 10/8 | 16.7M |
| B | 172.16.0.0 to 172.31.255.255 | 172.16/12 | 1M |
| C | 192.168.0.0 to 192.168.255.255 | 192.168/16 | 65K |

Everything else is public (routable on internet).

---

## Public vs Private IP

**Public IP:**
- Unique globally
- Routable on internet
- Assigned by ISP
- Cost money
- Example: 8.8.8.8 (Google DNS)

**Private IP:**
- Only used internally
- Reserved ranges only
- Multiple organizations use same ranges
- Free, unlimited
- Example: 192.168.1.100

---

## Static vs Dynamic IP

**Static IP:**
- Manually assigned
- Never changes
- Used for: servers, printers, network devices
- Requires manual configuration
- Example: web server at 192.168.1.50 always

**Dynamic IP:**
- Assigned by DHCP server
- Changes when lease expires
- Used for: laptops, desktops, mobile
- Automatic assignment
- Example: home computer gets different IP each day

---

## DHCP (Dynamic Host Configuration Protocol)

Server automatically assigns IPs to devices.

Process (DORA):
1. **D**iscover — device broadcasts "who is DHCP server?"
2. **O**ffer — DHCP server responds "I'll give you 192.168.1.100"
3. **R**equest — device confirms "I accept 192.168.1.100"
4. **A**cknowledge — server confirms "yours for 24 hours" (lease time)

After lease expires, device renews.

---

## APIPA (Automatic Private IP Addressing)

If device cannot reach DHCP server:
- Device waits
- Still no DHCP response
- Device auto-assigns 169.254.x.x/16
- Can talk to other APIPA devices
- Cannot reach outside network

**Indicator:** 169.254.x.x = DHCP problem, investigate server.

---

## Subnet Mask and CIDR

**Subnet Mask:** Determines network vs host bits.
255.255.255.0 = 11111111.11111111.11111111.00000000
First 24 bits (255.255.255) = network
Last 8 bits (.0) = host

**CIDR notation:** /24 (shorthand for /24)
192.168.1.0/24
/24 = first 24 bits are network, last 8 are host

---

## Calculating Subnet Information

Example: 192.168.1.0/24

- **Network address:** 192.168.1.0 (all host bits = 0)
- **First usable host:** 192.168.1.1
- **Last usable host:** 192.168.1.254
- **Broadcast address:** 192.168.1.255 (all host bits = 1)
- **Total addresses:** 256 (2^8)
- **Usable hosts:** 254

---

## NAT (Network Address Translation)

Translates between private and public IPs.
Private device (192.168.1.100)
↓
Router (private: 192.168.1.1, public: 45.67.89.123)
↓
Internet
Outgoing: NAT changes source from 192.168.1.100 to 45.67.89.123
Incoming: NAT changes destination from 45.67.89.123 back to 192.168.1.100

---

## Default Gateway

Router that connects your subnet to other networks.

If IP on subnet: send directly
If IP different subnet: send to gateway
Your network: 192.168.1.0/24
Gateway: 192.168.1.1 (router)
To reach 192.168.1.100 (same subnet): send directly
To reach 8.8.8.8 (different): send to gateway 192.168.1.1

---

## Security Angle — Shodan Exposed IPs

Shodan search engine finds devices on internet. Attackers use to find:
- Default credentials on public databases
- Misconfigured firewalls
- Exposed services

Example attack:
1. Search Shodan: "port:3306 mysql" (MySQL port 3306 public)
2. Find company database with public IP exposed
3. Try default MySQL credentials
4. Access database directly from internet
5. Steal data

**Prevention:** Keep databases on private IPs only. Expose only web servers publicly.