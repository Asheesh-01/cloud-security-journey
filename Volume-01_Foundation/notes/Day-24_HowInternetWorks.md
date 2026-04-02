# Day 24 — How the Internet Works

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 10

---

## IP Addresses

32-bit number. Four octets. Each 0-255.
Private ranges (never routed publicly):
  10.x.x.x / 172.16-31.x.x / 192.168.x.x
Public IP: assigned by ISP. Globally unique.

NAT: router translates private IP to public IP for outbound traffic.
Forensic implication: server logs show public IP only.
Specific device identification requires router NAT table.

IPv6: 128-bit. Hexadecimal. :: compresses zeros.
Security risk: IPv4-only monitoring tools miss IPv6 traffic.
Attackers use IPv6 tunneling to bypass IPv4 firewalls.

---

## DNS Resolution — Step by Step

1. Browser cache check
2. OS cache check
3. /etc/hosts check — malware modifies this for redirection
4. Query recursive resolver (your DNS server)
5. Recursive resolver queries root nameservers
6. Root nameservers direct to TLD nameservers (.com)
7. TLD nameservers direct to authoritative nameservers
8. Authoritative nameservers return the A record (IP address)
9. Recursive resolver caches result, returns to OS
10. OS caches result, browser connects to IP

## DNS Record Types

| Record | Purpose |
|---|---|
| A | Domain → IPv4 address |
| AAAA | Domain → IPv6 address |
| MX | Domain → mail server |
| CNAME | Domain → another domain (alias) |
| TXT | Text data (SPF, DKIM, verification) |
| NS | Authoritative nameservers for domain |
| PTR | IP → domain (reverse lookup) |

## DNS Security Attacks

DNS cache poisoning: attacker injects false records into resolver cache.
Everyone using that resolver gets wrong IP.
DNSSEC prevents this with cryptographic signatures on records.

DNS exfiltration: data encoded as subdomains in DNS queries.
Bypasses firewalls that block direct connections.
Detection: unusually long domain names, high DNS query volume to new domains.

DoH (DNS over HTTPS): encrypts DNS. Privacy benefit, monitoring blind spot.

---

## Packets

Header: source IP, destination IP, protocol, TTL, flags.
Payload: actual data. Encrypted in HTTPS. Readable in HTTP.

TTL in IP packets: starts at 64 or 128. Decremented by each router.
At 0, packet discarded, ICMP Time Exceeded sent back.
Prevents infinite routing loops. traceroute exploits TTL.

TTL in DNS records: how long to cache the DNS answer (seconds).
Different concept, same name.

---

## Ports

16-bit number (0-65535). Identifies service on a device.
Socket = IP + Port + Protocol. Uniquely identifies connection endpoint.

Well-known (0-1023): standard services, root required to listen.
Registered (1024-49151): application-specific.
Ephemeral (49152-65535): client source ports for outgoing connections.

## 20 Critical Ports — Memorize

21 FTP, 22 SSH, 23 Telnet, 25 SMTP, 53 DNS
80 HTTP, 110 POP3, 143 IMAP, 443 HTTPS, 445 SMB
3306 MySQL, 3389 RDP, 8080 HTTP-alt, 8443 HTTPS-alt
27017 MongoDB, 5432 PostgreSQL, 6379 Redis
9200 Elasticsearch, 5601 Kibana, 2375 Docker

Port 4444: Metasploit Meterpreter default. Outbound traffic here = compromise.
Port 445: EternalBlue. Never expose to internet. No exceptions.
Port 3389: RDP. Always behind VPN. Never internet-exposed.

---

## Complete Journey — google.com

1. DNS cache check (browser → OS → hosts file)
2. DNS query UDP port 53 to recursive resolver
3. Recursive resolver finds authoritative answer
4. IP returned, cached with TTL
5. TCP three-way handshake to IP:443 (SYN→SYN-ACK→ACK)
6. TLS handshake, certificate verification, session keys
7. Encrypted HTTP GET request
8. Encrypted HTTP response with HTML
9. Browser renders, makes additional requests

Total: under 200ms on fast connection.
Attack surfaces: DNS (step 2), TCP (step 5), TLS cert (step 6),
payload if HTTP instead of HTTPS (step 7).

---

## Real Breach — EternalBlue / WannaCry 2017

NSA exploit targeting Windows SMB port 445 (CVE-2017-0144).
Remote code execution, no authentication, just network access to port 445.
WannaCry used it to spread automatically — no phishing, no clicks.
24 hours: 230,000 machines, 150 countries. NHS hospitals offline. $4B damage.
Patch MS17-010 was available 1 month earlier.
Lesson: patch management is a security control.

---

