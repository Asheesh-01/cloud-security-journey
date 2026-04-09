# Day 29 — Labs: Availability

**Environment:** Kali Linux VM

---

## Lab 1 — System Uptime
```bash
uptime
```
- `uptime` — prints time since last boot, logged-in user count,
  and load averages for 1, 5, and 15 minutes.
- Load average above number of CPU cores = system under pressure.

---

## Lab 2 — Disk Space
```bash
df -h
```
- `df` — disk free, shows filesystem space usage
- `-h` — human-readable sizes (GB, MB)
- `Use%` column: 100% = filesystem full = availability failure
- Full `/var` crashes logging. Full `/` crashes the OS.

---

## Lab 3 — Memory Usage
```bash
free -h
```
- `free` — shows RAM and swap memory usage
- `-h` — human-readable
- Watch the `available` column — when near 0, system crashes or swaps heavily
- Swap usage means RAM is exhausted — severe performance degradation

---

## Lab 4 — CPU Usage Snapshot
```bash
top -b -n 1 | head -20
```
- `top` — process viewer showing CPU and memory usage
- `-b` — batch mode, outputs text instead of interactive display
- `-n 1` — one snapshot then exit
- `| head -20` — first 20 lines
- Top process = highest CPU consumer at this moment

---

## Lab 5 — Kernel Load Average
```bash
cat /proc/loadavg
```
- `/proc/loadavg` — virtual file exposed by kernel
- Five values: 1-min avg, 5-min avg, 15-min avg,
  running/total processes, last PID
- Compare load values to number of CPU cores for context

---

## Lab 6 — DNS Availability Testing
```bash
nslookup google.com
nslookup google.com 8.8.8.8
nslookup google.com 1.1.1.1
```
- First command — uses configured default DNS server
- Second — queries Google DNS (8.8.8.8) directly, bypasses default
- Third — queries Cloudflare DNS (1.1.1.1) directly
- If default fails but alternates work: DNS provider failure confirmed
- If all fail: internet connectivity issue

---

## Lab 7 — Simulate CPU Load
```bash
sudo apt install stress -y
stress --cpu 2 --timeout 10
```
- `apt install stress` — installs the stress testing tool
- `stress --cpu 2` — generates maximum load on 2 CPU cores
- `--timeout 10` — runs for 10 seconds then stops
- Open a second terminal during stress and run `top` to observe
  CPU usage spike — this is what resource exhaustion looks like

---

## Lab 8 — Backup Verification Simulation
```bash
ls -lh /tmp/testfile.txt 2>/dev/null || echo "File missing - backup needed"
```
- `ls -lh` — long format with human-readable file size
- `2>/dev/null` — suppress error message if file does not exist
- `||` — OR operator: if previous command fails (file missing), run this
- `echo` — prints the message confirming file is absent
- Simulates a backup check script — real scripts verify backup file
  exists, is recent, and is the correct size