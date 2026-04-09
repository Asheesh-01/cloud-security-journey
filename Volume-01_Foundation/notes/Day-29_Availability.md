# Day 29 — CIA Triad: Availability

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 15 — Basic Security Principles
**Subtopic:** 3 — Availability

---

## What It Is

Systems and data are accessible when authorized users need them.
Question: can authorized users access what they need, when they need it?
Availability failures have direct physical and business consequences.

## Key Terms

**Uptime** — percentage of time system is operational.
99.9% = 8.7 hrs downtime/year. 99.99% = 52 min. 99.999% = 5 min.

**SPOF** — Single Point of Failure. One component failure = total failure.
Eliminate SPOFs through redundancy.

**Redundancy** — duplicate systems so failure of one does not cause outage.
RAID, load balancers, multi-region cloud, backup power.

**Failover** — automatic switch to backup when primary fails.
Invisible to users when configured correctly.

**RTO** — Recovery Time Objective. Max acceptable time to restore after failure.
**RPO** — Recovery Point Objective. Max acceptable data loss in time.
RPO of 1 hour = backup every hour = max 1 hour of data lost.

**DDoS** — flood target with traffic from thousands of sources.
Volumetric, Protocol, Application-layer (Layer 7) variants.

**Backup** — separate copy of data. Must be tested regularly.
Untested backup = not a backup.

## How Availability Is Broken

**DDoS** — overwhelming traffic makes service unresponsive.
**Ransomware** — encrypts files, systems unusable.
**Physical destruction** — fire, flood, hardware failure.
**Resource exhaustion** — CPU, memory, disk, bandwidth consumed fully.
**DNS failure** — domain names unresolvable, services unreachable by name.

## Real Breach — Dyn DNS DDoS 2016

Mirai botnet: 600,000 IoT devices, default credentials, port 23.
Target: Dyn DNS provider used by Twitter, Netflix, Reddit, GitHub, PayPal.
Attack: 1.2 Tbps DDoS against Dyn infrastructure.
Result: DNS unavailable, major services unreachable for 11 hours.
No data stolen. No systems compromised. Pure availability attack.

Lesson: security depends on dependencies.
Fix: use multiple DNS providers simultaneously — no single DNS SPOF.

## Commands

```bash
uptime                              # system uptime and load averages
df -h                               # disk space — full disk = outage
free -h                             # RAM usage — exhaustion causes crashes
top -b -n 1 | head -20             # CPU usage by process
cat /proc/loadavg                   # kernel load average file
nslookup google.com                 # default DNS
nslookup google.com 8.8.8.8        # query specific DNS directly
nslookup google.com 1.1.1.1        # query alternate DNS
stress --cpu 2 --timeout 10        # simulate CPU load — observe impact
ls -lh /tmp/testfile.txt 2>/dev/null || echo "File missing"
```