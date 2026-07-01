# Day 82 — Labs: IP Addressing Hands-On

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Lab 1 — Check Your IP

```bash
ip addr
```

Look for:
- `inet 192.168.x.x/24` = your IP and subnet
- `link/ether AA:BB:CC:DD:EE:FF` = your MAC address
- Note broadcast address (brd)

---

## Lab 2 — Check Default Gateway

```bash
ip route
```

Output: `default via 192.168.1.1 dev eth0`

Gateway is 192.168.1.1 (your router).

---

## Lab 3 — Understand Your Subnet

From your IP 192.168.1.100/24:

- Network: 192.168.1.0
- Usable hosts: 192.168.1.1 to 192.168.1.254
- Broadcast: 192.168.1.255
- Subnet mask: 255.255.255.0
- Total hosts: 256
- Usable: 254

---

## Lab 4 — Identify IP Class

For each IP, name the class and first octet range:

1. 10.0.0.1 → Class A (1-126)
2. 172.16.0.1 → Class B (128-191)
3. 192.168.1.1 → Class C (192-223)
4. 224.0.0.1 → Class D (224-239)
5. 8.8.8.8 → Class A (1-126)

---

## Lab 5 — Identify Private vs Public

For each IP, say Private or Public:

1. 10.5.5.5 → Private
2. 8.8.8.8 → Public
3. 172.20.0.1 → Private
4. 203.45.67.89 → Public
5. 192.168.100.50 → Private
6. 1.1.1.1 → Public

---

## Lab 6 — Calculate Subnet Information

For network 192.168.1.0/24:

- Network address: 192.168.1.0
- First usable: 192.168.1.1
- Last usable: 192.168.1.254
- Broadcast: 192.168.1.255
- Total hosts: 256
- Usable hosts: 254
- Subnet mask: 255.255.255.0

---

## Lab 7 — Calculate Different Subnet

For network 10.0.0.0/28:

/28 = 32 - 28 = 4 bits for host
2^4 = 16 addresses

- Network: 10.0.0.0
- First usable: 10.0.0.1
- Last usable: 10.0.0.14
- Broadcast: 10.0.0.15
- Total: 16
- Usable: 14
- Subnet mask: 255.255.255.240

---

## Lab 8 — Check DHCP Configuration

```bash
cat /etc/netplan/00-installer-config.yaml
```

Shows if your IP is static or dynamic (dhcp4: yes/no).

---

## Lab 9 — List All Network Interfaces

```bash
ifconfig
```

(Legacy, but shows all interfaces)

Or modern:

```bash
ip link show
```

---

## Lab 10 — Ping Gateway

```bash
ping -c 4 192.168.1.1
```

Tests connectivity to your gateway (router).

Should get 4 responses with latency in milliseconds.