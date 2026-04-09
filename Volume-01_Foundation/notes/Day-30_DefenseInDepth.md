# Day 30 — Defense in Depth

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 15 — Basic Security Principles
**Subtopic:** 4 — Defense in Depth

---

## What It Is

Multiple independent security layers protecting the same asset.
Compromise of one layer does not compromise the asset.
Military origin: multiple defensive lines, not one strong wall.

## Core Mental Model: Assume Breach

Do not design assuming the perimeter will never fail.
Design assuming it will, and build detection and containment for after.
Shifts security from prevention-only to detection and response.

## The 8 Layers

1. Perimeter — firewall, IPS, WAF, DDoS protection
2. Network — segmentation, VLANs, internal firewalls, zero trust
3. Endpoint — EDR, antivirus, host firewall, disk encryption
4. Identity — MFA, PAM, password policies, account monitoring
5. Application — input validation, secure coding, OWASP controls
6. Data — encryption at rest and in transit, DLP, access controls
7. Monitoring — SIEM, logging, alerting, threat hunting
8. Recovery — backups, DR plan, incident response, business continuity

## Why One Control Is Not Enough

Colonial Pipeline: firewall passed valid VPN credential. No MFA = breach.
SolarWinds: AV had no signature for signed legitimate malware. No behavioral detection = 9 months undetected.
Target: FireEye alerts fired and were IGNORED. Alert fatigue = monitoring layer failure.

Alert fatigue: too many alerts causing analysts to dismiss real ones.
Most dangerous monitoring failure — tools work, process fails.

## Real Example — Target 2013 (Defense in Depth View)

Layer 1 Perimeter: FAILED — vendor had legitimate access
Layer 2 Network: FAILED — no segmentation, POS reachable from HVAC subnet
Layer 3 Endpoint: PARTIAL — FireEye detected malware but alerts ignored
Layer 4 Identity: FAILED — no MFA on vendor account
Layer 5 Application: FAILED — POS software had known vulnerabilities
Layer 6 Data: FAILED — card data not encrypted end-to-end
Layer 7 Monitoring: FAILED — alerts seen and dismissed
Layer 8 Recovery: N/A — 40 million cards already stolen

## Commands

```bash
sudo ufw status verbose
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw status numbered
ss -tlnp
sudo grep "Failed password" /var/log/auth.log | wc -l
sudo dmsetup ls 2>/dev/null
echo "=== FIREWALL ===" && sudo ufw status
echo "=== LISTENING SERVICES ===" && ss -tlnp
echo "=== FAILED LOGINS ===" && sudo grep -c "Failed password" /var/log/auth.log
echo "=== DISK ENCRYPTION ===" && sudo dmsetup ls 2>/dev/null
```