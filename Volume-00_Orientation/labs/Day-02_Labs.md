# Day 02 — Labs

**Topic:** Cloud Ecosystem & Service Models
**Environment:** Kali Linux VM

---

## Lab 1 — DNS lookup of all three cloud providers

**Commands:**
```bash
host aws.amazon.com
host azure.microsoft.com
host cloud.google.com
```

**What these commands do:**
host is a DNS lookup tool. It resolves a domain name to
its actual IP address by querying your DNS server.
You are asking — where does this domain physically live?

**What the output means:**
Each line shows one or more IP addresses for that provider.
Multiple IP addresses mean the provider uses load balancing —
your request gets routed to the nearest or fastest server.
This is the scale of infrastructure you will be securing.

**Screenshot:** saved in artifacts/Day-02/

---

## Lab 2 — Detailed DNS query with dig

**Commands:**
```bash
dig aws.amazon.com
dig azure.microsoft.com
dig cloud.google.com
```

**What dig does:**
dig is a more powerful DNS tool than host. It shows the full
DNS response including query time, which DNS server answered,
TTL values, and all IP addresses returned.

**What the output means:**
ANSWER SECTION — the IP addresses for the domain
Query time — how fast your DNS server responded
SERVER — which DNS server answered your query
TTL — how long this DNS record is cached

**Screenshot:** saved in artifacts/Day-02/

---

## Lab 3 — Short DNS lookup for comparison

**Commands:**
```bash
dig +short aws.amazon.com
dig +short azure.microsoft.com
dig +short cloud.google.com
```

**What +short does:**
Returns only the IP addresses with no extra information.
Clean and fast. Useful when you only need the IP.

**What the output means:**
Notice AWS returns more IP addresses than others.
This shows AWS has more distributed global infrastructure.
More IPs = more redundancy = harder to take down with DDoS.

**Screenshot:** saved in artifacts/Day-02/

---

## Lab 4 — Fetch AWS IP ranges

**Command:**
```bash
curl -s https://ip-ranges.amazonaws.com/ip-ranges.json \
| head -50
```

**What this command does:**
curl fetches a URL and prints the response.
-s means silent mode — no progress bar.
The URL is AWS's official published list of all their
IP ranges across all regions worldwide.
| head -50 shows only the first 50 lines of output.

**What the output means:**
Each entry shows an IP range, which AWS service uses it,
and which region it belongs to. Security teams use this
list to whitelist AWS traffic in firewalls or to identify
if suspicious outbound traffic is going to AWS infrastructure.

**Screenshot:** saved in artifacts/Day-02/

---

## Lab 5 — Traceroute to AWS

**Command:**
```bash
traceroute aws.amazon.com
```

**What this command does:**
traceroute shows every network hop your packet takes from
your machine to the destination. Each hop is a router
forwarding your packet closer to AWS servers.

**What the output means:**
Watch where the IPs change from Indian network IPs to
Amazon owned IP ranges. You are watching your request
travel from your Kali VM in Gujarat to AWS infrastructure.
The last few hops will be inside Amazon's network.
This is the infrastructure you will be securing.

**Screenshot:** saved in artifacts/Day-02/