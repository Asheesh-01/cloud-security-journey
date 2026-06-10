# Day 72 — Labs: Windows Firewall + Open Ports

**Environment:** Windows 10 VM inside VirtualBox inside Ubuntu 24.04
**Firewall commands require CMD as Administrator**

---

## Lab 1 — View Firewall Status All Profiles

```cmd
netsh advfirewall show allprofiles
```
- `netsh` — network shell tool
- `advfirewall` — advanced firewall module
- `show allprofiles` — displays state and default behavior for Domain, Private, Public profiles

---

## Lab 2 — View All Firewall Rules

```cmd
netsh advfirewall firewall show rule name=all
```
- `firewall` — firewall rules submodule
- `show rule name=all` — displays every rule currently configured

---

## Lab 3 — View Inbound Rules Only

```cmd
netsh advfirewall firewall show rule name=all dir=in | more
```
- `dir=in` — filter to inbound rules only
- `| more` — shows output one page at a time

---

## Lab 4 — Add Block Rule for Telnet

```cmd
netsh advfirewall firewall add rule name="Block Telnet" protocol=TCP dir=in localport=23 action=block
```
- `add rule` — creates new firewall rule
- `name="Block Telnet"` — label for the rule
- `protocol=TCP` — applies to TCP traffic
- `dir=in` — inbound rule
- `localport=23` — port 23 is Telnet
- `action=block` — drop matching traffic

---

## Lab 5 — Verify Rule Created

```cmd
netsh advfirewall firewall show rule name="Block Telnet"
```
- Confirms rule exists with correct parameters

---

## Lab 6 — Delete Test Rule

```cmd
netsh advfirewall firewall delete rule name="Block Telnet"
```
- Removes test rule. Always clean up after labs.

---

## Lab 7 — View All Connections and Listening Ports

```cmd
netstat -ano
```
- `-a` — all connections and listening ports
- `-n` — show numbers not hostnames
- `-o` — show PID for each entry

---

## Lab 8 — Filter Listening Ports Only

```cmd
netstat -ano | findstr "LISTENING"
```
- Shows every open port waiting for connections
- This is the machine's complete attack surface

---

## Lab 9 — Filter Active Connections

```cmd
netstat -ano | findstr "ESTABLISHED"
```
- Shows every active connection with data flowing right now

---

## Lab 10 — Identify Process Behind a Port

```cmd
netstat -ano | findstr "LISTENING"
tasklist /fi "PID eq [PID from above output]"
```
- Take any PID from netstat output
- Replace [PID from above output] with actual number
- Links suspicious port to the exact program that owns it

---

## Lab 11 — Check if RDP is Open

```cmd
netstat -ano | findstr "LISTENING" | findstr ":3389"
```
- Output present = RDP enabled and listening = attack surface exists
- No output = RDP disabled = good