# Day 25 — Labs

**Topics:** What is a Network + What is Cybersecurity
**Environment:** Kali Linux VM

---

## Lab 1 — Show Network Interfaces
```bash
ip addr show
ip addr show up
```

- `ip` — primary Linux networking tool replacing ifconfig
- `addr` — address subcommand showing interface configuration
- `show` — display all interfaces
- `show up` — filters to only currently active interfaces. Ignores
  disabled interfaces for a cleaner view of what is actually running.

---

## Lab 2 — Routing Table
```bash
ip route show
```

- `ip route` — routing subcommand
- `show` — display the current routing table
- `default via X.X.X.X` line shows your gateway — the exit point
  to networks outside your local subnet including the internet.

---

## Lab 3 — Hostname and All IPs
```bash
hostname
hostname -I
```

- `hostname` — prints the machine's network name
- `-I` — prints all IP addresses assigned to all interfaces.
  Useful on machines with multiple network interfaces.

---

## Lab 4 — Interface Statistics
```bash
ip -s link show
```

- `-s` — statistics flag. Shows packet counts, errors, and drops
  per interface. High error counts indicate network problems.
  Drops under attack conditions can spike significantly.
- `link` — Layer 2 information including MAC addresses and interface stats.

---

## Lab 5 — ARP Table
```bash
arp -n
ip neigh show
```

- `arp -n` — shows the ARP cache. Maps IP addresses to MAC addresses
  for devices recently communicated with on the local network.
  `-n` shows numeric IPs instead of resolving hostnames.
- `ip neigh show` — modern equivalent. neigh = neighbor table = ARP cache.
  States: REACHABLE (confirmed active), STALE (not recently verified),
  FAILED (unreachable). ARP spoofing attacks modify these mappings.

---

## Lab 6 — Scan Local Network
```bash
nmap -sn 192.168.1.0/24
```

- `nmap` — Network Mapper
- `-sn` — ping scan only. No port scanning. Discovers which hosts
  respond to ICMP echo requests and TCP probes. Shows live hosts only.
- `192.168.1.0/24` — target subnet. Replace with your actual subnet
  (check ip addr show for your IP and use that network range).
- CRITICAL: Only scan networks you own. Never scan without permission.

---

## Lab 7 — Your Attack Surface
```bash
nmap -sV -p- localhost
```

- `nmap` — Network Mapper
- `-sV` — service version detection. Identifies software and version
  on each open port. Version info reveals which CVEs apply.
- `-p-` — scan all 65535 ports. Without this nmap only scans top 1000.
  Complete picture but takes longer.
- `localhost` — your own machine only. Safe target.

---

## Lab 8 — Running Processes
```bash
ps aux
```

- `ps` — process status
- `a` — show processes from all users
- `u` — user-oriented format showing username, CPU%, memory%, command
- `x` — include background processes not attached to a terminal
- Combined: complete view of every process running on the system.

---

## Lab 9 — Authentication Log
```bash
sudo grep "Failed password" /var/log/auth.log | tail -20
sudo grep "Accepted password\|Accepted publickey" /var/log/auth.log | tail -10
```

- `sudo` — required to read auth.log (root-owned file)
- `grep "Failed password"` — finds failed SSH login attempts
- `\|` — OR operator in grep. Matches either pattern.
- `"Accepted password\|Accepted publickey"` — finds successful SSH logins
  whether by password or SSH key authentication.
- `| tail -20` / `| tail -10` — most recent entries only.

---

## Lab 10 — Login History
```bash
who
w
last | head -20
```

- `who` — shows currently logged-in users with terminal and source IP
- `w` — detailed version. Adds idle time, CPU usage, current command.
  Answers: who is here and what are they doing right now?
- `last` — reads /var/log/wtmp. Full login/logout history.
  Shows username, terminal, source IP, login time, session duration.
- `| head -20` — most recent 20 entries only.

---

## Lab 11 — Sudo Usage History
```bash
sudo grep "sudo" /var/log/auth.log | tail -20
```

- `grep "sudo"` — finds all auth.log entries involving sudo usage.
  Every sudo command is logged with: who ran it, from which terminal,
  and what command they executed.
- Security use: unauthorized sudo usage = privilege escalation indicator.
  Any sudo usage by accounts that should not have sudo = finding.