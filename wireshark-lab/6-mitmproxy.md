# MITMPROXY HTTPS DECRYPTION

---

## Our Goal

We want to decrypt HTTPS traffic to see what's inside encrypted packets. This shows how Certificate Forgery works and why certificate validation is important.

---

## What is MITMProxy?

MITMProxy (Man-in-the-Middle Proxy) is a tool that:
1. Sits between your device and the internet
2. Generates fake certificates for websites
3. Decrypts HTTPS traffic (if certificate is trusted)
4. Shows you everything in plaintext

---

## How Certificate Forgery Works

1. You try to visit `https://example.com`
2. MITMProxy intercepts the request
3. MITMProxy creates a FAKE certificate for example.com
4. MITMProxy presents this fake certificate to your browser
5. If your browser trusts it, the connection is established
6. MITMProxy can now see everything in plaintext

---

## Why We Did This

We wanted to see:
1. How Certificate Forgery works
2. Why certificate validation is critical
3. What attackers see when they decrypt HTTPS
4. How SOC analysts detect certificate forgeries

---

## Step 1: Install MITMProxy

```bash
sudo apt install mitmproxy -y
What it does: Installs MITMProxy. This is a powerful tool for intercepting and analyzing HTTPS traffic.

Step 2: Enable IP Forwarding
bash
sudo sysctl -w net.ipv4.ip_forward=1
What it does: Enables IP forwarding so our machine can route packets (same as in SSL Stripping).

Step 3: Start MITMProxy
Command (Terminal 1):

bash
sudo mitmproxy --mode transparent --showhost --ssl-insecure --listen-port 8080
Breakdown:

sudo: Run with root privileges

mitmproxy: The tool

--mode transparent: Run in transparent mode (intercepts all traffic)

--showhost: Show the actual destination

--ssl-insecure: Accept any SSL certificate (don't validate)

--listen-port 8080: Listen for traffic on port 8080

What this does: Starts MITMProxy listening on port 8080, ready to intercept HTTPS traffic.

Step 4: Redirect Traffic to MITMProxy
Command (Terminal 2):

bash
sudo bettercap -eval "set arp.spoof.targets 10.26.0.1; arp.spoof on; set http.proxy.port 8080; http.proxy on; net.sniff on"
Breakdown: Same as SSL Stripping but with http.proxy.port 8080 to send traffic to our MITMProxy.

What this does: Redirects ALL traffic through our MITMProxy.

Step 5: Test with curl
Command (Terminal 3):

bash
curl -v -x http://10.26.4.216:8080 https://example.com
Breakdown:

curl: Command-line HTTP client

-v: Verbose mode

-x http://10.26.4.216:8080: Use proxy at this address

https://example.com: The HTTPS site we want to visit

What this does: Forces curl to go through our MITMProxy.

What Happened
HTTP	✅ Working	Traffic passed through MITMProxy
HTTPS	❌ 502 Bad Gateway	Certificate not trusted
Why HTTPS Failed
Plain English:
MITMProxy creates a fake certificate for example.com. Your computer/curl doesn't trust this fake certificate. Because it's not trusted, the connection is refused.

Technical Explanation:

MITMProxy generates a certificate signed by its own CA

This CA is not in your system's trusted certificate store

curl checks the certificate, sees it's not trusted

curl refuses the connection

MITMProxy returns 502 Bad Gateway

How to Fix Certificate Issue
Quick Test (Ignore SSL Errors):

bash
curl -v -x http://10.26.4.216:8080 -k https://example.com
What -k does: Tells curl to ignore certificate errors (skip validation)

Permanent Fix (Install Certificate System-Wide):

bash
sudo cp ~/.mitmproxy/mitmproxy-ca-cert.pem /usr/local/share/ca-certificates/mitmproxy-ca-cert.crt
sudo update-ca-certificates
What this does:

Copies the MITMProxy certificate to system certificate store

Updates the certificate trust database

Now MITMProxy's certificate is trusted system-wide

HTTPS connections will work without errors

MITMProxy Interface Navigation
↑ / ↓	Move through flows (select which request to view)
Enter	View selected flow (see decrypted request and response)
q	Go back / Quit
?	Show help menu
What We See When Viewing a Flow
When we press Enter on a flow, we see:

Full request headers

Full request body (form data, credentials)

Full response headers

Full response body

Everything in plaintext!

How SOC Analysts Detect Certificate Forgery
Filter in Wireshark:

text
tls.handshake.certificate
What to look for:

Self-signed certificates

Certificates from unknown authorities

Mismatched domain names

Expired certificates

Why This Matters
Certificate forgery is a real attack (used in Mimecast breach 2021)

Attackers use it to intercept corporate email

It bypasses HTTPS encryption

Detection requires careful certificate validation

Our Intention
We're learning this because:

We want to understand how HTTPS decryption works

We need to know why certificate validation is critical

We must understand how to detect certificate forgeries

As future SOC analysts, we'll need to identify these attacks

Understanding the attack helps us implement better defenses

What This Taught Us
HTTPS encryption isn't unbreakable

Certificate validation is crucial for security

MITM attacks are possible with certificate forgery

Proper certificate management is essential

Multiple layers of security protect against this

HSTS and certificate pinning are effective defenses

🔐 Security Angle
Real Breach Reference: In 2021, the Mimecast breach used certificate forgery to intercept encrypted email traffic. Attackers generated fake certificates that internal systems trusted.

Defense: HSTS, certificate pinning, VPN, and never ignore certificate warnings.