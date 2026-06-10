# Day 72 — Windows Firewall Rules + Checking Open Ports

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 8 — Windows Networking Basics
**Subtopics:** 2 — Windows Firewall Rules | 3 — Checking Open Ports

---

## Subtopic 2 — Windows Firewall Rules

### What It Is

Windows Firewall is a gatekeeper between your machine and the network. Every incoming and outgoing connection is checked against a ruleset. Match = allow or block. No match = default behavior. Default inbound = block. Default outbound = allow.

### Key Terms

**Inbound rule** — controls traffic coming INTO your machine. Someone connecting to your port.
**Outbound rule** — controls traffic going OUT from your machine to the network.
**Port** — numbered door on your machine. Each service listens on a specific port number.
**Protocol** — TCP (reliable, connection-based) or UDP (fast, no guarantee).
**Profile** — three profiles: Domain (company network), Private (home), Public (untrusted). Rules apply per profile.
**Action** — Allow, Block, or Block with notification.
**netsh advfirewall** — command-line tool for all firewall operations on Windows.

### How It Works

Rule processing order:
1. Check all rules for match — port, protocol, program, profile
2. Block rule matches = traffic dropped immediately
3. Allow rule matches = traffic passes
4. No match = default behavior (inbound block, outbound allow)

Attackers abuse outbound default allow — malware calls home freely because outbound is open.

### Commands

```cmd
netsh advfirewall show allprofiles
```
Shows firewall state and default behavior for all three profiles.

```cmd
netsh advfirewall firewall show rule name=all
```
Shows every firewall rule on the machine.

```cmd
netsh advfirewall firewall show rule name=all dir=in | more
```
Shows inbound rules only, one page at a time.

```cmd
netsh advfirewall firewall add rule name="Block Telnet" protocol=TCP dir=in localport=23 action=block
```
Creates inbound block rule for port 23 (Telnet).

```cmd
netsh advfirewall firewall show rule name="Block Telnet"
```
Verifies the rule was created correctly.

```cmd
netsh advfirewall firewall delete rule name="Block Telnet"
```
Removes the test rule. Always clean up.

### Security Angle — WannaCry 2017

WannaCry infected 200,000 machines in 150 countries in one day via port 445 (SMB) left open inbound. Machines with port 445 blocked at firewall were completely immune. Organizations without that rule lost entire networks to ransomware.

SOC analysts audit inbound rules for: port 23 (Telnet), 445 (SMB), 3389 (RDP) open to 0.0.0.0 = critical finding. Also check for recently added allow rules = possible attacker persistence.

---

## Subtopic 3 — Checking Open Ports

### What It Is

Every service listening for network connections opens a port and waits. Checking open ports reveals the complete attack surface of a machine. First thing attackers check after gaining access. First thing defenders check during hardening and investigation.

### Key Terms

**Listening** — port is open, service waiting for incoming connections. Attack surface.
**Established** — active connection, data flowing between two machines.
**PID (Process ID)** — unique number assigned to every process. Links port to program.
**netstat** — Network Statistics — shows all connections and listening ports.
**0.0.0.0** — service listening on ALL interfaces. Reachable from any network. High exposure.
**127.0.0.1** — service listening on loopback only. Not reachable from network. Safe.

### How It Works

When a program wants to receive connections it registers a port with the OS. The OS routes all incoming traffic on that port to that program. `netstat -ano` asks the OS for the complete table of all registered ports and active connections including the PID behind each one.

### Commands

```cmd
netstat -ano
```
Shows all connections and listening ports with PIDs.
- `-a` shows all connections and listening ports
- `-n` shows numbers not hostnames
- `-o` shows PID for each connection

```cmd
netstat -ano | findstr "LISTENING"
```
Shows only listening ports — complete attack surface of the machine.

```cmd
netstat -ano | findstr "ESTABLISHED"
```
Shows only active connections — data flowing right now.

```cmd
netstat -ano | findstr ":443"
```
Filters connections involving a specific port.

```cmd
tasklist /fi "PID eq 4"
```
Identifies which process owns a specific PID from netstat output.
Replace 4 with actual PID. Links suspicious port to specific program.

```cmd
netstat -ano | findstr "LISTENING" | findstr ":3389"
```
Checks if RDP is listening. Output present = RDP open = attack surface.

### Security Angle — SolarWinds 2020

SUNBURST malware communicated over HTTPS port 443 — blending with normal web traffic. netstat alone would not catch it. Full investigation combines: netstat (connections) + ipconfig /displaydns (domains contacted) + tasklist (process behind connection). Individual commands miss what the combination catches.

Metasploit reverse shells default to port 4444 outbound. Established connection to unknown IP on port 4444 + PID tracing to process in C:\Users\Public = malware identification in under 3 minutes.