# Day 71 — Windows Networking Basics — IP Configuration

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 8 — Windows Networking Basics
**Subtopic:** 1 — IP Configuration on Windows

---

## What It Is

Every machine on a network needs an address so other machines can find it. `ipconfig` shows everything about how your Windows machine is connected — IP address, gateway, DNS server, MAC address. First command an attacker or analyst runs on a Windows machine to understand where it sits in the network.

---

## Key Terms

**IP address** — unique number assigned to your machine on a network. Format: four numbers separated by dots — `192.168.1.5`. Range 0-255 per number.

**Subnet mask** — defines which part of IP is the network and which is the device. `255.255.255.0` = first three numbers are network, last number is your machine.

**Default gateway** — IP address of your router. All traffic going outside your network goes here first.

**DNS server** — translates domain names like `google.com` into IP addresses. Machine asks this server every time you type a domain.

**DHCP** — service that automatically assigns IP addresses when machines join a network. Most networks use this instead of manual IP assignment.

**MAC address** — permanent hardware address burned into network card during manufacturing. Format: six hex pairs — `A4-BB-6D-12-34-56`. Fixed unlike IP addresses.

**Network adapter** — hardware that connects machine to network. Each adapter gets its own IP and MAC.

**Loopback address** — `127.0.0.1` — always refers to your own machine. Used for local services and testing.

---

## How It Works

1. Machine joins network → sends DHCP request
2. DHCP server (router) responds with IP, subnet mask, gateway, DNS server
3. Windows stores this in the network adapter configuration
4. `ipconfig` reads and displays that configuration

Each adapter (Wi-Fi, ethernet) has its own separate IP configuration.

---

## Commands

```cmd
ipconfig
```
Shows adapter name, IPv4 address, subnet mask, default gateway for each adapter.

```cmd
ipconfig /all
```
Shows everything — adds MAC address, DHCP status, DNS servers, lease times. Use this during investigation, not plain ipconfig.

```cmd
ipconfig /displaydns
```
Shows DNS cache — every domain recently resolved and the IP returned. Malware C2 domains appear here.

```cmd
ipconfig /flushdns
```
Clears DNS cache. Forces fresh DNS lookup for every domain. Used when cache is poisoned or stale.

```cmd
arp -a
```
Shows ARP table — every IP recently communicated with and its mapped MAC address. Unexpected MAC for gateway IP = possible ARP spoofing.

---

## Security Angle — Target Breach 2013

Attackers entered via third-party HVAC vendor credentials. Ran network reconnaissance to map layout. Found POS systems on the same flat network as vendor access — no segmentation. Moved laterally directly to POS systems. Installed card-stealing malware. 40 million credit cards stolen.

**What failed:** No network segmentation. Vendor access and POS systems on same subnet. ipconfig on any internal machine revealed the entire network layout.

**SOC analyst uses IP config to:**
- Identify subnet — determine blast radius if machine is compromised
- Check DNS server — unexpected IP = possible DNS hijack
- Check ARP table — changed gateway MAC = possible ARP spoofing
- Check DNS cache — recently resolved domains = possible C2 beaconing domains