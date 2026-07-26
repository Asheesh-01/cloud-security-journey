# Day 93 — Labs: Network Scanning with nmap

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04
**CRITICAL:** Only scan 127.0.0.1 (localhost) or your own VM IP. Never scan external systems without permission.

---

## Lab 1 — Install and Verify nmap

```bash
sudo apt update
sudo apt install nmap -y
nmap --version
```

**Command breakdown:**
- `sudo apt update` — refresh package repository list
- `sudo apt install nmap` — install nmap from repositories
- `-y` — auto-confirm without prompting
- `nmap --version` — verify installation, shows version and build info

---

## Lab 2 — Ping Sweep (Host Discovery)

```bash
sudo nmap -sn 192.168.1.0/24
```

**Command breakdown:**
- `sudo` — root for better host discovery techniques
- `nmap` — network mapper
- `-sn` — scan no ports, ping scan only (finds live hosts without port scanning)
- `192.168.1.0/24` — scan all 254 hosts in this subnet

Identifies which IPs have live devices. First step of any network assessment.

---

## Lab 3 — Basic Port Scan

```bash
nmap 127.0.0.1
```

**Command breakdown:**
- `nmap` — network mapper
- `127.0.0.1` — scan localhost (your own machine)
- No flags = default: top 1000 most common ports, SYN scan if root, connect scan otherwise

Note all open ports found. These are services accessible on your machine.

---

## Lab 4 — Scan Specific Ports

```bash
nmap -p 22 127.0.0.1
nmap -p 22,80,443 127.0.0.1
nmap -p 1-1000 127.0.0.1
```

**Command breakdown:**

**Single port:**
- `-p 22` — scan only port 22 (SSH)

**Multiple ports:**
- `-p 22,80,443` — comma-separated list of specific ports

**Port range:**
- `-p 1-1000` — scan ports 1 through 1000 using dash notation

---

## Lab 5 — Scan All Ports

```bash
nmap -p- 127.0.0.1
```

**Command breakdown:**
- `-p-` — scan all ports from 1 to 65535
- Dash after p with no number means full range
- Takes longer than default 1000-port scan
- Finds services on non-standard ports

---

## Lab 6 — Service Version Detection

```bash
nmap -sV 127.0.0.1
```

**Command breakdown:**
- `nmap` — network mapper
- `-sV` — service version detection
- Sends service-specific probes to each open port
- Compares responses against nmap-service-probes database
- Returns exact software name and version number

Search each detected version against CVE databases to find known vulnerabilities.

---

## Lab 7 — OS Detection

```bash
sudo nmap -O 127.0.0.1
```

**Command breakdown:**
- `sudo` — root required for raw packet crafting
- `nmap` — network mapper
- `-O` — OS detection (capital O not zero)
- Sends crafted packets, analyzes response patterns
- Compares against nmap-os-db

---

## Lab 8 — Aggressive Scan

```bash
sudo nmap -A 127.0.0.1
```

**Command breakdown:**
- `sudo` — root required
- `nmap` — network mapper
- `-A` — aggressive mode combining:
  - `-sV` version detection
  - `-O` OS detection
  - `-sC` default NSE scripts
  - `--traceroute` network path

Most thorough single command. Most noisy. Only on own systems.

---

## Lab 9 — Fast Scan

```bash
nmap -F 127.0.0.1
```

**Command breakdown:**
- `nmap` — network mapper
- `-F` — fast mode, scan only top 100 most common ports
- Much faster than default 1000-port scan
- Good for quick checks when speed matters more than completeness

---

## Lab 10 — Verbose Output

```bash
nmap -v 127.0.0.1
nmap -vv 127.0.0.1
```

**Command breakdown:**

**Single verbose:**
- `-v` — verbose, shows real-time results as ports found, more detail

**Double verbose:**
- `-vv` — extra verbose, maximum detail during scan

Watch ports appear in real time as scan progresses.

---

## Lab 11 — Save Scan Results to File

```bash
nmap -oN /tmp/scan_normal.txt 127.0.0.1
nmap -oX /tmp/scan_xml.xml 127.0.0.1
cat /tmp/scan_normal.txt
```

**Command breakdown:**

**Normal format:**
- `-oN /tmp/scan_normal.txt` — `-o` for output, `N` for Normal format, then file path
- Human-readable text, same as screen output

**XML format:**
- `-oX /tmp/scan_xml.xml` — `X` for XML format
- Machine-readable, importable into other security tools

**View file:**
- `cat /tmp/scan_normal.txt` — display saved results

---

## Lab 12 — Default NSE Scripts

```bash
sudo nmap -sC 127.0.0.1
```

**Command breakdown:**
- `sudo` — root for some scripts
- `nmap` — network mapper
- `-sC` — run default NSE scripts against all detected services
- Default scripts are safe and gather extra information
- SSH: shows host key fingerprints
- HTTP: checks for common misconfigurations and shows page title

---

## Lab 13 — Run Specific NSE Scripts

```bash
nmap --script ssh-hostkey 127.0.0.1
nmap --script http-title 127.0.0.1
```

**Command breakdown:**

**SSH hostkey script:**
- `--script ssh-hostkey` — run this specific named script
- Retrieves and displays SSH server's host key fingerprints
- Used to verify server identity (changed fingerprint = possible MITM)

**HTTP title script:**
- `--script http-title` — run http-title script
- Retrieves HTTP page title from web server
- Quick identification of what web application is running

---

## Lab 14 — UDP Scan

```bash
sudo nmap -sU -p 53,67,123,161 127.0.0.1
```

**Command breakdown:**
- `sudo` — root required for UDP scanning
- `nmap` — network mapper
- `-sU` — UDP scan mode instead of default TCP
- `-p 53,67,123,161` — scan specific UDP ports:
  - 53 = DNS
  - 67 = DHCP server
  - 123 = NTP (time sync)
  - 161 = SNMP (network management)

UDP scanning slower than TCP. Open UDP ports often give no response making state detection harder.

---

## Lab 15 — Complete Security Audit

```bash
sudo nmap -A -p- -oN /tmp/full_audit.txt 127.0.0.1
cat /tmp/full_audit.txt
```

**Command breakdown:**

**Full audit scan:**
- `sudo` — root for all detection capabilities
- `-A` — aggressive: version, OS, scripts, traceroute
- `-p-` — all 65535 ports, leave no port unchecked
- `-oN /tmp/full_audit.txt` — save results for documentation
- `127.0.0.1` — scan own machine only

**Review results:**
- `cat /tmp/full_audit.txt` — display complete audit results

**Document findings:**
1. List all open ports discovered
2. Note software and version for each port
3. Search each version for known CVEs
4. Identify ports that should not be open
5. Write brief security finding for each issue found

This is what a real security audit produces.