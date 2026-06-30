# Day 81 — Labs: TCP/IP Model and Comparison

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Lab 1 — Memorize TCP/IP Layers

Say out loud 20 times:

**"Application → Transport → Internet → Network Interface"**

Or acronym: **ATIN** (4 layers, not APSNDN)

---

## Lab 2 — Map OSI to TCP/IP

Draw a chart:
OSI (7 layers)              TCP/IP (4 layers)
───────────────             ────────────────
Application           ┐
Presentation          ├──→  Application
Session               ┘
Transport             ──→  Transport
Network               ──→  Internet
Data Link             ┐
Physical              ├──→  Network Interface

---

## Lab 3 — Identify TCP/IP Layer from Description

For each scenario, name the TCP/IP layer:

1. "Browser sends HTTP request to www.google.com" → Layer 4 (Application)
2. "Packets arrive out of order" → Layer 3 (Transport, UDP property)
3. "Router forwards packet to destination network" → Layer 2 (Internet)
4. "Ethernet frame arrives through cable" → Layer 1 (Network Interface)
5. "SSH authenticates with username and password" → Layer 4 (Application)
6. "ARP replies with MAC address" → Layer 1 (Network Interface)
7. "Port 22 listening for SSH" → Layer 3 (Transport)
8. "ICMP ping test connectivity" → Layer 2 (Internet)

---

## Lab 4 — TCP vs UDP Quiz

For each protocol, say TCP or UDP:

1. Email (SMTP) → TCP (reliable needed)
2. Online gaming → UDP (fast needed)
3. Video streaming → UDP (speed over reliability)
4. SSH terminal → TCP (ordered delivery needed)
5. DNS lookup → UDP (quick, simple query)
6. File download (FTP) → TCP (every byte must arrive)
7. VoIP phone call → UDP (voice acceptable to lose a packet)
8. HTTP website → TCP (page must render correctly)

---

## Lab 5 — TCP 3-Way Handshake

Understand sequence:

1. Client (seq=100) → Server: "SYN"
2. Server (seq=300, ack=101) → Client: "SYN-ACK"
3. Client (seq=101, ack=301) → Server: "ACK"

Connection established.

---

## Lab 6 — Capture TCP/IP in Real Traffic

```bash
sudo tcpdump -i eth0 -n tcp port 22
```

Captures TCP traffic on port 22 (SSH).

In output, identify:
- Layer 4 (Transport): TCP protocol, port numbers
- Layer 2 (Internet): Source and destination IPs
- Layer 1 (Network Interface): MAC addresses (if showing with -e flag)

---

## Lab 7 — Trace SSH Connection Layer-by-Layer

```bash
ssh user@192.168.1.50
```

While connected, in another terminal:

```bash
ss -tulnp | grep :22
```

Shows SSH listening on Layer 3 (Transport):
- Protocol: tcp
- Port: 22
- State: LISTEN

---

## Lab 8 — Test UDP vs TCP

**Test TCP connection:**
```bash
nc -zv 8.8.8.8 22
```

- `-z` scan mode
- `-v` verbose
- Tests if port 22 reachable
- UDP equivalent:

```bash
nc -zvu 8.8.8.8 53
```

- `-u` UDP mode
- Port 53 (DNS)

---

## Lab 9 — Draw TCP/IP Model by Hand

Draw 4 layers, top to bottom:
Layer 4 — Application (HTTP, SSH, DNS)
Layer 3 — Transport (TCP, UDP, ports)
Layer 2 — Internet (IP, routing)
Layer 1 — Network Interface (MAC, Ethernet)

With one protocol example at each layer.