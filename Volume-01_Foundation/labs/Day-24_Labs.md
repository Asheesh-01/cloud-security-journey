# Day 24 — Labs

**Topic:** How the Internet Works
**Environment:** Kali Linux VM

---

## Lab 1 — Full DNS Trace
```bash
dig google.com +trace
```

- `dig` — Domain Information Groper. Advanced DNS query tool.
- `google.com` — the domain to look up.
- `+trace` — forces dig to trace the full resolution path from root servers down to the authoritative nameserver. Shows every step of the DNS hierarchy instead of just the final answer. This is how you see DNS resolution working live.

---

## Lab 2 — Hosts File
```bash
cat /etc/hosts
```

- `cat` — concatenate and print. Reads a file and prints contents to terminal.
- `/etc/hosts` — the local DNS override file. OS checks this before querying any DNS server. Any domain listed here resolves to the specified IP immediately. Malware modifies this file to redirect legitimate domains to malicious IPs.

---

## Lab 3 — Configured DNS Servers
```bash
cat /etc/resolv.conf
```

- `cat` — reads and prints file contents.
- `/etc/resolv.conf` — the file that tells your OS which DNS server to query. The `nameserver` lines show the IP addresses of your configured resolvers. On Kali this often points to 127.0.0.1 (systemd-resolved handles it locally) or directly to your router or ISP's DNS.

---

## Lab 4 — Reverse DNS Lookup
```bash
dig -x 8.8.8.8
nslookup 8.8.8.8
```

- `dig` — DNS query tool.
- `-x` — reverse lookup flag. Instead of domain → IP, this does IP → domain. Queries the PTR record for the given IP address.
- `8.8.8.8` — Google's public DNS server. Its PTR record returns dns.google.
- `nslookup` — Name Server Lookup. Older DNS tool. Same reverse lookup when given an IP instead of a domain name.

---

## Lab 5 — DNS Record Types
```bash
dig google.com A
dig google.com MX
dig google.com NS
dig google.com TXT
```

- `dig google.com A` — queries the A record. Returns the IPv4 address for google.com.
- `dig google.com MX` — queries the MX record. Returns the mail servers responsible for receiving email for google.com. MX records always point to hostnames, never IPs.
- `dig google.com NS` — queries the NS record. Returns the authoritative nameservers for google.com. These are the servers that hold the definitive DNS records.
- `dig google.com TXT` — queries the TXT record. Returns text data — commonly SPF records (which mail servers are allowed to send email for this domain), DKIM keys, and domain ownership verification tokens.

---

## Lab 6 — TTL Values
```bash
dig google.com +noall +answer
```

- `dig google.com` — standard DNS query for google.com.
- `+noall` — suppress all output sections. Clears everything so only what you re-enable shows.
- `+answer` — re-enable only the ANSWER SECTION. Combined with +noall this gives a clean output showing just the final answer with its TTL value. The TTL number (in seconds) shows how long this record will be cached before being re-queried.

---

## Lab 7 — Query Specific Resolvers
```bash
dig @1.1.1.1 google.com
dig @8.8.8.8 google.com
```

- `dig` — DNS query tool.
- `@1.1.1.1` — the @ symbol specifies which DNS server to send this query to directly, bypassing your system's configured resolver. 1.1.1.1 is Cloudflare's public DNS.
- `@8.8.8.8` — sends the same query to Google's public DNS server.
- `google.com` — the domain being queried.
- Comparing results from two different resolvers confirms consistency. If answers differ, one resolver may have a poisoned or stale cache.

---

## Lab 8 — Scan Local Ports
```bash
nmap -sV localhost
```

- `nmap` — Network Mapper. The standard tool for network discovery and port scanning.
- `-sV` — service version detection. After finding open ports, nmap probes each one to identify the service running and its version number. Version information is critical — an old version of SSH or Apache often has known CVEs.
- `localhost` — the loopback address. Scans your own machine (127.0.0.1). This shows every service currently listening on your Kali VM.

---

## Lab 9 — Filter Specific Port
```bash
ss -tlnp | grep :22
ss -tlnp | grep :80
```

- `ss` — Socket Statistics. Shows network socket information.
- `-t` — TCP sockets only.
- `-l` — listening sockets only — services waiting for incoming connections.
- `-n` — numeric output. Shows port numbers instead of service names.
- `-p` — show the process that owns each socket.
- `|` — pipe. Sends ss output to grep as input.
- `grep :22` — filters output to show only lines containing `:22`. Isolates the SSH service entry. grep :80 isolates the HTTP service entry.
- If nothing appears for a port, that service is not listening.

---

## Lab 10 — Hosts File Modification Test
```bash
echo "127.0.0.1 testdomain.local" | sudo tee -a /etc/hosts
ping -c 2 testdomain.local
sudo sed -i '/testdomain.local/d' /etc/hosts
```

- `echo "127.0.0.1 testdomain.local"` — creates the text string to add to the hosts file. Maps testdomain.local to the loopback address.
- `|` — pipe. Passes the echo output to tee.
- `sudo tee -a /etc/hosts` — tee reads from stdin and writes to a file. `-a` means append (add to end, do not overwrite). sudo is required because /etc/hosts is owned by root. This adds the mapping to your hosts file.
- `ping -c 2 testdomain.local` — sends 2 ICMP packets to testdomain.local. Because the hosts file now maps it to 127.0.0.1, it resolves immediately without any DNS query. This proves the hosts file takes priority over DNS.
- `sudo sed -i '/testdomain.local/d' /etc/hosts` — sed is the Stream Editor. `-i` means edit the file in place. `/testdomain.local/d` means delete any line containing "testdomain.local". Removes the test entry cleanly.