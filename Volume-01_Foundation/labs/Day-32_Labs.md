# Day 32 — Labs: Zero Trust

**Environment:** Kali Linux VM

---

## Lab 1 — Audit SSH Configuration
```bash
sudo cat /etc/ssh/sshd_config | grep -E "PasswordAuthentication|PubkeyAuthentication|PermitRootLogin|MaxAuthTries"
```
- `cat /etc/ssh/sshd_config` — reads the SSH daemon config file
- `grep -E` — extended regex enabling | for OR matching
- Checks four Zero Trust-relevant settings simultaneously
- PasswordAuthentication no = key-only = stolen password cannot authenticate

---

## Lab 2 — Disable Password Authentication
```bash
sudo sed -i 's/#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo grep "PasswordAuthentication" /etc/ssh/sshd_config
```
- `sed -i` — edit file in place
- `s/old/new/` — substitute matching text
- Two sed commands handle both commented and uncommented variants
- Final grep confirms the change was applied

---

## Lab 3 — Check Active Sessions
```bash
ss -tnp state established
```
- `ss` — Socket Statistics
- `-t` — TCP only
- `-n` — numeric output
- `-p` — show owning process
- `state established` — only active connections, not listening
- Every connection here should have a known, verified identity

---

## Lab 4 — Review Authentication Log
```bash
sudo grep -E "Accepted|Failed" /var/log/auth.log | tail -20
```
- `grep -E "Accepted|Failed"` — matches both successful and failed logins
- Zero Trust: even successful logins warrant review for anomalies
- Unexpected source IPs or times on successful logins = re-verify

---

## Lab 5 — Review Firewall Policy
```bash
sudo ufw status numbered
```
- Shows current network access policy with rule numbers
- Zero Trust network principle: default deny, explicit allow per rule
- Every allowed port is a documented trust decision

---

## Lab 6 — Audit Running Services
```bash
systemctl list-units --type=service --state=running
```
- `systemctl list-units` — lists systemd units
- `--type=service` — services only
- `--state=running` — currently active only
- Each running service is a trust surface — disable unnecessary ones
- `sudo systemctl disable [service]` removes a service from auto-start