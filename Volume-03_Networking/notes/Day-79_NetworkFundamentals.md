# Day 79 — Network Fundamentals

**Volume:** 03 — Networking From Zero
**Topic:** 1 — Network Fundamentals
**Environment:** Conceptual (no terminal work today)

---

## What It Is

A network is machines connected together able to communicate. Your home WiFi is a network. Office is a network. Internet is largest network.

Networks need:
- Physical connection (cables or wireless)
- Protocols (rules for communication)
- Addressing scheme (IP addresses to identify machines)
- Routing (forwarding data toward destination)

---

## Key Terms

**Network** — group of connected machines able to communicate
**Node** — any device on network (computer, server, printer, router, switch)
**Link** — physical connection between nodes (Ethernet, WiFi)
**Router** — connects different networks, forwards data between them
**Switch** — connects machines within same network
**IP address** — unique identifier for machine on network
**Gateway** — router connecting local network to outside world
**Protocol** — rules both sides must follow to communicate
**Bandwidth** — data flow rate (Mbps, Gbps)
**Latency** — delay in data delivery (milliseconds)
**Packet** — chunk of data sent across network
**Port** — numbered endpoint for communication on a machine

---

## Postal System Analogy

Network = Postal System
- Your computer = Your house
- Another computer = Neighbor's house
- Data = Letter
- IP address = Postal address
- Packet = Individual pieces of letter (if broken up)
- Router = Post office
- Default gateway = Local mailbox

---

## What Happens When You Type google.com

1. Browser asks DNS: "What IP is google.com?" → Receives 142.250.195.46

2. Browser checks: "Is 142.250.195.46 on my local network?" → No

3. Browser sends to default gateway (router)

4. Computer needs router's MAC address. Sends ARP: "Who has 192.168.1.1?" → Router responds

5. Packet travels through Ethernet cable to router wrapped with MAC addresses

6. Router unwraps MAC, checks routing table for 142.250.195.46 → "Send through ISP"

7. Router wraps with new MAC for ISP network, forwards packet

8. Packet travels through: your ISP → internet backbone → Google's ISP → Google's network

9. Google's server receives packet on port 443 (HTTPS)

10. Server processes HTTP request, sends response

11. Response travels same path in reverse

12. Browser receives HTML, renders page

**Total time: ~100 milliseconds**

---

## Security Angle — Man-in-the-Middle Attack

Attacker positions between you and router:
- Uses ARP spoofing: fake ARP reply "I am gateway"
- Your computer sends all traffic to attacker
- Attacker forwards to real gateway
- Attacker reads everything (if no HTTPS encryption)

**What failed:** No encryption. Traffic visible to attacker.
**What stops it:** HTTPS encryption (encrypted even if intercepted).

---

## Real Incident — Target Breach 2013

- Attacker entered via HVAC vendor network
- Network had no segmentation (all on same flat network)
- Attacker mapped network topology using network tools
- Found POS systems accessible from vendor network
- Moved laterally with no firewall in between
- Installed malware on POS systems
- Stole 40 million credit card numbers over months

**Root cause:** Network flat (no segmentation). No firewall between vendor and POS. No network monitoring.