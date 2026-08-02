
# MDNS ANALYSIS

---

## Our Goal

We want to understand how devices discover each other on our home network. We'll analyze mDNS (Multicast DNS) traffic to see what devices are present and what services they're advertising.

---

## What is mDNS?

mDNS (Multicast DNS) is like a local phonebook for your home network.

**Without mDNS:**
- You type "printer.local" in your browser
- Your computer asks a central DNS server (like Google's 8.8.8.8)
- The central server says "I don't know who printer.local is"

**With mDNS:**
- You type "printer.local" in your browser
- Your computer asks EVERYONE on the network "Who is printer.local?"
- The printer responds "I'm at IP 10.26.4.50!"

---

## What We Saw

We captured mDNS traffic and found these devices:

| IP Address | Device Name | What It Is |
|------------|-------------|------------|
| 10.26.8.72 | Android_FXKTXMGG.local | Android phone/tablet |
| 10.26.3.37 | Unknown | Spotify Connect device |
| 10.26.3.106 | Unknown | Google Cast device |
| 10.26.10.54 | Unknown | Unknown device |
| 10.26.5.213 | Unknown | Unknown device |
| 10.26.11.234 | Unknown | Unknown device |

---

## mDNS Queries We Saw

| Query | What This Means |
|-------|-----------------|
| `_spotify-connect._tcp.local` | Device looking for Spotify Connect speakers |
| `_googlecast._tcp.local` | Device looking for Chromecast/Google Cast |
| `_CC1AD845._sub._googlecast._tcp.local` | Specific Google Cast subtype |

---

## Wireshark Filters We Used

| Filter | What It Does |
|--------|--------------|
| `mdns` | Shows only mDNS protocol packets |
| `mdns.flags.response == 0` | Shows only mDNS QUERIES (asking "Who is X?") |
| `mdns.flags.response == 1` | Shows only mDNS RESPONSES (answering "I am X") |
| `mdns and dns.qry.name contains "googlecast"` | Shows only Google Cast discovery |

---

## Why This Matters for Security

An attacker on your network can use mDNS to:

1. **Map your network** — See every device
2. **Identify users** — "Johns-iPhone.local", "LivingRoom-TV.local"
3. **Target specific devices** — The CEO's laptop vs a guest's phone
4. **See what services you use** — Spotify, Chromecast, printers

---

## How SOC Analysts Use This

We look for:
- Unknown devices appearing on the network
- Unusual mDNS queries
- Devices with suspicious names
- Traffic patterns that don't match normal behavior

---

## Our Intention

We're learning this to:
1. Understand how devices communicate on networks
2. See what information is publicly broadcast
3. Learn what attackers can discover
4. Understand how to monitor networks as a SOC analyst

---

## 🔐 Security Angle

**Real Breach Reference:** In 2023, researchers discovered that mDNS can be used to track users. Attackers on the same network can see all `.local` names being queried, identify devices, and build a map of the network.

**Defense:** Disable mDNS on devices that don't need it. Use a VPN. Use generic device names (not "Johns-iPhone").
