# Day 01 — Labs

**Topic:** What is Cloud Security
**Environment:** Kali Linux VM

---

## Lab 1 — traceroute to google.com

**Command:**
```bash
traceroute google.com
```

**What this command does:**
traceroute shows every network hop your request passes through before
reaching the destination. Each hop is a router or server that forwards
your packet. You are watching your data travel through infrastructure
you do not own or control — that is the cloud reality.

**What the output means:**
Each numbered line is one hop. The IP address is that router or server.
The time values are how long the packet took to reach that hop.
Some hops show * * * meaning they do not respond to traceroute probes
but are still forwarding your traffic.

**Screenshot:** saved in artifacts/Day-01/

---

## Lab 2 — ping google.com

**Command:**
```bash
ping google.com
```

**What this command does:**
ping sends a small packet to the destination and waits for a reply.
It checks if the host is reachable and measures response time.

**What the output means:**
Each line shows bytes sent, destination IP, sequence number, and time.
This confirms your machine can reach Google's servers over the internet.
The IP address shown is Google's actual server IP resolved from the domain name.

**Screenshot:** saved in artifacts/Day-01/

---

## Lab 3 — nmap ping scan on local network

**Command:**
```bash
ip addr
```
Run this first to find your IP address and network range.
```bash
nmap -sn 192.168.1.0/24
```
Replace 192.168.1.0/24 with your actual network range from ip addr output.

**What this command does:**
nmap is a network scanner. -sn means ping scan only — no port scanning,
just discover which hosts are alive on the network.
192.168.1.0/24 means scan all 254 addresses in this subnet.

**What the output means:**
Every line showing Host is up is a live device on your network.
Each one is part of your attack surface. This is exactly what an
attacker does first — find what is alive before deciding what to attack.

**Screenshot:** saved in artifacts/Day-01/

---

## Lab 4 — AWS metadata service awareness

**Command:**
```bash
curl -s http://169.254.169.254/latest/meta-data/ 2>/dev/null || echo "Not on EC2 — this endpoint exists only on AWS EC2 instances"
```

**What this command does:**
curl fetches a URL. 169.254.169.254 is the AWS Instance Metadata Service
endpoint. On a real EC2 instance this returns IAM credentials, instance ID,
and sensitive configuration data. From your local machine it will fail.

**What the output means:**
You will see the error message since you are not on EC2.
The point is awareness — this endpoint is what SSRF attacks target
in cloud environments. An attacker who can make your server fetch this
URL gets your IAM credentials. Covered in depth in Volume 07.

**Screenshot:** saved in artifacts/Day-01/