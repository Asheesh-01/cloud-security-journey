# Day 83 — Labs: Subnetting Calculation

**Environment:** Paper, pen, and brain

---

## Lab 1 — Calculate /24 Subnet (Warmup)
192.168.1.0/24
Calculate:

Host bits: ___
Total addresses: ___
Usable hosts: ___
Subnet mask: ___
Network: ___
First host: ___
Last host: ___
Broadcast: ___

Answers:

Host bits: 8
Total: 256
Usable: 254
Subnet mask: 255.255.255.0
Network: 192.168.1.0
First: 192.168.1.1
Last: 192.168.1.254
Broadcast: 192.168.1.255


---

## Lab 2 — Calculate /28 Subnet (Interview Difficulty)
192.168.1.0/28
Calculate:

Host bits: ___
Total addresses: ___
Usable hosts: ___
Subnet mask: ___
Network: ___
First host: ___
Last host: ___
Broadcast: ___

Answers:

Host bits: 4
Total: 16
Usable: 14
Subnet mask: 255.255.255.240
Network: 192.168.1.0
First: 192.168.1.1
Last: 192.168.1.14
Broadcast: 192.168.1.15


---

## Lab 3 — Calculate /30 Subnet (Point-to-Point)
192.168.1.0/30
Calculate:

Host bits: ___
Total addresses: ___
Usable hosts: ___
Subnet mask: ___
Network: ___
First router: ___
Second router: ___
Broadcast: ___

Answers:

Host bits: 2
Total: 4
Usable: 2
Subnet mask: 255.255.255.252
Network: 192.168.1.0
First: 192.168.1.1
Second: 192.168.1.2
Broadcast: 192.168.1.3


---

## Lab 4 — Convert Subnet Mask to CIDR

For each mask, convert to /XX:

1. 255.255.255.0 → /24 (8+8+8+0 = 24)
2. 255.255.255.240 → /28 (8+8+8+4 = 28)
3. 255.255.255.252 → /30 (8+8+8+6 = 30)
4. 255.255.0.0 → /16 (8+8+0+0 = 16)
5. 255.255.255.128 → /25 (8+8+8+1 = 25)

---

## Lab 5 — Divide Network into Subnets

**Problem:** 192.168.0.0/22 needs 4 equal subnets.

**Solution:**
- Original: /22
- Need: 4 = 2^2 subnets
- New prefix: 22 + 2 = /24
- Each subnet size: /24 = 256 addresses

Subnets:
1. 192.168.0.0/24 (Network: .0, Broadcast: .255)
2. 192.168.1.0/24 (Network: 256, Broadcast: 511)
3. 192.168.2.0/24 (Network: 512, Broadcast: 767)
4. 192.168.3.0/24 (Network: 768, Broadcast: 1023)

---

## Lab 6 — Reverse Engineering

**Problem:** "I need a subnet with exactly 30 usable hosts."

**Solution:**
- 30 usable + 2 (network + broadcast) = 32 needed
- 2^5 = 32 ✓
- Host bits = 5
- Prefix = 32 - 5 = /27
- Subnet mask: 255.255.255.224

---

## Lab 7 — Real Interview Question

**Question:** Your company network is 10.0.0.0/16 (65,536 addresses). Divide into:
- 10 department subnets (256 addresses each)
- Each needs /24

Calculate first 3 subnets:

1. 10.0.0.0/24 (Network: 10.0.0.0, Broadcast: 10.0.0.255)
2. 10.0.1.0/24 (Network: 10.0.1.0, Broadcast: 10.0.1.255)
3. 10.0.2.0/24 (Network: 10.0.2.0, Broadcast: 10.0.2.255)

Remaining 7 subnets: 10.0.3.0 through 10.0.9.0 (/24)

---

## Lab 8 — Speed Test

Calculate 10 subnets in 5 minutes (without notes):

1. 192.168.1.0/24 → usable: ___
2. 192.168.1.0/28 → usable: ___
3. 192.168.1.0/30 → usable: ___
4. 10.0.0.0/25 → usable: ___
5. 10.0.0.0/27 → usable: ___
6. 172.16.0.0/16 → usable: ___
7. 172.16.0.0/24 → usable: ___
8. 192.168.0.0/22 → usable: ___
9. 10.0.0.0/22 → usable: ___
10. 192.168.0.0/25 → usable: ___

Answers:
1. 254, 2. 14, 3. 2, 4. 126, 5. 30, 6. 65534, 7. 254, 8. 1022, 9. 1022, 10. 126

---

## Lab 9 — Draw Subnet Network Diagram

Draw on paper:
Company Network: 10.0.0.0/16
Subnet 1: 10.0.1.0/24 (Department A, 254 devices)
Subnet 2: 10.0.2.0/24 (Department B, 254 devices)
Subnet 3: 10.0.3.0/24 (Department C, 254 devices)
All subnets connected through router.
Firewall between subnets.
Database server in isolated subnet 10.0.10.0/24.

---

## Lab 10 — Practice Until Instant

Do Lab 1-3 until you calculate without thinking.

Target: <30 seconds per /24, <1 minute per /28, <1 minute per /30.

