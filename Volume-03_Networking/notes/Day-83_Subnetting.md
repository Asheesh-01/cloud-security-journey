# Day 83 — Subnetting

**Volume:** 03 — Networking From Zero
**Topic:** 5 — Subnetting
**Requirement:** Calculate subnets by hand instantly. Asked in every security interview.

---

## CIDR Notation Explained

/24 = 24 network bits, 8 host bits
32 total bits
/24 prefix length
32 - 24 = 8 host bits
2^8 = 256 addresses
256 - 2 (network + broadcast) = 254 usable

---

## Subnet Calculation Formula

**Given:** Network address and CIDR (e.g., 192.168.1.0/24)

**Calculate:**
1. Host bits = 32 - CIDR prefix
2. Total addresses = 2^(host bits)
3. Usable addresses = total - 2
4. Subnet mask = convert prefix to dotted decimal
5. First usable IP = network + 1
6. Last usable IP = broadcast - 1
7. Broadcast = network + (total - 1)

---

## Common Subnets (Memorize These)

| Prefix | Subnet Mask | Total | Usable | Use Case |
|--------|------------|-------|--------|----------|
| /24 | 255.255.255.0 | 256 | 254 | Standard LAN |
| /28 | 255.255.255.240 | 16 | 14 | Small subnet |
| /30 | 255.255.255.252 | 4 | 2 | Point-to-point (routers) |
| /32 | 255.255.255.255 | 1 | 0 | Single host (loopback) |
| /16 | 255.255.0.0 | 65K | 65K | Large network |

---

## Example 1: /24 Subnet
192.168.1.0/24
Host bits: 32 - 24 = 8
Addresses: 2^8 = 256
Usable: 254
Subnet mask: 255.255.255.0
Network: 192.168.1.0
First: 192.168.1.1
Last: 192.168.1.254
Broadcast: 192.168.1.255

---

## Example 2: /28 Subnet
192.168.1.0/28
Host bits: 32 - 28 = 4
Addresses: 2^4 = 16
Usable: 14
Subnet mask: 255.255.255.240
Network: 192.168.1.0
First: 192.168.1.1
Last: 192.168.1.14
Broadcast: 192.168.1.15

**Subnet mask calculation:**
- /28 means 28 bits set to 1
- First 24 bits: 255.255.255 (3 octets)
- Last 4 bits in 4th octet: 11110000 = 240

---

## Example 3: /30 Subnet (Point-to-Point)
192.168.1.0/30
Host bits: 32 - 30 = 2
Addresses: 2^2 = 4
Usable: 2
Subnet mask: 255.255.255.252
Network: 192.168.1.0
First: 192.168.1.1 (Router A)
Second: 192.168.1.2 (Router B)
Broadcast: 192.168.1.3

**Used for:** Direct router-to-router links. Only 2 devices needed.

---

## Powers of 2 (Memorize)
2^1 = 2
2^2 = 4
2^3 = 8
2^4 = 16
2^5 = 32
2^6 = 64
2^7 = 128
2^8 = 256
2^9 = 512
2^10 = 1024

When you see /24, instantly: 32-24=8, 2^8=256.

---

## Dividing a Network into Subnets

**Scenario:** 192.168.0.0/22 needs 4 equal subnets

**Step 1:** Original size
- Host bits: 32 - 22 = 10
- Total: 2^10 = 1024 addresses

**Step 2:** Divide into 4
- 4 = 2^2, so add 2 bits
- New prefix: 22 + 2 = /24
- Each subnet: /24 = 256 addresses

**Subnets:**
1. 192.168.0.0/24
2. 192.168.1.0/24
3. 192.168.2.0/24
4. 192.168.3.0/24

---

## Subnet Mask to CIDR Conversion

Count the 1 bits in binary:
255.255.255.0 = 11111111.11111111.11111111.00000000
8 + 8 + 8 + 0 = 24 ones = /24

255.255.255.240 = 11111111.11111111.11111111.11110000
8 + 8 + 8 + 4 = 28 ones = /28

---

## Security Angle — Network Segmentation

**Yahoo Breach 2013:**
- Flat network (no subnetting)
- Attacker got in via phishing
- No firewalls between subnets
- Reached user database
- Stole 3 billion accounts

**Defense:**
- Subnetting for logical organization
- Firewalls between subnets
- Containment if one subnet compromised
- Database in separate secure subnet

---

## Interview Question Examples

**Q1:** Calculate subnets for 10.0.0.0/22 divided into 8 equal subnets.
- New prefix: 22 + 3 = /25
- Each subnet: 2^7 = 128 addresses

**Q2:** Network needs 50 usable hosts. What prefix?
- 50 usable + 2 (network + broadcast) = 52 total needed
- 2^6 = 64 ≥ 52 ✓
- Host bits = 6
- Prefix = 32 - 6 = /26

**Q3:** What's subnet mask for /27?
- /27 = 27 bits of 1, 5 bits of 0
- First 24 bits: 255.255.255
- Last 3 bits of octet: 11100000 = 224
- Subnet mask: 255.255.255.224
