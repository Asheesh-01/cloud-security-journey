# Day 92 — Labs: Wireless Networking and Network Monitoring

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Lab 1 — View Wireless Interfaces

```bash
iwconfig
```

**Command breakdown:**
- `iwconfig` — wireless interface configuration tool similar to ifconfig but for WiFi
- Shows all wireless interfaces, connection mode, ESSID, frequency, signal level

---

## Lab 2 — View Wireless Networks with nmcli

```bash
nmcli dev wifi list
```

**Command breakdown:**
- `nmcli` — NetworkManager command-line interface
- `dev` — device subcommand
- `wifi` — WiFi-specific operations
- `list` — list all available WiFi networks in range

Look at SECURITY column. WEP or blank = serious vulnerability.

---

## Lab 3 — Check WiFi Security Types

```bash
nmcli -f SSID,BSSID,SIGNAL,SECURITY dev wifi list
```

**Command breakdown:**
- `nmcli` — NetworkManager tool
- `-f SSID,BSSID,SIGNAL,SECURITY` — `-f` selects specific fields to display
- `SSID` — network name
- `BSSID` — access point MAC address
- `SIGNAL` — signal strength percentage
- `SECURITY` — encryption type (WPA2, WPA3, WEP, or blank for open)
- `dev wifi list` — list WiFi networks

---

## Lab 4 — Check Active Connection Details

```bash
nmcli connection show --active
```

**Command breakdown:**
- `nmcli connection` — manage network connections
- `show` — display connection details
- `--active` — only show currently active connections (not saved but disconnected)

---

## Lab 5 — Install Wireshark

```bash
sudo apt update
sudo apt install wireshark -y
```

**Command breakdown:**
- `sudo apt update` — refresh package repository list
- `sudo apt install wireshark` — install Wireshark and all dependencies
- `-y` — auto-confirm without prompting

When asked about non-superuser packet capture, select YES.

---

## Lab 6 — Basic tcpdump Capture

```bash
sudo tcpdump -i eth0 -n -c 20
```

**Command breakdown:**
- `sudo` — root required for packet capture
- `tcpdump` — command-line packet capture tool
- `-i eth0` — capture on eth0 interface
- `-n` — numeric output, do not resolve hostnames or port names
- `-c 20` — capture exactly 20 packets then stop automatically

---

## Lab 7 — Capture DNS Traffic Only

```bash
sudo tcpdump -i eth0 -n 'port 53'
```

**Command breakdown:**
- `sudo tcpdump -i eth0 -n` — capture on eth0 with numeric output
- `'port 53'` — BPF filter in single quotes, capture only traffic on port 53 (DNS)

In another terminal trigger DNS:

```bash
dig google.com
```

**Command breakdown:**
- `dig google.com` — DNS query generating traffic on port 53 visible in tcpdump

---

## Lab 8 — Save Capture to PCAP File

```bash
sudo tcpdump -i eth0 -n -w /tmp/capture.pcap
```

**Command breakdown:**
- `sudo tcpdump -i eth0 -n` — capture on eth0 with numeric output
- `-w /tmp/capture.pcap` — write raw packets to file instead of displaying on screen
- `/tmp/capture.pcap` — output file path

Generate traffic in another terminal:

```bash
ping -c 5 8.8.8.8
dig google.com
curl -s https://example.com > /dev/null
```

**Command breakdown:**
- `ping -c 5 8.8.8.8` — 5 ICMP pings generating ICMP traffic
- `dig google.com` — DNS query generating DNS traffic
- `curl -s https://example.com` — HTTPS request generating TLS traffic
- `-s` — silent mode, suppress progress output
- `> /dev/null` — discard HTML output, we only care about captured packets

Press Ctrl+C in tcpdump terminal to stop.

---

## Lab 9 — Read PCAP File

```bash
sudo tcpdump -r /tmp/capture.pcap -n
```

**Command breakdown:**
- `sudo tcpdump` — tcpdump tool
- `-r /tmp/capture.pcap` — read from file instead of live interface
- `-n` — numeric output

---

## Lab 10 — Open PCAP in Wireshark

```bash
wireshark /tmp/capture.pcap
```

**Command breakdown:**
- `wireshark` — launch Wireshark graphical interface
- `/tmp/capture.pcap` — open this specific file directly on launch

In Wireshark, apply display filters by typing in filter bar and pressing Enter:
- Type `dns` → see only DNS packets
- Type `icmp` → see only ping packets
- Type `ip.addr == 8.8.8.8` → see only Google DNS traffic

---

## Lab 11 — Wireshark Display Filters Practice

Open Wireshark with capture file. Apply and observe each filter:

dns

Shows only DNS queries and responses.

icmp

Shows only ICMP (ping) traffic.

tcp.flags.syn == 1


**Filter breakdown:**
- `tcp.flags.syn` — TCP SYN flag field
- `== 1` — equal to 1 means SYN flag is set
- Shows all new TCP connection attempts

ip.addr == 8.8.8.8


**Filter breakdown:**
- `ip.addr` — matches source OR destination IP address
- `== 8.8.8.8` — equal to Google DNS IP

---

## Lab 12 — Capture HTTP Traffic and View Content

```bash
sudo tcpdump -i lo -A 'port 80'
```

**Command breakdown:**
- `sudo tcpdump` — capture with root
- `-i lo` — capture on loopback interface (localhost traffic)
- `-A` — print each packet in ASCII text so content is human-readable
- `'port 80'` — BPF filter for HTTP traffic only

In another terminal:

```bash
curl http://example.com
```

**Command breakdown:**
- `curl` — transfer data from URL
- `http://example.com` — use HTTP (not HTTPS) so traffic is cleartext

Watch tcpdump output show full HTTP headers and HTML body in readable text.

This demonstrates why HTTP is insecure.

---

## Lab 13 — Capture and Filter by IP

```bash
sudo tcpdump -i eth0 -n 'host 8.8.8.8'
```

**Command breakdown:**
- `'host 8.8.8.8'` — BPF filter capturing traffic to OR from 8.8.8.8

In another terminal:

```bash
ping -c 3 8.8.8.8
dig @8.8.8.8 google.com
```

**Command breakdown:**
- `ping -c 3 8.8.8.8` — 3 ICMP pings to Google DNS
- `dig @8.8.8.8 google.com` — DNS query sent specifically to 8.8.8.8

Both appear in tcpdump output filtered to only 8.8.8.8 traffic.

---

## Lab 14 — Detect ARP Traffic

```bash
sudo tcpdump -i eth0 -n 'arp'
```

**Command breakdown:**
- `sudo tcpdump -i eth0 -n` — capture on eth0 numeric
- `'arp'` — BPF filter capturing only ARP packets

In another terminal:

```bash
ping -c 1 192.168.1.1
```

**Command breakdown:**
- `ping -c 1 192.168.1.1` — single ping to gateway, triggers ARP request to find gateway MAC

Watch ARP who-has request and ARP reply appear in tcpdump.

During ARP spoofing attack, multiple ARP replies for same IP from different MACs would appear here.

---

## Lab 15 — tcpdump BPF Filter Cheatsheet Practice

Test each filter, generate matching traffic, verify correct packets captured:

```bash
sudo tcpdump -i eth0 -n 'not port 22'
```

**Command breakdown:**
- `'not port 22'` — capture everything EXCEPT SSH, reduces noise during analysis

```bash
sudo tcpdump -i eth0 -n 'tcp and port 443'
```

**Command breakdown:**
- `'tcp and port 443'` — only TCP traffic on port 443 (HTTPS)
- `and` — logical AND, both conditions must be true

```bash
sudo tcpdump -i eth0 -n 'net 192.168.1.0/24'
```

**Command breakdown:**
- `'net 192.168.1.0/24'` — capture traffic to or from entire 192.168.1.x subnet