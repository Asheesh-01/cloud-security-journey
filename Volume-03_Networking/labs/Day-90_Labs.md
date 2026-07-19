# Day 90 — Labs: DHCP and Routing Hands-On

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Lab 1 — View Your Current IP (DHCP Assigned)

```bash
ip addr show
```

**Command breakdown:**
- `ip` — modern Linux networking tool replacing ifconfig
- `addr` — subcommand for address management
- `show` — display current addresses on all interfaces

Look for `inet 192.168.x.x/24` and `dynamic` keyword confirming DHCP assignment.

---

## Lab 2 — View DHCP Lease Information

```bash
cat /var/lib/dhcp/dhclient.leases
```

**Command breakdown:**
- `cat` — display file contents
- `/var/lib/dhcp/dhclient.leases` — DHCP client lease storage file

Shows assigned IP, gateway, DNS server, and lease expiry time received from DHCP server.

If not found try:

```bash
cat /var/lib/NetworkManager/dhclient-eth0.conf
```

---

## Lab 3 — Release and Renew DHCP Lease

```bash
sudo dhclient -r eth0
sudo dhclient eth0
```

**Command breakdown:**

**Release command:**
- `sudo` — root required for network changes
- `dhclient` — DHCP client tool
- `-r` — release flag, sends DHCP Release to server, gives back IP
- `eth0` — interface to release IP on

**Renew command:**
- `sudo dhclient eth0` — request new DHCP lease, performs full DORA process
- Sends Discover, receives Offer, sends Request, receives Acknowledge

Check if IP changed after renewal:

```bash
ip addr show eth0
```

**Command breakdown:**
- `ip addr show eth0` — show addresses only on eth0 interface (not all interfaces)

---

## Lab 4 — View Routing Table

```bash
ip route
```

**Command breakdown:**
- `ip` — modern Linux networking tool
- `route` — subcommand displaying routing table

Read each line:
- `default via X.X.X.X` = default gateway (where unknown destinations go)
- `192.168.x.x/24 dev eth0` = local network (direct delivery)
- `metric 100` = route cost (lower = preferred)

---

## Lab 5 — View Default Gateway Only

```bash
ip route | grep default | awk '{print $3}'
```

**Command breakdown:**
- `ip route` — show full routing table
- `| grep default` — filter to only default route line
- `| awk '{print $3}'` — extract third field which is the gateway IP address

Output is clean gateway IP only. Use this IP in next labs.

---

## Lab 6 — Test Default Gateway Connectivity

```bash
ping -c 4 $(ip route | grep default | awk '{print $3}')
```

**Command breakdown:**
- `ping` — send ICMP echo requests to test connectivity
- `-c 4` — send 4 packets then stop automatically
- `$(...)` — command substitution, runs inner command and uses output as argument
- Inner command: `ip route | grep default | awk '{print $3}'` — automatically finds gateway IP

This pings your gateway without manually typing the IP.

Four replies = gateway reachable. No reply = network problem.

---

## Lab 7 — Trace Route to Destination

```bash
traceroute google.com
```

**Command breakdown:**
- `traceroute` — shows every router hop between you and destination
- `google.com` — destination to trace

Works by sending packets with increasing TTL values:
- TTL=1: first router responds with ICMP time exceeded → hop 1 revealed
- TTL=2: second router responds → hop 2 revealed
- Continues until destination reached

`* * *` in output = router at that hop blocked traceroute (firewall). Hop exists but hidden.

---

## Lab 8 — Add and Remove Static Route

```bash
sudo ip route add 10.0.0.0/8 via 192.168.1.1
ip route
sudo ip route del 10.0.0.0/8
ip route
```

**Command breakdown:**

**Add route:**
- `sudo ip route add` — add new entry to routing table (root required)
- `10.0.0.0/8` — destination network (all 10.x.x.x addresses)
- `via 192.168.1.1` — send traffic for this network through this gateway

**View table:**
- `ip route` — verify new route appears in table

**Delete route:**
- `sudo ip route del 10.0.0.0/8` — remove the static route (cleanup)

**View table again:**
- `ip route` — verify route removed

---

## Lab 9 — Capture DHCP Traffic with tcpdump

Open two terminals.

**Terminal 1 — start capture:**

```bash
sudo tcpdump -i eth0 -n port 67 or port 68
```

**Command breakdown:**
- `sudo` — root required for packet capture
- `tcpdump` — command-line packet capture tool
- `-i eth0` — capture on eth0 interface (your network card)
- `-n` — do not resolve hostnames, show IP numbers
- `port 67` — DHCP server port
- `or port 68` — OR DHCP client port
- Captures all DHCP traffic in both directions

**Terminal 2 — trigger DHCP:**

```bash
sudo dhclient -r eth0 && sudo dhclient eth0
```

**Command breakdown:**
- `sudo dhclient -r eth0` — release IP (sends DHCP Release)
- `&&` — run next command only if previous succeeded
- `sudo dhclient eth0` — request new IP (triggers full DORA process)

Watch Terminal 1 for four DORA packets appearing in sequence.

---

## Lab 10 — View Network Interface Statistics

```bash
ip -s link show eth0
```

**Command breakdown:**
- `ip` — networking tool
- `-s` — statistics flag (show packet counts and errors)
- `link` — subcommand for link-layer information
- `show eth0` — show only eth0 interface

**Output shows:**
- `RX` = received packets, bytes, errors, dropped
- `TX` = transmitted packets, bytes, errors, dropped

High error count = possible network hardware issue or attack causing malformed packets.