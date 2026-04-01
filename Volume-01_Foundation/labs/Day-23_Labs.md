# Day 23 — Labs

**Topics:** What is Programming + What is the Internet
**Environment:** Kali Linux VM

---

## Lab 1 — Check IP Address

**Commands:**
```bash
ip addr show
ifconfig 2>/dev/null || ip addr
```

**What each part does:**
- `ip` — the primary Linux networking tool. Replaced ifconfig on modern systems.
- `addr` — address subcommand. Shows network interface addresses.
- `show` — display the information.
- `ifconfig` — older tool. Still works on Kali. 2>/dev/null suppresses error
  if not installed. `||` means run the right side only if left side fails.

**What to look for:**
- `lo` — loopback interface. Always 127.0.0.1. The machine talking to itself.
- `eth0` or `ens33` — your main network interface. This has your IP address.
- `inet` — IPv4 address line. Your IP and subnet mask in CIDR notation.

**Paste output here.**

---

## Lab 2 — Check Routing Table

**Command:**
```bash
ip route show
```

**What each part does:**
- `ip route` — the routing subcommand
- `show` — display current routing table

**What to look for:**
- `default via X.X.X.X` — your default gateway. Every packet not on your
  local network goes here first. This is your router.
- The gateway IP is what your machine uses to reach the internet.

**Paste output here.**

---

## Lab 3 — Test Connectivity with Ping

**Command:**
```bash
ping -c 4 8.8.8.8
```

**What each part does:**
- `ping` — sends ICMP Echo Request packets and waits for ICMP Echo Reply.
- `-c 4` — count. Send exactly 4 packets then stop. Without -c, ping runs forever.
- `8.8.8.8` — Google's public DNS server. A reliable target that is always up.

**Output fields:**
- `bytes from` — reply received, connectivity confirmed
- `icmp_seq` — sequence number. Gaps = lost packets.
- `ttl` — Time to Live. Number of router hops the packet can make.
  Starts at 64 or 128. Each router decrements by 1. Prevents infinite loops.
- `time` — round-trip latency in milliseconds.
- Final line: packets transmitted, received, loss percentage, total time.

**Paste output here.**

---

## Lab 4 — Trace the Route

**Commands:**
```bash
traceroute 8.8.8.8
traceroute google.com
```

**What each part does:**
- `traceroute` — sends packets with increasing TTL values (1, 2, 3...).
  Each router decrements TTL. When TTL reaches 0, router sends back an
  ICMP Time Exceeded message, revealing its IP address.
  This exposes every hop between you and the destination.
- Each line = one router hop. Three columns = three timing measurements.
- `* * *` = router did not respond (blocked ICMP). Common on enterprise routers.

**Security relevance:**
Every hop between you and a destination is a potential interception point.
This is why unencrypted traffic (HTTP, Telnet, FTP) is dangerous — any
router in this chain can read the payload.

**Paste output here.**

---

## Lab 5 — DNS Lookups

**Commands:**
```bash
nslookup google.com
dig google.com
dig google.com MX
```

**What each part does:**
- `nslookup` — Name Server Lookup. Basic DNS query tool.
  Shows which DNS server answered and what IP it returned.
- `dig` — Domain Information Groper. More detailed DNS query.
- `dig google.com` — queries the A record (IPv4 address) for google.com
- `dig google.com MX` — queries the MX record (mail server) for google.com
- `dig google.com A` — explicitly requests the A (address) record type

**Dig output sections:**
- `QUESTION SECTION` — what was asked
- `ANSWER SECTION` — the DNS answer
- `Query time` — how long the DNS lookup took in milliseconds
- `SERVER` — which DNS server answered your query

**Paste output here.**

---

## Lab 6 — Check Listening Ports

**Command:**
```bash
ss -tlnp
```

**What each part does:**
- `ss` — Socket Statistics. Modern replacement for netstat.
- `-t` — show TCP sockets only
- `-l` — show only listening sockets (servers waiting for connections)
- `-n` — numeric. Show port numbers instead of service names.
  Without -n: shows "ssh" instead of "22". With -n: shows "22".
- `-p` — show which process owns each socket. Requires root for full info.

**Output columns:**
- State: LISTEN = waiting for connections
- Recv-Q / Send-Q: data queued to receive/send (normally 0)
- Local Address:Port — the IP and port the service is listening on
- 0.0.0.0:22 means SSH is listening on all interfaces on port 22
- 127.0.0.1:631 means listening only on localhost — not reachable externally

**Security relevance:** this is your machine's attack surface.
Every listening port is a service an attacker could target.
Unexpected listening ports = potential backdoor or malware.

**Paste output here.**

---

## Lab 7 — Full HTTP Request

**Command:**
```bash
curl -v https://example.com 2>&1 | head -40
```

**What each part does:**
- `curl` — Client URL. Makes HTTP/HTTPS requests from command line.
- `-v` — verbose. Shows every detail of the request and response.
- `https://example.com` — the URL to request
- `2>&1` — redirect stderr (stream 2) to stdout (stream 1).
  curl -v sends verbose output to stderr. This combines both streams.
- `| head -40` — show first 40 lines only

**Verbose output shows:**
- `* Trying` — DNS resolved, attempting TCP connection
- `* Connected` — TCP handshake complete
- `* SSL connection` — TLS handshake complete
- `> GET / HTTP/2` — the HTTP request sent to the server
- `< HTTP/2 200` — the HTTP response status from the server
- Headers sent and received

**Paste output here.**

---

## Lab 8 — Active Connections

**Command:**
```bash
ss -tnp
```

**What each part does:**
- Same as Lab 6 but without `-l` — shows ESTABLISHED connections too
- `-t` — TCP only
- `-n` — numeric ports
- `-p` — process info

**What to look for:**
- ESTABLISHED connections = active data transfers right now
- The remote IP and port tells you what your machine is communicating with
- Unexpected ESTABLISHED connections to unknown IPs = investigation trigger

**Paste output here.**

---

## Lab 9 — Public IP

**Commands:**
```bash
curl ifconfig.me
curl ipinfo.io
```

**What each part does:**
- Both services return information about the IP address making the request
- `ifconfig.me` — returns just your public IP as plain text
- `ipinfo.io` — returns JSON with IP, city, region, ISP, coordinates

**Security relevance:**
Your public IP is what the internet sees. This is what shows up in
server access logs when you connect to websites. When your machine
connects to a malicious server, this IP appears in their logs.
Understanding the difference between your private IP (from ip addr)
and public IP (from ifconfig.me) is fundamental to network investigation.

**Paste output here.**

---

## Lab Summary

| Lab | Commands | Concept Demonstrated |
|-----|----------|---------------------|
| 1 | ip addr show | Your IP address and network interfaces |
| 2 | ip route show | Default gateway = internet exit point |
| 3 | ping -c 4 | Connectivity test, latency measurement |
| 4 | traceroute | Every router hop to destination |
| 5 | nslookup, dig | DNS resolution in detail |
| 6 | ss -tlnp | Your machine's listening ports = attack surface |
| 7 | curl -v | Full HTTP request and response visible |
| 8 | ss -tnp | Active connections = what your machine talks to now |
| 9 | curl ifconfig.me | Your public IP vs private IP difference |