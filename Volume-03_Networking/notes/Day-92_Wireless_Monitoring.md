# Day 92 — Wireless Networking and Network Monitoring

**Volume:** 03 — Networking From Zero
**Topics:** 16 — Wireless Networking + 17 — Network Monitoring
**Focus:** WiFi security standards, WPS vulnerability, packet capture, Wireshark analysis.

---

## Wireless Networking

### What It Is

WiFi uses radio waves to transmit network data without physical cables.

Attack surface: anyone within radio range can attempt to intercept or inject traffic. Attack surface extends through walls and into parking lots.

---

## WiFi Standards (802.11)

| Standard | Year | Max Speed | Band | Security |
|----------|------|-----------|------|----------|
| 802.11a | 1999 | 54 Mbps | 5 GHz | WEP |
| 802.11b | 1999 | 11 Mbps | 2.4 GHz | WEP |
| 802.11g | 2003 | 54 Mbps | 2.4 GHz | WPA |
| 802.11n | 2009 | 600 Mbps | 2.4+5 GHz | WPA2 |
| 802.11ac | 2013 | 3.5 Gbps | 5 GHz | WPA2 |
| 802.11ax (WiFi 6) | 2019 | 9.6 Gbps | 2.4+5 GHz | WPA3 |

2.4 GHz = better range, more interference, slower.
5 GHz = shorter range, less interference, faster.

---

## WiFi Security Standards

### WEP (Wired Equivalent Privacy)

Completely broken. Crackable in minutes with aircrack-ng.

Do not use under any circumstances. If you see WEP network, treat it as open.

### WPA (WiFi Protected Access)

First replacement for WEP. Also largely broken.

Uses TKIP encryption. TKIP has known vulnerabilities.

Do not use.

### WPA2 (WiFi Protected Access 2)

Current standard. Uses AES-CCMP encryption.

Secure when strong password used.

Vulnerability: 4-way handshake can be captured and subjected to offline dictionary attack.

If WiFi password is weak, attacker captures handshake and cracks offline without being on network.

### WPA3 (WiFi Protected Access 3)

Newest standard (2019).

Uses SAE (Simultaneous Authentication of Equals) instead of PSK.

Protects against offline dictionary attacks even if handshake captured.

Each authentication uses unique session key (forward secrecy).

Use WPA3 when devices support it.

---

## WPA2 4-Way Handshake

When device joins WPA2 network:

Device Access Point
| |
|←── Packet 1: ANonce ────────────| AP sends random number (ANonce)
|──── Packet 2: SNonce, MIC ──────→| Device sends its random + MIC (Message Integrity Code)
|←── Packet 3: GTK, MIC ──────────| AP sends Group Temporal Key
|──── Packet 4: ACK ──────────────→| Device confirms
Connection established


PTK (Pairwise Transient Key) derived from: PSK + ANonce + SNonce + MACs.

Attacker captures Packets 1+2 = has enough to attempt offline password cracking.

Strong password = cracking infeasible. Weak password (dictionary word) = crackable.

---

## SSID

Service Set Identifier. The name displayed when scanning for WiFi networks.

SSID hiding provides no real security:
- SSID still broadcast in probe requests and responses
- Discoverable with monitoring tools in seconds
- Only inconveniences legitimate users

Use strong encryption and strong password. Do not rely on hidden SSID.

---

## WPS (WiFi Protected Setup)

Feature for easy device connection using 8-digit PIN or physical button press.

**PIN Vulnerability (CVE-2011-5053):**

WPS PIN is 8 digits. Mathematically: 10^8 = 100,000,000 combinations.

But protocol validates first 4 digits before checking last 4 digits.

Real combinations needed:
- First half: 10^4 = 10,000 combinations
- Second half: 10^4 = 10,000 combinations
- Total: 20,000 combinations maximum

Brute forceable in hours using reaver or bully.

**Recommendation: Always disable WPS.**

---

## Wireless Attacks

### Evil Twin Attack

Attacker sets up rogue access point with identical SSID as legitimate network.

Legitimate AP SSID: "CompanyCorp-WiFi"
Attacker AP SSID: "CompanyCorp-WiFi" (same name, different BSSID)

Attacker may use deauthentication attack to force devices off legitimate AP.

Devices reconnect, some connect to evil twin instead.

Attacker performs MITM on all traffic from connected devices.

**Detection:** Multiple BSSIDs (MAC addresses) for same SSID = evil twin likely.

### Deauthentication Attack

802.11 management frames (including deauth) are not authenticated.

Attacker sends fake deauthentication frames to victim device.

Device disconnects from legitimate AP.

Device tries to reconnect → handshake captured by attacker.

**Defense:** 802.11w (Management Frame Protection) authenticates management frames. WPA3 requires this.

---

## Network Monitoring

### What It Does

Captures all packets flowing through network. Records traffic for analysis. Detects attacks, investigates incidents, establishes baselines.

### Packet Capture Modes

**Promiscuous mode (wired):**
- NIC accepts ALL packets on network segment
- Not just packets addressed to this machine
- Default for tcpdump and Wireshark on wired

**Monitor mode (wireless):**
- Wireless NIC captures ALL wireless frames
- Including frames not addressed to this device
- Required for wireless traffic analysis

---

## tcpdump

Command-line packet capture tool.

Best for: capturing on remote servers via SSH, quick captures, automation.

### Common Commands

Capture all traffic on interface:
```bash
sudo tcpdump -i eth0 -n
```

Capture specific protocol/port:
```bash
sudo tcpdump -i eth0 -n 'port 53'
```

Save to PCAP file:
```bash
sudo tcpdump -i eth0 -n -w capture.pcap
```

Read PCAP file:
```bash
sudo tcpdump -r capture.pcap -n
```

Show packet content in ASCII:
```bash
sudo tcpdump -i eth0 -A 'port 80'
```

### BPF Filter Syntax

host 192.168.1.100 → traffic to/from this IP
src host 192.168.1.100 → traffic FROM this IP
dst host 192.168.1.100 → traffic TO this IP
port 80 → traffic on port 80
src port 54321 → traffic FROM this port
dst port 443 → traffic TO port 443
not port 22 → exclude SSH
net 192.168.1.0/24 → entire subnet
tcp → TCP only
udp → UDP only
arp → ARP only
icmp → ICMP only
port 80 or port 443 → HTTP or HTTPS


---

## Wireshark

GUI packet capture and analysis tool.

Best for: deep packet analysis, visual inspection, protocol decoding, following streams.

### Display Filter Examples

http → all HTTP traffic
dns → all DNS traffic
arp → ARP traffic (detect spoofing)
icmp → ping traffic
tcp.flags.syn == 1 → all SYN packets
tcp.flags.syn == 1 and tcp.flags.ack == 0 → SYN flood detection
ip.addr == 192.168.1.100 → all traffic to/from IP
ip.src == 192.168.1.100 → traffic FROM IP
ip.dst == 192.168.1.100 → traffic TO IP
tcp.port == 22 → SSH traffic
http.request.method == "POST" → HTTP POST requests
frame contains "password" → packets containing string


### Analysis Techniques

**Follow TCP Stream:**
Right-click packet → Follow → TCP Stream
Shows complete client-server conversation in readable format.

**Export Objects:**
File → Export Objects → HTTP
Extracts files transferred over HTTP (malware extraction).

**Statistics → Conversations:**
Shows all IP pairs communicating and bandwidth used.
Identifies top talkers (potential data exfiltration).

**Statistics → Protocol Hierarchy:**
Shows breakdown of all protocols in capture.
Unusual protocol = investigate.

---

## tcpdump vs Wireshark

| Aspect | tcpdump | Wireshark |
|--------|---------|-----------|
| Interface | Command-line | Graphical |
| Best for | Remote servers, quick captures | Deep analysis, visual inspection |
| Resource usage | Low | High |
| Remote use | Yes (SSH) | Limited |
| Filter syntax | BPF | Display filters + BPF |
| Use case | Production server capture | Analysis workstation |

Workflow: tcpdump captures on remote server → transfer PCAP → Wireshark analyzes locally.

---

## C2 Beaconing Detection

Malware checks in with C2 server at regular intervals (every 60 seconds typical).

Pattern in Wireshark: filter to suspicious IP, see regular connections.

12:00:00 → 185.220.101.1:4444
12:01:00 → 185.220.101.1:4444 (exactly 60 seconds)
12:02:00 → 185.220.101.1:4444 (exactly 60 seconds)


Regular automated interval = malware beacon. Human traffic is irregular.

tcpdump detection:
```bash
sudo tcpdump -i eth0 host 185.220.101.1
```

---

## Real Incidents

### TJX Companies Breach (2005-2007)

Attacker in parking lot used directional antenna to connect to store WiFi.

Store used WEP encryption. Cracked in minutes.

No network segmentation between WiFi and payment systems.

No monitoring to detect unusual traffic.

94 million credit cards stolen over 18 months.

**Defense:** WPA2/WPA3, network segmentation, network monitoring.

### Capital One Breach (2019)

Misconfigured WAF allowed SSRF to AWS metadata service.

Exfiltrated 100 million customer records.

Detected via third-party tip, not internal monitoring.

Monitoring existed but did not alert on large-scale data exfiltration.

**Defense:** Properly configured monitoring with actionable alerts on data exfiltration patterns.