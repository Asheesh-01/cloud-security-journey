# Day 71 — Labs: IP Configuration on Windows

**Environment:** Windows 10 VM inside VirtualBox inside Ubuntu 24.04
**Commands run in normal CMD**

---

## Lab 1 — Basic IP Configuration

```cmd
ipconfig
```
- `ipconfig` — reads all network adapter settings and displays them
- Shows adapter name, IPv4 address, subnet mask, default gateway

---

## Lab 2 — Full Configuration with MAC and DNS

```cmd
ipconfig /all
```
- `/all` — shows complete configuration
- Adds MAC address, DHCP enabled or not, DNS server addresses, DHCP lease times
- Use this during any security investigation

---

## Lab 3 — View DNS Cache

```cmd
ipconfig /displaydns
```
- `/displaydns` — shows every domain recently resolved and the IP returned
- Security use: reveals every domain machine contacted recently including malware C2 domains

---

## Lab 4 — Flush DNS Cache

```cmd
ipconfig /flushdns
```
- `/flushdns` — clears entire DNS cache
- Forces fresh DNS lookup for every domain going forward
- Used when DNS cache is poisoned or contains stale entries

---

## Lab 5 — View ARP Table

```cmd
arp -a
```
- `arp` — Address Resolution Protocol tool
- `-a` — display all current entries in the ARP table
- Shows every IP recently communicated with and its mapped MAC address
- Gateway MAC address changed from previous = ARP spoofing indicator