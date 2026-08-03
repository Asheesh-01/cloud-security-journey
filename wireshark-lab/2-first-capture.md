# FIRST PACKET CAPTURE

---

## Our Goal

We want to capture our first network packets to see what a live network looks like. We'll learn how to start/stop captures and use basic filters.

---

## Step 1: Start Capturing

**Action:** In Wireshark, double-click on Wi-Fi interface (`wlp85s0f0`)

**What this does:** Starts capturing ALL packets flowing through our Wi-Fi interface. This includes traffic from all devices on our network.

---

## Step 2: Generate Traffic

**Action:** Open browser and visit `http://example.com`

**What this does:** Creates HTTP traffic for us to capture. Example.com is a simple test website.

---

## Step 3: Stop Capturing

**Action:** Click the red square in Wireshark

**What this does:** Stops the capture so we can analyze the packets we collected.

---

## Step 4: Apply Filter

**Command in filter bar:**
http

text

**What this does:** Tells Wireshark to only show HTTP protocol packets. All other packets are hidden from view.

---

## Step 5: Find Our Request

**What we look for:** A packet with "GET /" and "HTTP/1.1" in the Info column

**What this means:** This is our browser requesting the webpage. The "GET" is the request method, "/" is the page requested, and "HTTP/1.1" is the protocol version.

---

## What Each Packet Shows Us

When we click on the packet, we see:
- **Frame:** Physical layer information
- **Ethernet II:** MAC addresses (source and destination hardware addresses)
- **Internet Protocol Version 4:** Source IP (our computer) and Destination IP (the server)
- **Transmission Control Protocol:** Source port and destination port
- **Hypertext Transfer Protocol:** The actual HTTP request

---

## Filters We Learned

| Filter | What It Does |
|--------|--------------|
| `http` | Shows only HTTP protocol packets |
| `http.request.method == "POST"` | Shows only POST requests (used for login forms) |
| `ip.addr == X.X.X.X` | Shows traffic to/from a specific IP address |
| `tls` | Shows HTTPS (encrypted) traffic |
| `dns` | Shows domain name lookup queries |

---

## Why We Did This

1. We learned the basic Wireshark workflow: Start capture → Generate traffic → Stop → Filter → Analyze
2. We saw what raw network packets look like
3. We learned filters - the most important skill in Wireshark
4. We understand that most traffic is HTTPS (encrypted) which we can't read

---

## Observation

Most traffic on our network is HTTPS (port 443). This means the content is encrypted and Wireshark can't show us what's inside. This is good for security, but makes learning harder.

---

## 🔐 Security Angle

**Why this matters:** When you're on public Wi-Fi, anyone can capture packets. This is why you should always use HTTPS and VPNs....

**Real Breach Reference:** In 2010, security researcher Robert Graham demonstrated that 30% of users on public Wi-Fi had their credentials stolen within one hour — simply because they visited HTTP sites.