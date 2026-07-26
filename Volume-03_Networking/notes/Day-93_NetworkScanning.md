# Day 93 — Network Scanning (nmap)

**Volume:** 03 — Networking From Zero
**Topic:** 18 — Network Scanning
**Tool:** nmap (Network Mapper)
**CRITICAL:** Only scan networks and systems you own. Unauthorized scanning is illegal.

---

## What is Network Scanning

Systematic process of probing network to discover:
- Which hosts are alive (host discovery)
- Which ports are open (port scanning)
- What software is running (service detection)
- What operating system is running (OS fingerprinting)

Used by: security engineers (audit own systems), penetration testers (authorized testing), SOC analysts (incident response), attackers (reconnaissance before attack).

---

## nmap Scan Phases

**Phase 1: Host Discovery**

Determines which hosts are alive before port scanning.

Methods:
- ICMP echo request (ping)
- TCP SYN to port 443
- TCP ACK to port 80
- ICMP timestamp

Result: list of live hosts to scan.

**Phase 2: Port Scanning**

Probes ports to determine state: open, closed, filtered.

**SYN scan (default, requires root):**

nmap sends SYN
Open port → SYN-ACK received → nmap sends RST (never completes)
Closed port → RST received
Filtered port → no response or ICMP unreachable


**Connect scan (no root required):**

nmap completes full TCP 3-way handshake
Then immediately closes connection
Slower and more detectable


**Phase 3: Service/Version Detection**

Sends service-specific probes to open ports.
Compares responses against nmap-service-probes database.
Returns exact software name and version.

**Phase 4: OS Detection**

Sends crafted TCP/UDP/ICMP packets.
Analyzes response patterns (window size, TTL, TCP options).
Compares against nmap-os-db with thousands of OS fingerprints.

---

## Port States

| State | Meaning | What nmap received |
|-------|---------|-------------------|
| open | Service running, accepting connections | SYN-ACK |
| closed | No service running | RST |
| filtered | Firewall blocking | No response or ICMP unreachable |

---

## nmap Flag Reference

| Flag | Meaning | Example |
|------|---------|---------|
| -sn | Ping scan only, no port scan | nmap -sn 192.168.1.0/24 |
| -sS | SYN scan (default, root needed) | sudo nmap -sS target |
| -sT | Connect scan (no root needed) | nmap -sT target |
| -sU | UDP scan | sudo nmap -sU target |
| -sV | Service version detection | nmap -sV target |
| -O | OS detection (root needed) | sudo nmap -O target |
| -A | Aggressive (all detection) | sudo nmap -A target |
| -sC | Default NSE scripts | sudo nmap -sC target |
| -p | Specify ports | nmap -p 22,80,443 target |
| -p- | All 65535 ports | nmap -p- target |
| -F | Fast scan (top 100 ports) | nmap -F target |
| -v | Verbose output | nmap -v target |
| -oN | Save normal output to file | nmap -oN file.txt target |
| -oX | Save XML output to file | nmap -oX file.xml target |
| --script | Run NSE script | nmap --script ssh-hostkey target |

---

## Common Scan Commands

**Find live hosts on network:**
```bash
sudo nmap -sn 192.168.1.0/24
```

**Basic scan (top 1000 ports):**
```bash
nmap 192.168.1.100
```

**Full port scan (all 65535):**
```bash
nmap -p- 192.168.1.100
```

**Service version detection:**
```bash
nmap -sV 192.168.1.100
```

**OS detection:**
```bash
sudo nmap -O 192.168.1.100
```

**Aggressive scan (everything):**
```bash
sudo nmap -A 192.168.1.100
```

**Fast scan (top 100 ports):**
```bash
nmap -F 192.168.1.100
```

**Specific ports:**
```bash
nmap -p 22,80,443,3306 192.168.1.100
```

**Save results:**
```bash
nmap -oN results.txt 192.168.1.100
```

**UDP scan common ports:**
```bash
sudo nmap -sU -p 53,67,123,161 192.168.1.100
```

---

## SYN Scan vs Connect Scan

| Aspect | SYN Scan | Connect Scan |
|--------|----------|--------------|
| Root required | Yes | No |
| Speed | Fast | Slower |
| Stealthiness | Stealthier | More detectable |
| Handshake | Never completes | Completes fully |
| Application logs | Often not logged | Often logged |
| Firewall logs | Still logged | Logged |
| Use when | Have root, want stealthy | No root available |

---

## Nmap Scripting Engine (NSE)

Scripts extend nmap capabilities beyond scanning.

Categories:
- **auth** — authentication bypass testing
- **default (-sC)** — safe default scripts for information gathering
- **discovery** — additional information gathering
- **exploit** — actual exploitation (use with extreme caution)
- **safe** — scripts guaranteed not to crash services
- **vuln** — vulnerability detection

Common script examples:
```bash
nmap --script ssh-hostkey target        # SSH host key
nmap --script http-title target         # HTTP page title
nmap --script http-enum target          # Web directory enumeration
nmap --script smtp-vuln* target         # SMTP vulnerability check
nmap --script vuln target               # Run all vuln scripts
```

---

## OS Fingerprinting

Different operating systems have unique network stack behaviors.

nmap analyzes:
- Initial TCP window size
- TTL starting values (Linux: 64, Windows: 128)
- TCP options order
- IP flags and ID values
- Response to malformed packets

Cross-references against nmap-os-db database.

Output:

OS CPE: cpe:/o:linux:linux_kernel:5
OS details: Linux 5.15 - 5.19


---

## Security Perspective

### Attacker Uses Scanning

1. Reconnaissance: find all live hosts on network
2. Port scan: find open ports and services
3. Version detection: identify exact software versions
4. Cross-reference CVE databases for vulnerabilities
5. Target vulnerable service for exploitation

### Defender Uses Scanning

1. Know your own attack surface
2. Find unexpected open ports (possible backdoors)
3. Find outdated software versions needing patching
4. Verify firewall rules are working (blocked ports show as filtered)
5. Discover unauthorized devices on network

### Shodan — Internet-Wide Scanning

Shodan continuously scans entire internet. Attackers use it without running nmap.

Searches:
- `port:3306 mysql` → exposed MySQL databases
- `port:6379 redis` → Redis without authentication
- `port:9200 elasticsearch` → exposed Elasticsearch instances
- `port:27017 mongodb` → exposed MongoDB instances

Your internet-facing IP is indexed in Shodan. Check shodan.io for what is visible about your systems.

---

## Real Incidents

### Target Breach (2013)

Attackers gained initial access via HVAC vendor credentials.

Ran internal network scans:
- Discovered POS systems on same network segment
- Found Windows XP Embedded (unpatched, old OS)
- Found management ports open on POS systems
- Deployed targeted malware based on scan findings

40 million credit cards stolen.

**Lesson:** Defenders who scan first find the same issues before attackers do.

### Shodan Exposed Databases (2017 onwards)

Tens of thousands of databases exposed on internet discovered via Shodan:
- MongoDB instances with no authentication
- Redis instances with no authentication
- Elasticsearch with full data exposed

Multiple data breaches caused by databases found through Shodan searches.

**Lesson:** Know what you expose to internet. Scan from outside your own network regularly.

---

## Volume 03 Complete — Final Checklist

- Topic 01: Network Fundamentals ✓
- Topic 02: OSI Model ✓
- Topic 03: TCP/IP Model ✓
- Topic 04: IP Addressing ✓
- Topic 05: Subnetting ✓
- Topic 06: MAC Addressing ✓
- Topic 07: Network Devices ✓
- Topic 08: Ports and Services ✓
- Topic 09: Transport Protocols ✓
- Topic 10: Application Protocols ✓
- Topic 11: DNS System ✓
- Topic 12: DHCP ✓
- Topic 13: Routing ✓
- Topic 14: Firewall Basics ✓
- Topic 15: VPN Concepts ✓
- Topic 16: Wireless Networking ✓
- Topic 17: Network Monitoring ✓
- Topic 18: Network Scanning ✓

Remaining tasks before Volume 04:
- Wireshark: capture HTTP and DNS traffic, identify 3-way handshake in PCAP
- nmap: scan own VM, document findings
- 20 ports memorized without looking
- Interview prep: all 7 final questions answered
- Hashnode blog published
- Mini project: Python network scanner
- Cyber Dragon: networking module draft