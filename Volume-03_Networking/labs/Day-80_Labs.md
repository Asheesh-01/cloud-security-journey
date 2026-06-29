# Day 80 — Labs: OSI Model Drawing and Understanding

**Environment:** Paper, pen, and Linux terminal

---

## Lab 1 — Draw OSI Model by Hand (Mandatory)

Draw on paper:
[   7   Application   ]  HTTP, HTTPS, FTP, SSH, DNS

[   6   Presentation  ]  Encryption, Compression

[   5   Session       ]  Connection Management

[   4   Transport     ]  TCP, UDP, Ports

[   3   Network       ]  IP, Routing

[   2   Data Link     ]  MAC, ARP, Switches

[   1   Physical      ]  Cables, Signals

Repeat 10 times. Memorize without looking.

---

## Lab 2 — Memorize Acronym

Say out loud 20 times:

**"All People Seem To Need Data Processing"**

Layer 7 to Layer 1.

---

## Lab 3 — Identify Protocols at Each Layer

For each layer, name 2 protocols:

Layer 7: HTTP, HTTPS
Layer 6: TLS, JPEG
Layer 5: NetBIOS, RPC
Layer 4: TCP, UDP
Layer 3: IP, ICMP
Layer 2: Ethernet, ARP
Layer 1: Ethernet cables, WiFi

---

## Lab 4 — Trace HTTP Request Through Layers

Open terminal:

```bash
curl -I https://google.com
```

As curl runs, trace through all 7 layers mentally:
- Layer 7: curl sends HTTPS request
- Layer 6: TLS encryption
- Layer 5: Session established
- Layer 4: TCP port 443
- Layer 3: IP routing to Google
- Layer 2: ARP finds gateway MAC
- Layer 1: Signals through cable

---

## Lab 5 — Identify Layer from Attack Description

For each attack, name the OSI layer:

1. Attacker intercepts unencrypted password → Layer 6 (Presentation)
2. Attacker sends fake ARP reply → Layer 2 (Data Link)
3. Attacker injects SQL into web form → Layer 7 (Application)
4. Attacker performs DDoS on port 80 → Layer 4 (Transport)
5. Attacker cuts network cable → Layer 1 (Physical)
6. Attacker hijacks browsing session → Layer 5 (Session)
7. Attacker spoofs source IP address → Layer 3 (Network)

---

## Lab 6 — Draw Encapsulation for Email Send

Draw email data moving down layers 7-1:

Layer 7: Raw email text
→ Layer 6: Add TLS encryption
→ Layer 5: Add session header
→ Layer 4: Add TCP header (port 587)
→ Layer 3: Add IP header (destination 8.8.8.8)
→ Layer 2: Add Ethernet frame (destination MAC)
→ Layer 1: Convert to electrical signals

---

## Lab 7 — Capture Real Network Traffic

```bash
sudo tcpdump -i eth0 -n -c 10
```

- `-i eth0` interface to capture on
- `-n` show IPs not hostnames
- `-c 10` capture 10 packets then stop

Look at output. Identify:
- Layer 3: Source and destination IPs
- Layer 4: Protocol (TCP or UDP) and ports
- Layer 2: Source and destination MACs (if showing)

---

## Lab 8 — Test with Browser DevTools

Open browser, go to google.com, open Developer Tools (F12).

Click Network tab. Refresh page.

For each request, identify:
- Layer 7: HTTP GET request
- Layer 6: HTTPS (encrypted)
- Layer 4: Port 443
- Layer 3: Google's IP