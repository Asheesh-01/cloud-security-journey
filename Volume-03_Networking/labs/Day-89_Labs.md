# Day 89 — Labs: DNS System Hands-On

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Lab 1 — Basic DNS Query with dig

```bash
dig google.com
```

Output shows full DNS response including answer, TTL, authoritative servers.

---

## Lab 2 — Query Specific Record Types

```bash
dig google.com A
dig google.com AAAA
dig google.com MX
dig google.com TXT
dig google.com NS
dig google.com SOA
```

Each returns different record type. Note TTL differences between record types.

---

## Lab 3 — Trace Full DNS Resolution Path

```bash
dig google.com +trace
```

Shows complete resolution from root to TLD to authoritative nameserver.

Output shows every step of recursive lookup.

---

## Lab 4 — Query Specific DNS Server

```bash
dig google.com @8.8.8.8
dig google.com @1.1.1.1
dig google.com @9.9.9.9
```

Queries Google DNS, Cloudflare DNS, Quad9 DNS directly. Compare response times.

---

## Lab 5 — Reverse DNS Lookup (PTR)

```bash
dig -x 8.8.8.8
dig -x 142.250.195.46
```

Maps IP back to domain name. Used in security investigations.

---

## Lab 6 — DNS with nslookup

```bash
nslookup google.com
nslookup google.com 8.8.8.8
nslookup -type=MX google.com
nslookup -type=TXT google.com
```

Alternative tool to dig. Simpler output.

---

## Lab 7 — Check DNS Cache on Linux

```bash
sudo systemd-resolve --statistics
```

Shows DNS cache hit rate and cached entries.

---

## Lab 8 — Flush DNS Cache

```bash
sudo systemd-resolve --flush-caches
sudo systemd-resolve --statistics
```

Clears cached DNS entries. After flushing, next query does full recursive lookup.

---

## Lab 9 — Check DNS Resolution Time (Cached vs Uncached)

```bash
sudo systemd-resolve --flush-caches
time dig google.com
time dig google.com
```

First query: full recursive lookup (slow, 50-200ms).

Second query: cached answer (fast, less than 5ms).

---

## Lab 10 — Find Email Security Records

```bash
dig google.com TXT | grep "spf"
dig google._domainkey.google.com TXT
dig _dmarc.google.com TXT
```

Shows SPF, DKIM, DMARC records protecting Google's email from spoofing.

---

## Lab 11 — DNS Zone Transfer Attempt (Educational Only)

```bash
dig @ns1.google.com google.com AXFR
```

Zone transfer attempts to download all DNS records for a domain.

Modern nameservers block unauthorized zone transfers.

Expected output: "Transfer failed" or "REFUSED"

If zone transfer succeeds on a target, it is a major security finding (exposes internal infrastructure).

---

## Lab 12 — Check if DNSSEC is Enabled

```bash
dig google.com +dnssec
dig cloudflare.com +dnssec
```

Look for `ad` flag in response (Authenticated Data) — means DNSSEC validated.

Also look for RRSIG records (cryptographic signatures).