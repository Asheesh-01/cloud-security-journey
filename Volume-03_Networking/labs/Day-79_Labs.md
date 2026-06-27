# Day 79 — Labs: Network Fundamentals Visualization

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Lab 1 — Understand Your Network

```bash
ip route
```

- Shows your default gateway and network routes
- First line shows: `default via 192.168.1.1` = your gateway
- See how packets reach different destinations

---

## Lab 2 — Understand Your Local Network

```bash
hostname -I
```

- Shows your local IP address
- Example: 192.168.1.100
- This is your address on the local network

---

## Lab 3 — See Network Information

```bash
ifconfig
```

- Shows all network interfaces
- IP address, MAC address, netmask, broadcast
- On Kali, displays eth0 (Ethernet) or wlan0 (WiFi)

---

## Lab 4 — Trace Route to External Server

```bash
traceroute google.com
```

- Shows every hop your packet takes to reach Google
- Displays routers between you and destination
- Shows latency at each hop
- Physical path of data through internet

---

## Lab 5 — Measure Latency

```bash
ping -c 4 8.8.8.8
```

- Sends 4 packets to Google DNS (8.8.8.8)
- Shows latency for each packet
- "time=25ms" = 25 milliseconds (typical for internet)
- Loss = packets that did not return (network problem indicator)

---

## Lab 6 — DNS Lookup

```bash
nslookup google.com
```

- Asks DNS server: "What IP is google.com?"
- Returns: 142.250.195.46 (or similar Google IP)
- Shows which DNS server answered query

---

## Lab 7 — Find Local Gateway MAC Address

```bash
arp -a
```

- Shows ARP table (IP to MAC mappings)
- Find gateway IP (192.168.1.1 or similar)
- Note its MAC address
- This is the machine your computer sends traffic to for external networks

---

## Lab 8 — Diagram Your Network

On paper, draw:
- Your computer (192.168.1.100)
- Your router/gateway (192.168.1.1)
- Internet cloud
- Google's servers (142.250.195.46)
- Arrow from your computer to router to internet to Google

This is packet flow for google.com request.