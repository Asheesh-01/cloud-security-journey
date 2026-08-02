# SSL STRIPPING ATTACK

---

## Our Goal

We want to simulate a real-world attack where a hacker intercepts HTTPS traffic and forces it to downgrade to HTTP. This shows why HSTS (HTTP Strict Transport Security) is important.

---

## What is SSL Stripping?

**HTTPS:** Encrypted traffic (locked safe - can't see inside)  
**HTTP:** Unencrypted traffic (postcard - anyone can read it)

**SSL Stripping works like this:**
1. You try to visit `https://bank.com`
2. Attacker intercepts your request
3. Attacker sends you `http://bank.com` instead
4. Your browser goes to `http://bank.com` (not secure)
5. You type your password
6. Attacker sees it in plaintext
7. You don't notice because the site looks the same

---

## How We Did It

### Step 1: Install BetterCap

```bash
sudo apt install bettercap -y
What this does: Installs BetterCap - a Swiss Army knife for network attacks. It can do ARP spoofing, SSL stripping, packet sniffing, and more.

Step 2: Enable IP Forwarding
bash
sudo sysctl -w net.ipv4.ip_forward=1
What this does: Enables IP forwarding on our machine. This allows our computer to act like a router - receiving packets from one device and forwarding them to another.

Why we need this: For the attack to work, traffic must flow through our machine. IP forwarding makes this possible.

Step 3: Launch the Attack
bash
sudo bettercap -eval "set arp.spoof.targets 10.26.0.1; arp.spoof on; set http.proxy.sslstrip true; http.proxy on; net.sniff on"
Breakdown:

sudo: Run with root privileges (needed for network manipulation)

bettercap: The tool we're using

-eval: Execute the following commands

set arp.spoof.targets 10.26.0.1: Target the router (pretend to be it)

arp.spoof on: Start ARP spoofing

set http.proxy.sslstrip true: Enable SSL stripping

http.proxy on: Start HTTP proxy

net.sniff on: Capture all traffic

What this does:

ARP spoofing: Tricks devices into thinking our computer is the router

All traffic goes through our machine

SSL stripping: Downgrades HTTPS to HTTP

Sniffing: Captures all traffic

Step 4: What Happened
BetterCap started showing:

endpoint.new: New devices detected on network

mdns: mDNS queries being captured

arp.spoof: ARP spoofing active

http.proxy: Proxy running

Step 5: Stop the Attack
bash
Ctrl + C
What it does: Stops BetterCap

bash
sudo sysctl -w net.ipv4.ip_forward=0
What it does: Disables IP forwarding (returns to normal)

How SOC Analysts Detect This
In Wireshark, we use this filter:

text
arp.duplicate-address-frame
What it shows: Multiple devices claiming the same IP address. In ARP spoofing, both the real router AND the attacker claim to be the router. This creates duplicate IP addresses.

Why This Attack Works
ARP has no authentication

Devices trust ARP responses without verification

Users don't notice the downgrade

Sites without HSTS are vulnerable

Defenses
SSL Stripping	HSTS (browser remembers to use HTTPS)
ARP Spoofing	Static ARP entries, port security
MITM	VPN, certificate validation
Our Intention
We're learning this because:

We want to understand how real hackers operate

We need to know what vulnerabilities exist

We must understand how to detect these attacks

As future SOC analysts, we need to recognize these patterns

Understanding the attack helps us defend against it

🔐 Security Angle
Real Breach Reference: In 2015, Lenovo pre-installed a certificate that allowed SSL stripping on 100,000+ laptops. Users thought they were on HTTPS but their traffic was being intercepted.

What This Taught Us:

HTTPS isn't always secure if users ignore warnings

ARP spoofing is powerful and easy to do

Security requires multiple layers (Defense in Depth)

User education is critical

