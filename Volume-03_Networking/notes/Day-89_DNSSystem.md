# Day 89 — DNS System (Deep Dive)

**Volume:** 03 — Networking From Zero
**Topic:** 11 — DNS System
**Port:** 53 (UDP for queries, TCP for zone transfers)
**Focus:** Hierarchy, record types, resolution process, cache poisoning, DNSSEC.

---

## What is DNS

Domain Name System. Distributed database translating human-readable domain names to machine-readable IP addresses.

Analogy: Phonebook of the internet. You know the name (google.com), DNS finds the number (142.250.195.46).

---

## DNS Hierarchy (Top to Bottom)
Root (.)
↓
TLD (.com, .org, .net, .in)
↓
Domain (google.com, amazon.com)
↓
Subdomain (mail.google.com, www.google.com)

**Root nameservers:** 13 clusters worldwide (A through M). Know locations of all TLD nameservers.

**TLD nameservers:** One set per TLD. .com nameservers know authoritative nameservers for each .com domain.

**Authoritative nameservers:** Final authority for specific domain. Google's nameservers know exact IPs for all google.com subdomains.

**DNS Resolver:** Your ISP's server or Google 8.8.8.8. Walks the hierarchy on your behalf. Returns final answer.

---

## DNS Resolution Process (Step by Step)

When you type mail.google.com:

**Step 1:** Browser checks local DNS cache → empty → asks OS.

**Step 2:** OS checks its DNS cache → empty → asks configured resolver (8.8.8.8).

**Step 3:** Resolver checks its cache → empty → starts recursive lookup.

**Step 4:** Resolver asks root nameserver → "I don't know mail.google.com. Ask .com TLD nameservers."

**Step 5:** Resolver asks .com TLD nameserver → "I don't know exact IP. Ask Google's nameservers: ns1.google.com, ns2.google.com."

**Step 6:** Resolver asks Google's authoritative nameserver → "mail.google.com A record: 142.250.195.174"

**Step 7:** Resolver caches answer with TTL, responds to OS, OS caches, browser caches.

**Step 8:** Browser opens TCP connection to 142.250.195.174:443. DNS resolution complete.

**Total time:** 50-200ms for full recursive lookup. Near-instant if cached.

---

## DNS Record Types

| Record | Purpose | Example |
|--------|---------|---------|
| A | Maps domain to IPv4 address | google.com → 142.250.195.46 |
| AAAA | Maps domain to IPv6 address | google.com → 2404:6800:4009::200e |
| MX | Mail exchange server | google.com → smtp.google.com (priority 10) |
| CNAME | Alias to another domain | www.google.com → google.com |
| TXT | Text record (SPF, DKIM, DMARC) | v=spf1 include:_spf.google.com ~all |
| NS | Authoritative nameservers for domain | google.com → ns1.google.com |
| PTR | Reverse DNS (IP to domain) | 8.8.8.8 → dns.google |

---

## Record Detail Examples

### A Record
google.com.    300    IN    A    142.250.195.46

- `google.com.` = domain (trailing dot = absolute)
- `300` = TTL in seconds (expires after 5 minutes, resolver must re-query)
- `IN` = Internet class
- `A` = record type
- `142.250.195.46` = IPv4 address

---

### MX Record
google.com.    3600    IN    MX    10    smtp.google.com.
google.com.    3600    IN    MX    20    alt1.aspmx.l.google.com.

- `10` = priority (lower = higher priority)
- `smtp.google.com.` = mail server hostname
- Multiple MX records = backup mail servers (failover)

---

### CNAME Record
www.google.com.    300    IN    CNAME    google.com.

www.google.com is an alias for google.com.

Browser resolves CNAME → resolves final A record.

Used for: www aliases, CDN routing, load balancing.

---

### TXT Record (Email Security)
google.com.    3600    IN    TXT    "v=spf1 include:_spf.google.com ~all"

- **SPF** — Sender Policy Framework. Lists authorized mail servers for domain.
- **DKIM** — DomainKeys Identified Mail. Cryptographic signature verifying email authenticity.
- **DMARC** — Domain-based Message Authentication. Policy for handling emails failing SPF/DKIM.

---

### PTR Record (Reverse DNS)
46.195.250.142.in-addr.arpa.    300    IN    PTR    google.com.

Note: IP written in reverse for PTR records.

Used for: email server verification, security tool output, log analysis.

---

## DNS Caching and TTL

**TTL (Time To Live):** How long DNS answer stays cached before expiring.

- Short TTL (300 seconds / 5 min): Changes take effect quickly. More DNS queries.
- Long TTL (86400 seconds / 24 hours): Fewer queries. Changes take longer to propagate.

**Cache locations:**
1. Browser cache (first checked)
2. OS cache (second checked)
3. Resolver cache (third checked)
4. Full recursive lookup if all caches miss

---

## DNS Cache Poisoning Attack

### Attack Concept

Attacker injects fake DNS records into resolver's cache. All users querying that resolver get attacker's fake IP.

### Attack Flow

1. Attacker queries resolver for bank.com
2. Resolver starts recursive lookup
3. Attacker floods resolver with fake responses: "bank.com is 192.168.1.100" (attacker IP)
4. If fake response arrives before real response and transaction ID matches, resolver caches fake IP
5. All users asking for bank.com get 192.168.1.100 (attacker's phishing server)
6. Users see perfect fake bank site, enter credentials
7. Attacker steals credentials
8. Fake entry stays cached until TTL expires

### Why Old DNS Was Vulnerable

Original DNS (1983) had 16-bit transaction ID (only 65,536 possibilities). Attacker could guess correct ID quickly. Once guessed, fake response accepted.

### Defense: DNSSEC

DNS Security Extensions add cryptographic signatures to records.
bank.com.    300    IN    A         1.2.3.4
bank.com.    300    IN    RRSIG     A [cryptographic signature]

Resolver verifies signature using nameserver's public key.

Fake response from attacker fails signature check → rejected.

### Defense: Source Port Randomization

Added after Kaminsky attack (2008). Resolver uses random source port for each query.

Attacker must guess: 16-bit transaction ID + 16-bit source port = billions of combinations.

---

## Real Incidents

### Kaminsky Attack (2008)

Security researcher Dan Kaminsky discovered critical DNS vulnerability.

Attacker could poison DNS cache in seconds by flooding resolver with fake responses for subdomains.

Before patch: 65,536 transaction ID combinations easily guessable.

After emergency global patch: source port randomization added (billions of combinations).

**Lesson:** DNS was designed without security in 1983. DNSSEC is the fix.

### Brazilian Banking DNS Hijack (2017)

Attackers compromised major Brazilian bank's domain registrar account (no MFA).

Changed NS records to attacker-controlled nameservers.

Result:
- All 36 million customers redirected to phishing sites
- Bank's email hijacked
- Malware served to users visiting bank website
- Lasted 5-6 hours before discovered

**Root cause:** Domain registrar account without MFA. One compromised account = full DNS control.

**Defense:** MFA on registrar accounts, DNSSEC, registry lock (prevents unauthorized NS changes).

---

## Security Checklist for DNS

- Enable DNSSEC on your domains
- Add MFA on domain registrar account
- Enable registry lock on critical domains
- Monitor NS records for unauthorized changes
- Use short TTL for critical records (faster change propagation)
- Check DNS logs for unusual queries (data exfiltration via DNS)
- Implement DNS filtering (block known malicious domains)