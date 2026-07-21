# Day 91 — Labs: Firewall and VPN Hands-On

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Lab 1 — View Current Firewall Rules (iptables)

```bash
sudo iptables -L -n -v
```

**Command breakdown:**
- `sudo` — root required for firewall inspection
- `iptables` — Linux firewall tool
- `-L` — list all rules in all chains (INPUT, OUTPUT, FORWARD)
- `-n` — numeric output, show IPs and ports as numbers not names
- `-v` — verbose, show packet and byte counts and interface names

Look for default policy on each chain (ACCEPT or DROP).

---

## Lab 2 — View Firewall Rules by Chain

```bash
sudo iptables -L INPUT -n -v
sudo iptables -L OUTPUT -n -v
sudo iptables -L FORWARD -n -v
```

**Command breakdown:**
- `sudo iptables` — firewall tool with root
- `-L INPUT` — list only INPUT chain (traffic coming TO this machine)
- `-L OUTPUT` — list only OUTPUT chain (traffic LEAVING this machine)
- `-L FORWARD` — list only FORWARD chain (traffic passing through)
- `-n` — numeric
- `-v` — verbose with packet counts

---

## Lab 3 — Check ufw Status

```bash
sudo ufw status verbose
```

**Command breakdown:**
- `sudo` — root required
- `ufw` — Uncomplicated Firewall, simplified iptables frontend
- `status` — show current status and all active rules
- `verbose` — full detail including default policies for incoming and outgoing

---

## Lab 4 — Enable ufw and Set Default Policies

```bash
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw status
```

**Command breakdown:**
- `sudo ufw enable` — activate firewall, starts enforcing rules immediately
- `sudo ufw default deny incoming` — block all inbound traffic unless rule explicitly allows
- `sudo ufw default allow outgoing` — allow all outbound traffic unless rule explicitly blocks
- `sudo ufw status` — verify firewall is active with correct default policies

---

## Lab 5 — Allow Specific Ports (Inbound Rules)

```bash
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw status
```

**Command breakdown:**
- `sudo ufw allow` — add allow rule
- `22/tcp` — port 22 using TCP protocol (SSH)
- `80/tcp` — port 80 using TCP protocol (HTTP)
- `443/tcp` — port 443 using TCP protocol (HTTPS)
- `sudo ufw status` — verify rules added correctly

---

## Lab 6 — Block Specific Ports

```bash
sudo ufw deny 23/tcp
sudo ufw deny 21/tcp
sudo ufw status
```

**Command breakdown:**
- `sudo ufw deny` — add deny rule, drops matching packets
- `23/tcp` — block Telnet (unencrypted, should never be allowed)
- `21/tcp` — block FTP (unencrypted credentials, use SFTP instead)
- `sudo ufw status` — verify block rules added

---

## Lab 7 — Allow Traffic from Specific IP Only

```bash
sudo ufw allow from 192.168.1.100 to any port 22
sudo ufw status
```

**Command breakdown:**
- `sudo ufw allow` — allow rule
- `from 192.168.1.100` — only from this specific source IP address
- `to any` — to any destination IP on this machine
- `port 22` — only for port 22 (SSH)

Result: Only 192.168.1.100 can SSH. All other IPs blocked on port 22.

---

## Lab 8 — Block Specific IP (Attacker Blocking)

```bash
sudo ufw deny from 203.45.67.89
sudo ufw status
```

**Command breakdown:**
- `sudo ufw deny` — deny rule
- `from 203.45.67.89` — block ALL traffic from this specific IP regardless of port or protocol

Used during incident response when attacker IP identified.

---

## Lab 9 — View Rules with Line Numbers

```bash
sudo ufw status numbered
```

**Command breakdown:**
- `sudo ufw status` — show current rules
- `numbered` — show rule number next to each entry

Line numbers needed for deleting specific rules in next lab.

---

## Lab 10 — Delete a Specific Rule

```bash
sudo ufw status numbered
sudo ufw delete [rule_number]
sudo ufw status
```

**Command breakdown:**
- `sudo ufw status numbered` — show rules with numbers
- `sudo ufw delete [rule_number]` — delete rule at that number (replace with actual number)
- `sudo ufw status` — verify rule removed

---

## Lab 11 — Test Port Before and After Firewall Rule

```bash
nc -zv 127.0.0.1 23
sudo ufw deny 23/tcp
nc -zv 127.0.0.1 23
sudo ufw delete [rule_number_for_deny_23]
```

**Command breakdown:**

**nc -zv 127.0.0.1 23:**
- `nc` — netcat
- `-z` — scan mode, test connectivity without sending data
- `-v` — verbose output showing success or failure
- `127.0.0.1` — localhost
- `23` — Telnet port to test

**sudo ufw deny 23/tcp:**
- Add block rule for port 23

**Second nc test:**
- Should now show connection refused or timeout (firewall blocking)

**Delete rule:**
- Clean up test rule using line number from numbered status

---

## Lab 12 — Build Complete Web Server Firewall Policy

```bash
sudo ufw reset
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 443/tcp
sudo ufw allow from 10.0.0.5 to any port 22
sudo ufw deny 3306/tcp
sudo ufw deny 5432/tcp
sudo ufw enable
sudo ufw status verbose
```

**Command breakdown:**
- `sudo ufw reset` — remove all existing rules, start clean
- `default deny incoming` — block all inbound by default
- `default allow outgoing` — allow all outbound by default
- `allow 443/tcp` — HTTPS from anywhere
- `allow from 10.0.0.5 to any port 22` — SSH only from admin IP
- `deny 3306/tcp` — explicitly block MySQL
- `deny 5432/tcp` — explicitly block PostgreSQL
- `enable` — activate with these rules
- `status verbose` — review complete policy

This is production-grade firewall configuration for a web server.

---

## Lab 13 — Check VPN Tools Available

```bash
which openvpn
which wg
openvpn --version 2>/dev/null || echo "OpenVPN not installed"
wg --version 2>/dev/null || echo "WireGuard not installed"
```

**Command breakdown:**
- `which openvpn` — find if OpenVPN installed and show path
- `which wg` — find if WireGuard tool installed
- `openvpn --version` — show version if installed
- `2>/dev/null` — redirect error messages to /dev/null (suppress errors if not installed)
- `||` — OR operator, if previous command fails run next command
- `echo "not installed"` — print message if tool not found

---

## Lab 14 — Install VPN Tools

```bash
sudo apt update
sudo apt install openvpn wireguard -y
openvpn --version
wg --version
```

**Command breakdown:**
- `sudo apt update` — refresh package list from repositories
- `sudo apt install openvpn wireguard` — install both OpenVPN and WireGuard packages
- `-y` — auto-confirm, do not prompt for yes/no
- `openvpn --version` — verify OpenVPN installed correctly
- `wg --version` — verify WireGuard installed correctly