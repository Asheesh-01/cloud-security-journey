# Day 87 — Labs: TCP vs UDP Hands-On

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04

---

## Lab 1 — Understand TCP Connection States

```bash
sudo netstat -tan | head -20
```

Or modern:

```bash
ss -tan | head -20
```

**Look for:**
- LISTEN (server waiting for connections)
- ESTABLISHED (active connections)
- TIME_WAIT (closed but in waiting period)

Document each state you find.

---

## Lab 2 — View SSH Connection (TCP)

SSH to a server (or localhost):

```bash
ssh localhost
```

(If SSH server running, will prompt for password. Cancel with Ctrl+C.)

In another terminal, watch TCP state:

```bash
ss -tan | grep 22
```

You'll see connection in SYN_SENT or ESTABLISHED state.

---

## Lab 3 — Capture TCP Handshake with tcpdump

Terminal 1 (capture traffic):

```bash
sudo tcpdump -i lo -n 'tcp port 22' -A
```

(Filters to port 22 only, -A prints ASCII)

Terminal 2 (initiate SSH):

```bash
ssh localhost
```

In Terminal 1, you'll see captured packets. Look for:
- SYN packet from client
- SYN-ACK from server
- ACK from client

Exit: Ctrl+C in both terminals.

---

## Lab 4 — Analyze Sequence Numbers

Run tcpdump with more detail:

```bash
sudo tcpdump -i lo -n 'tcp port 22' -vv
```

Output shows: `ack 1001 win 512 ack 1001 win 512`

- `ack 1001` = expects next packet at sequence 1001
- `win 512` = this side's window size (buffer space)

---

## Lab 5 — TCP Connection Timing

```bash
time nc -zv 192.168.1.1 22
```

Measures time to establish connection (handshake overhead).

Output: `real    0m0.002s` (2 milliseconds for handshake locally)

Over internet, typically 50-200ms.

---

## Lab 6 — UDP Test (DNS Query)

```bash
nc -zu 8.8.8.8 53
```

UDP doesn't wait for handshake, responds immediately (or timeout).

No "Connection succeeded" message (UDP is connectionless).

---

## Lab 7 — Compare TCP vs UDP Speed

**TCP (reliable, slower):**

```bash
time nc -zv 8.8.8.8 53
```

**UDP (fast, no guarantee):**

```bash
time nc -zu 8.8.8.8 53
```

UDP completes faster (no handshake).

---

## Lab 8 — View All Listening Ports and Their States

```bash
sudo ss -tulnp
```

Document:
- Which services listening (TCP vs UDP)
- Which IPs listening on (0.0.0.0 = all, 127.0.0.1 = localhost)
- Which process owns each port

---

## Lab 9 — Watch TCP States Change

Monitor TCP states while establishing and closing connection.

Terminal 1:

```bash
watch -n 0.1 'ss -tan | grep 22'
```

(Updates every 0.1 seconds)

Terminal 2:

```bash
ssh localhost
```

In Terminal 1, watch states: SYN_SENT → ESTABLISHED → FIN_WAIT → TIME_WAIT

---

## Lab 10 — TCP Connection with curl (HTTP)

```bash
curl -v http://google.com
```

Output shows TCP connection process:
- `Connected to google.com`
- HTTP request/response
- `Connection closed`

Run with tcpdump in background to see packets.

---

## Lab 11 — Identify SYN_RECEIVED State (Half-Open Connections)

Normally, SYN_RECEIVED is brief (handshake completes quickly).

To see it, you need partially-closed connection (attacker scenario).

Advanced: Run SYN flood simulation (educational only, own VM):

```bash
sudo hping3 -S --flood -p 22 127.0.0.1
```

(Sends SYN packets rapidly)

In another terminal:

```bash
watch -n 0.1 'ss -tan | grep 22'
```

You'll see accumulation of SYN_RECEIVED states (server waiting for ACK).

After stopping hping3, states clear as TCP times out.

---

## Lab 12 — TCP Connection Sequence Number Analysis

Run tcpdump with sequence numbers:

```bash
sudo tcpdump -i lo -S 'tcp port 22' -n
```

Output shows actual sequence numbers (not relative):
- Client SYN: `seq 1000000000` (random large number)
- Server SYN-ACK: `seq 2000000000, ack 1000000001`
- Client ACK: `seq 1000000001, ack 2000000001`

Note: Each side's sequence starts randomly (security against prediction).

---

## Lab 13 — Track Connection Lifecycle

Create simple connection and monitor entire lifecycle:

Terminal 1:

```bash
while true; do ss -tan | grep 22; sleep 1; done
```

(Monitor every 1 second)

Terminal 2:

```bash
ssh -v localhost 2>&1 | head -20
```

In Terminal 1, watch:
1. SYN_SENT (sending)
2. ESTABLISHED (connected)
3. Type something, then Ctrl+D to exit
4. FIN_WAIT_1, FIN_WAIT_2, TIME_WAIT (closing)
5. Gone (after 2 minutes)

---

## Lab 14 — Interview Question: Draw TCP Handshake

Draw and label complete TCP 3-way handshake:
Client (192.168.1.100:54321)        Server (192.168.1.50:22)
     SYN seq=1000
──────────────────────→
     (empty, just handshake)

          SYN-ACK seq=2000, ack=1001
←──────────────────────

     ACK seq=1001, ack=2001
──────────────────────→

← ESTABLISHED on both sides →
Data can flow both directions

Label each packet with sequence and acknowledgment numbers.

---

## Lab 15 — Test Timeout Behavior

**TCP (reliable, retries):**

Try to connect to non-existent IP:

```bash
timeout 10 nc -zv 192.0.2.1 22
```

Takes ~10 seconds to timeout (TCP keeps retrying with backoff).

**UDP (no retry):**

```bash
timeout 5 nc -zu 192.0.2.1 53
```

Timeout much faster (UDP doesn't retry at transport level).