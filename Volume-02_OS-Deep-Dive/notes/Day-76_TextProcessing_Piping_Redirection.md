# Day 76 — Text Processing + Pipe Chaining + Redirection

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 9.5 — Linux Command Line Mastery
**Subtopics:** 6 — Text Processing | 7 — Pipe Chaining | 8 — Redirection
**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04
**Context:** Real incident response scenario

---

## The Incident Scenario

Server web-prod-01 showing 15,000 HTTP 403 errors in 2 hours. Possible brute force attack. You have a 600MB access log. Raw grep and find are not enough — you need to transform raw data into actionable intelligence.

---

## Subtopic 6 — Text Processing

### What It Is
Transform unstructured log data into structured, ranked, filtered output. Extract specific columns, perform calculations, search and replace patterns. Core of log analysis.

### Key Tools

**awk** — field extraction and complex text processing. Reads input line by line, splits into fields, performs pattern matching and actions.

**sed** — search and replace. Delete lines, insert text, modify patterns. Stream editor.

**sort** — arrange lines. Alphabetical by default. `-n` numeric. `-r` reverse. `-u` unique (equivalent to sort | uniq).

**uniq** — remove consecutive duplicates. **MUST be sorted first.**

**wc** — count. `-l` lines. `-w` words. `-c` characters.

### Key Terms

**Field** — a column in structured data. Separated by delimiter.

**awk variables:**
- `$0` — entire line
- `$1`, `$2`, etc. — first, second field
- `$NF` — last field
- `NF` — number of fields in line
- `NR` — current line number

**Apache log format:**
IP - - [timestamp] "HTTP_METHOD PATH PROTOCOL" STATUS SIZE

1  2 3      4                5                      6      7

### Real Incident Commands

**Extract attacker IPs from 403 errors, ranked by frequency:**
```bash
grep "403" /var/log/apache2/access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head -20
```
- `grep "403"` — filter to 403 status codes
- `awk '{print $1}'` — extract IP (field 1)
- `sort` — group identical IPs
- `uniq -c` — count occurrences
- `sort -rn` — highest count first
- `head -20` — top 20 results

**Extract attack URLs from specific attacker:**
```bash
grep "403" /var/log/apache2/access.log | grep "192.168.1.100" | awk '{print $7}' | sort | uniq -c | sort -rn
```
- Field 7 is the requested URL path
- Shows what attacker targeted most

**Conditional awk — extract IPs attacking admin only:**
```bash
grep "403" /var/log/apache2/access.log | awk '$7 ~ /admin/ {print $1}' | sort | uniq -c | sort -rn
```
- `$7 ~ /admin/` — if field 7 contains "admin"
- Extract only IPs attacking that endpoint

**Check if attack succeeded (200 status):**
```bash
grep "200" /var/log/apache2/access.log | grep "192.168.1.100" | grep "/admin" | wc -l
```
- Result: 0 = no successful access. Attack ongoing.

**sed — remove attacker from logs:**
```bash
sed '/192.168.1.100/d' /var/log/apache2/access.log > /tmp/cleaned_logs.log
```
- `/192.168.1.100/d` — delete lines matching
- Outputs cleaned log without that IP

**sed — anonymize IPs before sharing:**
```bash
sed 's/[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}/XXX.XXX.XXX.XXX/g' /var/log/apache2/access.log
```
- Replaces all IPs with placeholder
- Safe to share with third parties

---

## Subtopic 7 — Pipe Chaining

### What It Is
Chain commands together. Output of left command becomes input of right command. Enables data transformation pipeline — raw logs → filtered data → ranked output → intelligence.

### The Power of Piping

Single command does one thing. Pipe chains single-purpose tools into a powerful pipeline.

**Example — finding attacker IPs:**

Without pipes (separate steps):
```bash
grep "403" /var/log/apache2/access.log > /tmp/step1.log
awk '{print $1}' /tmp/step1.log > /tmp/step2.log
sort /tmp/step2.log > /tmp/step3.log
uniq -c /tmp/step3.log > /tmp/step4.log
sort -rn /tmp/step4.log > /tmp/step5.log
head -20 /tmp/step5.log
```
Creates 5 temporary files. Slow. Messy.

With pipes (one command):
```bash
grep "403" /var/log/apache2/access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head -20
```
One line. Data flows through pipeline. Memory efficient. No temp files.

### Real Incident Pipeline Stages

**Stage 1 — Filter:**
```bash
grep "403" /var/log/apache2/access.log
```
15,847 lines matching status 403.

**Stage 2 — Extract:**
```bash
| awk '{print $1}'
```
Extract IP addresses from each line. 15,847 IPs (with duplicates).

**Stage 3 — Sort:**
```bash
| sort
```
Group identical IPs together. Requirement for uniq.

**Stage 4 — Count:**
```bash
| uniq -c
```
Count each unique IP. Output: count + IP.

**Stage 5 — Rank:**
```bash
| sort -rn
```
Sort by count in reverse (highest first).

**Stage 6 — Limit:**
```bash
| head -20
```
Show only top 20. Human readable.

**Final output:**
250 192.168.1.100
180 192.168.1.101
142 10.0.0.5
89 203.45.67.89
...

---

## Subtopic 8 — Redirection

### What It Is
Direct output of commands to files or input from files. Create reports, preserve evidence, chain complex investigations.

### Key Operators

**`>`** — write to file. Overwrites if exists.
**`>>`** — append to file. Adds to end.
**`<`** — read from file as input.
**`2>`** — redirect errors only.
**`&>`** — redirect both output and errors.
**`|`** — pipe (covered in subtopic 7).

### Real Incident Report Creation

**Create report with header:**
```bash
echo "=== INCIDENT REPORT ===" > /tmp/incident_report.txt
```
- `>` creates file and writes header
- Overwrites if file existed

**Append timestamp:**
```bash
echo "Timestamp: $(date)" >> /tmp/incident_report.txt
```
- `>>` appends to existing file
- `$(date)` command substitution — inserts output of date command

**Append top attackers:**
```bash
echo "" >> /tmp/incident_report.txt
echo "=== TOP ATTACKERS ===" >> /tmp/incident_report.txt
grep "403" /var/log/apache2/access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head -10 >> /tmp/incident_report.txt
```
- Pipeline output appended to report file
- Report grows with each command

**Append attack analysis:**
```bash
echo "" >> /tmp/incident_report.txt
echo "=== ATTACK TARGETS ===" >> /tmp/incident_report.txt
grep "403" /var/log/apache2/access.log | grep "192.168.1.100" | awk '{print $7}' | sort | uniq -c | sort -rn >> /tmp/incident_report.txt
```

**Append success check:**
```bash
echo "" >> /tmp/incident_report.txt
echo "=== SUCCESS CHECK ===" >> /tmp/incident_report.txt
echo "Successful admin logins from attacker:" >> /tmp/incident_report.txt
grep "200" /var/log/apache2/access.log | grep "192.168.1.100" | grep "/admin" | wc -l >> /tmp/incident_report.txt
```

**Final report file contains complete analysis:**
=== INCIDENT REPORT ===

Timestamp: Thu Jun 18 14:32:15 UTC 2026
=== TOP ATTACKERS ===

250 192.168.1.100

180 192.168.1.101
=== ATTACK TARGETS ===

120 /admin

80 /wp-admin.php
=== SUCCESS CHECK ===

Successful admin logins from attacker:

0

### Real-Time Monitoring

Watch attack as it happens:
```bash
tail -f /var/log/apache2/access.log | grep "403" | grep "192.168.1.100"
```
- `tail -f` streams new log lines
- Piped through filters
- Live display of attacker activity as it occurs

---

## Integration — One Real Command

Complete incident response in one command with redirection:

```bash
(echo "INCIDENT REPORT - $(date)"; echo ""; echo "TOP ATTACKERS:"; grep "403" /var/log/apache2/access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head -10; echo ""; echo "PRIMARY ATTACKER TARGETS:"; grep "403" /var/log/apache2/access.log | grep "192.168.1.100" | awk '{print $7}' | sort | uniq -c | sort -rn; echo ""; echo "ATTACK SUCCESS:"; grep "200" /var/log/apache2/access.log | grep "192.168.1.100" | grep "/admin" | wc -l) > /tmp/full_incident_report.txt
```

Parentheses group all commands. Entire output redirected to file. One line. Complete report.

---

## Security Angle — Real Incident Response

Every major breach investigation follows this exact pattern:
1. grep — filter to relevant events
2. awk — extract key fields (IPs, URLs, timestamps)
3. sort | uniq -c — identify patterns
4. sort -rn — rank by severity
5. Redirection — save evidence and report

The difference between detecting an attack in 2 minutes vs 2 weeks is mastery of these tools. This skill is what makes senior SOC analysts irreplaceable.
