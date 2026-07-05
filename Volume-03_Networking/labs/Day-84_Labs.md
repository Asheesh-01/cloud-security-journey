# Day 84 — Labs: MAC Addressing and ARP

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Lab 1 — View Your MAC Address

```bash
ip link show
```

Look for `link/ether AA:BB:CC:DD:EE:FF`. That's your MAC.

Also try:
```bash
ip addr show
```

Identify your MAC and note it down.

---

## Lab 2 — View ARP Table

```bash
arp -a
```

Shows all IP-to-MAC mappings your computer has cached.

Modern version:
```bash
ip neigh
```

Identify:
- Your gateway IP and its MAC
- Other devices on network and their MACs
- TTL (time to live) of entries

---

## Lab 3 — Understand Your Network

From your ARP table:
- How many devices are on your network?
- What is your gateway IP?
- What is your gateway's MAC?

Write these down.

---

## Lab 4 — Ping a Device and Watch ARP Work

```bash
arp -a | grep 192.168.1.1
```

Note if gateway is in table (cached entry).

Now ping gateway:
```bash
ping -c 1 192.168.1.1
```

Check ARP table again:
```bash
arp -a | grep 192.168.1.1
```

Gateway should still be there (cached).

---

## Lab 5 — Clear ARP Cache and Watch Rebuild

```bash
sudo ip neigh flush all
```

Clears all ARP entries.

View empty table:
```bash
ip neigh
```

Now ping gateway:
```bash
ping -c 1 192.168.1.1
```

View ARP table:
```bash
ip neigh
```

Gateway entry reappeared (ARP learned it again).

---

## Lab 6 — Identify ARP Request (Wireshark)

Install Wireshark (if not present):
```bash
sudo apt update
sudo apt install wireshark -y
```

Start packet capture:
```bash
sudo wireshark
```

In Wireshark:
1. Select your network interface (eth0)
2. Start capture
3. In terminal, ping unknown IP:
```bash
ping -c 1 192.168.1.200
```
4. In Wireshark, look for "ARP" packets
5. Find ARP Request: "Who has 192.168.1.200?"
6. Find ARP Reply: "I have it, my MAC is..."

---

## Lab 7 — Monitor ARP Changes (Detect Spoofing)

```bash
arp -a > baseline.txt
cat baseline.txt
```

Wait 10 seconds:
```bash
sleep 10
arp -a > current.txt
diff baseline.txt current.txt
```

If no changes (normal). If changes detected, someone on network sending ARP packets.

---

## Lab 8 — Manually Add Static ARP Entry

```bash
sudo arp -s 192.168.1.100 AA:BB:CC:DD:EE:FF
arp -a | grep 192.168.1.100
```

Entry now "permanent" (not auto-deleted).

This is how static ARP prevents spoofing (only works for known devices).

---

## Lab 9 — Delete ARP Entry

```bash
arp -a | grep 192.168.1.1
```

Note gateway's current cached MAC.

Delete entry:
```bash
sudo arp -d 192.168.1.1
```

Verify deleted:
```bash
arp -a | grep 192.168.1.1
```

Should be gone.

Ping gateway to re-learn:
```bash
ping -c 1 192.168.1.1
```

Entry reappears.

---

## Lab 10 — Simulate ARP Spoofing Scenario (Educational Only)

**Note:** Only run on your own VMs. Never on networks you don't own.

**Scenario:** Two VMs on VirtualBox network.
- VM1 (your computer): 192.168.1.100
- VM2 (attacker): 192.168.1.105

On VM2, send fake ARP:
```bash
sudo arping -c 5 -w 100 -I eth0 -S 192.168.1.1 192.168.1.100
```

This sends ARP spoofing replies claiming to be 192.168.1.1.

On VM1, check ARP table:
```bash
arp -a | grep 192.168.1.1
```

You'll see 192.168.1.1's MAC changed to VM2's MAC (spoofing worked).

**Mitigation:**
```bash
sudo arp -s 192.168.1.1 [REAL_GATEWAY_MAC]
```

Now static entry prevents spoofing.

---

## Lab 11 — Interview Question Practice

**Q:** "You notice ARP table showing two different IPs with same MAC. Explain what is happening and your response."

**A:** Write out full answer with:
1. What is happening (ARP spoofing)
2. Why it matters (MITM attack)
3. Immediate response (block, investigate)
4. Long-term fix (HTTPS, VPN, segmentation)

---

## Lab 12 — Network Diagram

Draw on paper:

Your Network: 192.168.1.0/24
Devices:

192.168.1.1 (Gateway/Router) - MAC: AA:BB:CC:DD:EE:FF
192.168.1.100 (Your Computer) - MAC: 11:22:33:44:55:66
192.168.1.50 (Server) - MAC: 99:88:77:66:55:44
192.168.1.105 (Attacker) - MAC: FF:FF:FF:FF:FF:FF

Normal flow: Your computer → Router → Internet
After spoofing: Your computer → Attacker → Router → Internet

Label each device with IP and MAC.