# Day 30 — Labs: Defense in Depth

**Environment:** Kali Linux VM

---

## Lab 1 — Check Firewall Status
```bash
sudo ufw status verbose
```
- `ufw` — Uncomplicated Firewall, front-end for iptables
- `status verbose` — shows enabled state, default policies, all rules

---

## Lab 2 — Enable Firewall With Default Deny
```bash
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing
```
- `ufw enable` — activates the firewall
- `default deny incoming` — block all inbound by default
- `default allow outgoing` — allow all outbound by default
- Correct starting posture: deny everything, allow only what is needed

---

## Lab 3 — Allow SSH Only
```bash
sudo ufw allow ssh
sudo ufw status numbered
```
- `allow ssh` — permits TCP port 22 inbound
- `status numbered` — shows rules with index numbers for management
- Result: only SSH permitted inbound — minimal attack surface

---

## Lab 4 — Count Failed Login Attempts
```bash
sudo grep "Failed password" /var/log/auth.log | wc -l
```
- `grep "Failed password"` — finds all failed SSH login lines in auth.log
- `| wc -l` — counts the number of matching lines
- Large number = active brute force against your SSH service
- This is the monitoring layer working

---

## Lab 5 — Check Disk Encryption
```bash
sudo dmsetup ls 2>/dev/null || echo "No encrypted volumes found"
```
- `dmsetup ls` — lists device mapper devices including LUKS volumes
- LUKS = Linux Unified Key Setup, standard full-disk encryption
- `2>/dev/null` — suppress errors
- `||` — if command fails or returns nothing, print the message
- No output = disk not encrypted = data layer gap

---

## Lab 6 — Quick Defense Posture Check
```bash
echo "=== FIREWALL ===" && sudo ufw status
echo "=== LISTENING SERVICES ===" && ss -tlnp
echo "=== FAILED LOGINS ===" && sudo grep -c "Failed password" /var/log/auth.log 2>/dev/null
echo "=== DISK ENCRYPTION ===" && sudo dmsetup ls 2>/dev/null || echo "Not encrypted"
```
- Combines four layer checks into one command block
- `&&` — run next command only if previous succeeded
- Gives instant multi-layer security posture view of the local machine
- In real environments this becomes a hardening checklist script