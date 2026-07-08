# Day 86 — Ports and Services

**Volume:** 03 — Networking From Zero
**Topic:** 8 — Ports and Services
**Requirement:** Memorize all 20 ports without looking. Tested in every security interview.

---

## What is a Port

16-bit number (0-65535) identifying specific process/service on a computer.

Analogy: IP address = building address, Port = apartment number, Service = who lives there.

---

## Port Ranges

| Range | Name | Description |
|-------|------|--------------|
| 0-1023 | Well-known | Standard services (HTTP, SSH, FTP), require admin to bind |
| 1024-49151 | Registered | Application-specific, registered with IANA |
| 49152-65535 | Dynamic/Private | Temporary client-side connections |

---

## The 20 Must-Memorize Ports

### Remote Access
| Port | Service | Notes |
|------|---------|-------|
| 21 | FTP | File transfer, sends credentials in CLEARTEXT |
| 22 | SSH | Encrypted remote terminal access |
| 23 | Telnet | Unencrypted remote access — legacy, dangerous |
| 3389 | RDP | Windows remote desktop — common ransomware entry point |

### Email
| Port | Service | Notes |
|------|---------|-------|
| 25 | SMTP | Send email between servers, often blocked outbound (anti-spam) |
| 110 | POP3 | Download email, removes from server |
| 143 | IMAP | Download email, keeps on server (syncs across devices) |

### Web Traffic
| Port | Service | Notes |
|------|---------|-------|
| 53 | DNS | Domain name resolution, TCP+UDP |
| 80 | HTTP | Unencrypted web traffic |
| 443 | HTTPS | Encrypted web traffic (TLS/SSL) — standard today |
| 8080 | HTTP-alt | Alternative HTTP, proxies, dev servers |
| 8443 | HTTPS-alt | Alternative HTTPS, admin panels |

### File Sharing
| Port | Service | Notes |
|------|---------|-------|
| 445 | SMB | Windows file sharing — WannaCry exploited via EternalBlue |

### Databases
| Port | Service | Notes |
|------|---------|-------|
| 3306 | MySQL | Never expose to internet |
| 5432 | PostgreSQL | Never expose to internet |
| 27017 | MongoDB | History of mass breaches (no default auth) |
| 6379 | Redis | Often no authentication by default |

### Modern Stack
| Port | Service | Notes |
|------|---------|-------|
| 9200 | Elasticsearch | Multiple breaches from misconfiguration |
| 5601 | Kibana | Elasticsearch dashboard, exposes indexed data |

### Containers
| Port | Service | Notes |
|------|---------|-------|
| 2375 | Docker (unencrypted) | Full host control if exposed — critical finding |

---

## Critical Security Findings (Ports That Should NEVER Be Public)

If any of these appear in a public-facing scan, it's an automatic critical finding:

- 3306 (MySQL)
- 5432 (PostgreSQL)
- 27017 (MongoDB)
- 6379 (Redis)
- 2375 (Docker unencrypted)
- 445 (SMB)
- 23 (Telnet)
- 3389 (RDP, unless properly secured with VPN/MFA)

---

## Real Incident — MongoDB Ransom Attacks 2017

**What happened:**
1. Early MongoDB versions had NO authentication by default
2. Thousands of companies deployed without realizing port 27017 was internet-facing
3. Attackers scanned internet, found open MongoDB instances
4. Connected directly: `mongo target_ip:27017`
5. Downloaded entire databases
6. Deleted original data
7. Left ransom note demanding Bitcoin
8. 27,000+ databases ransomed in one week

**Root cause:** Default configuration exposed database with no authentication requirement.

**Fix:** MongoDB changed default to localhost-only binding. Explicit configuration required for external access.

**Lesson:** Database ports must never be directly internet-facing. Always require authentication and firewall restriction.

---

## Testing Commands

**Check local listening ports:**
```bash
sudo ss -tulnp
```

**Test if remote port is open:**
```bash
nc -zv 192.168.1.50 22
```

**Test multiple ports:**
```bash
nc -zv 192.168.1.50 21 22 23 80 443
```

**Interpretation:**
- "succeeded" = port open, service responding
- "Connection refused" = port closed, no service listening
- "timeout" = port likely filtered by firewall

---

## Memorization Practice

Say out loud until instant recall:

"21 FTP, 22 SSH, 23 Telnet, 25 SMTP, 53 DNS, 80 HTTP, 110 POP3, 143 IMAP, 443 HTTPS, 445 SMB, 3306 MySQL, 3389 RDP, 8080 HTTP-alt, 8443 HTTPS-alt, 27017 MongoDB, 5432 PostgreSQL, 6379 Redis, 9200 Elasticsearch, 5601 Kibana, 2375 Docker"

---

## Interview Questions

**Q1:** Why is Telnet (port 23) considered insecure?

**A1:** Telnet sends all data including passwords in cleartext (unencrypted). Anyone intercepting network traffic (e.g., via ARP spoofing or packet capture) can read credentials directly. SSH (port 22) replaced Telnet because it encrypts the entire session.

**Q2:** What is the difference between POP3 and IMAP?

**A2:** POP3 downloads email to the client and typically removes it from the server (single-device access). IMAP downloads email but keeps it on the server, allowing sync across multiple devices (phone, laptop, webmail all show same inbox state).

**Q3:** Why should database ports never be exposed to the internet?

**A3:** Databases contain sensitive data (user records, credentials, financial info). If directly accessible from the internet, attackers can attempt authentication bypass, exploit known vulnerabilities, or in cases of default-no-auth configurations (early MongoDB, Redis), connect directly without any credentials. Databases should only be accessible from application servers within a private network segment, never from the public internet.