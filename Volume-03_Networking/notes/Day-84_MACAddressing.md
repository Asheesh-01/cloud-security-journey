# Day 84 — MAC Addressing and ARP

**Volume:** 03 — Networking From Zero
**Topic:** 6 — MAC Addressing
**Layer:** Layer 2 (Data Link)

---

## MAC Address Format

48-bit hardware address: AA:BB:CC:DD:EE:FF

First 24 bits (AA:BB:CC) = OUI (Organizationally Unique Identifier) = Manufacturer
Last 24 bits (DD:EE:FF) = Device-specific identifier

Example: 08:00:27:1F:5E:6B
- 08:00:27 = Intel/VirtualBox (OUI)
- 1F:5E:6B = This specific VM

---

## IP vs MAC Comparison

| Aspect | IP Address | MAC Address |
|--------|------------|-------------|
| What | Network address (logical) | Hardware address (physical) |
| Layer | Layer 3 (Network) | Layer 2 (Data Link) |
| Routing | Across networks | Same network only |
| Example | 192.168.1.100 | AA:BB:CC:DD:EE:FF |
| Assignment | DHCP or manual | Burned at manufacture |
| Change | Can change | Permanent (almost) |
| Scope | Global (internet) | Local (network segment) |

---

## How Devices Find Each Other on Same Network

**Scenario:** Your computer (192.168.1.100) pings 192.168.1.50

**Step 1: Check ARP Cache**
- Do I know 192.168.1.50's MAC?
- Check local ARP table
- Not found

**Step 2: Send ARP Request (Broadcast)**
- "Who has 192.168.1.50? Tell 192.168.1.100"
- Sent to broadcast MAC: FF:FF:FF:FF:FF:FF
- All devices on network receive

**Step 3: Target Responds**
- 192.168.1.50 hears request
- Sends unicast ARP reply: "I have 192.168.1.50, my MAC is BB:BB:BB:BB:BB:BB"
- Sent only to your MAC

**Step 4: Cache and Send**
- Your ARP table updated: 192.168.1.50 → BB:BB:BB:BB:BB:BB
- Send ping packet with destination MAC: BB:BB:BB:BB:BB:BB
- Switch forwards to that MAC port
- Device 192.168.1.50 receives

---

## ARP Table Structure
IP Address          MAC Address           TTL (seconds)
192.168.1.1         AA:BB:CC:DD:EE:FF     120
192.168.1.50        BB:BB:BB:BB:BB:BB     120
192.168.1.100       CC:CC:CC:CC:CC:CC     120

TTL = how long entry stays before deletion (dynamic cleanup)

---

## ARP Spoofing Attack (Man-in-the-Middle)

### Normal Network Flow
Your computer (192.168.1.100)
↓
Router (192.168.1.1)
↓
Internet

### After ARP Spoofing

Attacker (192.168.1.105) sends fake ARP replies:

**Fake ARP #1 (to your computer):**
- "I have 192.168.1.1"
- "My MAC is AA:AA:AA:AA:AA:AA" (attacker's MAC)

Your ARP table becomes:
192.168.1.1 → AA:AA:AA:AA:AA:AA (wrong, should be router's MAC)

**Fake ARP #2 (to router):**
- "I have 192.168.1.100"
- "My MAC is AA:AA:AA:AA:AA:AA" (attacker's MAC)

Router ARP table becomes:
192.168.1.100 → AA:AA:AA:AA:AA:AA (wrong, should be your MAC)

### Result: MITM
Your computer (192.168.1.100)
↓ (thinks this is router)
Attacker (192.168.1.105) ← in the middle
↓ (forwards to real router)
Router (192.168.1.1)
↓
Internet

Attacker can:
- Read all traffic (if unencrypted)
- Modify packets before forwarding
- Drop packets (DoS)
- Perform SSL strip (remove HTTPS encryption)

---

## Detecting ARP Spoofing

**Command:**
```bash
arp -a
```

**Output:**
192.168.1.1 at AA:BB:CC:DD:EE:FF [ether] on eth0
192.168.1.50 at BB:BB:BB:BB:BB:BB [ether] on eth0
192.168.1.105 at AA:AA:AA:AA:AA:AA [ether] on eth0

**Red Flag:** Same MAC for different IPs
192.168.1.1 at AA:AA:AA:AA:AA:AA [ether] on eth0
192.168.1.105 at AA:AA:AA:AA:AA:AA [ether] on eth0

Two IPs with same MAC = ARP spoofing

**Response:**
1. Identify attacker IP: 192.168.1.105
2. Block at switch or firewall
3. Alert security team
4. Investigate what data was intercepted

---

## Defense Against ARP Spoofing

**1. Static ARP Entries (Small Networks)**
- Manually configure gateway: 192.168.1.1 = AA:BB:CC:DD:EE:FF
- Cannot be spoofed
- Impractical for large networks

**2. ARP Monitoring**
- Tools: arpwatch, Snort IDS
- Alert on unusual ARP activity
- Detect rapid changes

**3. Network Segmentation**
- Divide into subnets
- Firewalls between subnets
- Attacker can only spoof within their subnet

**4. HTTPS Encryption**
- Even if MITM intercepts traffic, encrypted
- Cannot read credentials or data
- Standard defense today

**5. VPN**
- Encrypt all traffic end-to-end
- MITM cannot read encrypted tunnel
- Recommended for public WiFi

---

## Security Angle — Real Incident

**Hotel WiFi MITM Attack (2013)**

Scenario:
1. Attacker and victim both on hotel WiFi
2. Attacker runs ARP spoofing tool
3. Victim's device thinks attacker is gateway
4. Victim visits gmail.com, logs in (old unencrypted login page)
5. Attacker intercepts username and password
6. Attacker logs into victim's email
7. Victim's email linked to banking account
8. Attacker resets banking password
9. Accesses victim's bank account
10. Transfers money

**Root cause:** Unencrypted email login page (no HTTPS)

**Fix:** Google enforced HTTPS on all login pages. Now even if MITM intercepts, traffic encrypted.

---

## Commands

**Check your MAC:**
```bash
ip link show
ip addr show
```

**View ARP table:**
```bash
arp -a
ip neigh
```

**Add static ARP entry (requires sudo):**
```bash
sudo arp -s 192.168.1.1 AA:BB:CC:DD:EE:FF
```

**Delete ARP entry:**
```bash
sudo arp -d 192.168.1.50
```

**Monitor ARP changes:**
```bash
arp -a > before.txt
# Wait or perform action
arp -a > after.txt
diff before.txt after.txt
```

---

## Interview Questions

**Q1:** Explain how ARP spoofing enables man-in-the-middle attack. What would attacker do step-by-step?

**A1:** 
1. Attacker sends fake ARP reply to victim: "I am the gateway (192.168.1.1), my MAC is mine"
2. Attacker sends fake ARP reply to gateway: "I am the victim (192.168.1.100), my MAC is mine"
3. Victim's traffic destined for internet now goes to attacker
4. Attacker forwards to real gateway (victim doesn't know they're intercepted)
5. Response from internet comes to attacker, attacker forwards to victim
6. Attacker is in the middle, can read/modify/drop traffic

**Q2:** You detect two IPs with same MAC in your ARP table. What is happening and what do you do?

**A2:**
- ARP spoofing is happening
- Two devices claiming same MAC (attacker and legitimate device)
- Likely MITM attack in progress
- Immediate actions:
  1. Identify attacker IP
  2. Block at switch/firewall
  3. Investigate what data was intercepted
  4. Alert security team
  5. Check logs for exfiltration