# Day 86 — Labs: Ports and Services Testing

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Lab 1 — Memorization Drill

Write all 20 ports from memory without looking (5 minutes):
21  = ___________
22  = ___________
23  = ___________
25  = ___________
53  = ___________
80  = ___________
110 = ___________
143 = ___________
443 = ___________
445 = ___________
3306  = ___________
3389  = ___________
8080  = ___________
8443  = ___________
27017 = ___________
5432  = ___________
6379  = ___________
9200  = ___________
5601  = ___________
2375  = ___________
Check answers against notes file. Repeat until 100% correct.

---

## Lab 2 — Check Locally Listening Ports

```bash
sudo ss -tulnp
```

Document every port found and what service is using it.

Identify:
- Which ports listening on 0.0.0.0 (all interfaces, network-accessible)
- Which ports listening on 127.0.0.1 (localhost only, safe)

---

## Lab 3 — Test Single Port Connectivity

```bash
nc -zv 127.0.0.1 22
```

Test against your own machine (localhost). Should succeed if SSH server running.

Try a closed port:
```bash
nc -zv 127.0.0.1 21
```

Should show "Connection refused" (FTP not installed/running).

---

## Lab 4 — Test Multiple Ports at Once

```bash
nc -zv 127.0.0.1 21 22 23 25 53 80 443
```

Document which ports are open vs closed on your Kali VM.

---

## Lab 5 — Test Ports with Timeout

```bash
nc -zv -w 3 8.8.8.8 53
```

- `-w 3` sets 3-second timeout
- Tests DNS port on Google's public DNS server

Should succeed (Google DNS listens on port 53).

---

## Lab 6 — Identify Service Banner

```bash
nc -v 127.0.0.1 22
```

If SSH running, should show banner like:
PORT     STATE  SERVICE
22/tcp   open   ssh
80/tcp   open   http
443/tcp  open   https
3306/tcp open   mysql
6379/tcp open   redis

**Report format:**
- Finding: Port 3306 (MySQL) exposed to public internet
- Risk: Critical
- Impact: Direct database access attempt possible from any internet source
- Recommendation: Restrict port 3306 to internal network only via firewall rule; require VPN for remote database administration

Write similar entry for port 6379.

---

## Lab 10 — Match Port to Protocol Layer

For each port, identify if TCP, UDP, or both:

1. Port 53 (DNS) → Both (UDP for queries, TCP for zone transfers)
2. Port 80 (HTTP) → TCP
3. Port 443 (HTTPS) → TCP
4. Port 22 (SSH) → TCP
5. Port 123 (NTP, bonus) → UDP

---

## Lab 11 — Real-World Port Check on Your Own VM

```bash
sudo netstat -tulnp
```

(Legacy alternative to ss, still works)

Compare output with ss -tulnp from Lab 2. Should show same information.

---

## Lab 12 — Speed Recall Test

Have someone (or yourself) call out port numbers. Answer service name in under 3 seconds each:

21, 443, 3389, 6379, 22, 27017, 80, 2375, 25, 9200

Target: 100% accuracy, instant recall.