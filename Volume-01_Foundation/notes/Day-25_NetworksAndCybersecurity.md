# Day 25 — What is a Network + What is Cybersecurity

**Volume:** 01 — Computer and Internet Foundations
**Topics:** 11 and 12

---

## Topic 11 — What is a Network

### Definition
Two or more devices connected to share data and resources.
Node = any connected device. Topology = physical/logical arrangement.
Segment = isolated portion of network. Segmentation limits lateral movement.

### Network Types

**PAN — Personal Area Network**
Range: 10 meters. Bluetooth. Your devices talking to each other.
Security: Bluebugging, BlueSnarfing, BLE attacks on IoT.
Rule: disable Bluetooth when not in use.

**LAN — Local Area Network**
Range: building or campus. Ethernet or WiFi. Shared subnets.
Security: lateral movement, ARP spoofing, SMB spreading (EternalBlue),
nmap scanning entire subnet in seconds.
Defense: segmentation, VLANs, host-based firewalls.

**MAN — Metropolitan Area Network**
Range: city or large campus. Fiber between buildings.
Security: physical fiber taps. Always encrypt inter-building traffic.

**WAN — Wide Area Network**
Range: countries/continents. Internet is the largest WAN.
Security: traffic through third-party infrastructure.
Always encrypt. Use VPNs for corporate WAN over internet.

**WLAN — Wireless LAN**
Range: same as LAN but wireless. WiFi. 802.11 standards.
WPA3 = current standard. WEP = broken, crackable in minutes.
Evil twin attack: fake AP with same SSID, intercept all traffic.
Defense: WPA3, verify SSIDs, HTTPS protects payload even on bad WiFi.

### Network Segmentation

Divides one large network into isolated zones.
VLANs implement logical segmentation without separate hardware.
Traffic between segments must pass through router or firewall.

Target breach 2013: HVAC vendor credentials phished.
Flat network = vendor access reached POS terminals directly.
40 million card numbers stolen.
Proper segmentation: HVAC subnet with firewall blocking POS access.
Segmentation does not prevent compromise — contains blast radius.

---

## Topic 12 — What is Cybersecurity

### Core Definitions

**Asset** — anything valuable needing protection. Data, systems, credentials.

**Threat** — potential cause of harm. WHO or WHAT could attack.
Examples: ransomware group, disgruntled employee, hurricane.

**Vulnerability** — weakness that can be exploited. WHERE or HOW.
Examples: unpatched software, weak passwords, misconfigured firewall.

**Exploit** — technique that takes advantage of a vulnerability.
EternalBlue = exploit. CVE-2017-0144 = vulnerability.

**Risk** — Threat × Vulnerability × Impact.
Unpatched server (vulnerability) + active exploitation (threat) +
no backups (high impact) = high risk.

**Control** — measure to reduce risk.
Preventive: stops attacks. Detective: identifies attacks. Corrective: responds.

**Attack surface** — all points where attacker could enter or extract data.
Reducing attack surface: close unused ports, remove unused accounts,
disable unnecessary services.

**Defense in depth** — multiple security layers. No single control relied upon.
Firewall + EDR + patching + monitoring + MFA = each catches what others miss.

**Least privilege** — minimum permissions needed to perform function.
Backup service: read access to database only, not full filesystem write.

**Zero trust** — never trust, always verify.
Replaces perimeter model. Assumes attackers are already inside.
Every access request authenticated and authorized regardless of source.
Required for cloud where there is no perimeter.

### CIA Triad — Memorize With Attack Examples

**Confidentiality** — only authorized people read the data.
Control: encryption.
Attack: Target 2013 — 40 million card numbers exposed. Confidentiality failure.

**Integrity** — data is accurate and unmodified.
Control: hashing, digital signatures.
Attack: ransomware encrypting files. Integrity failure.
Also: attacker modifying financial records. Integrity failure.

**Availability** — systems accessible when needed.
Control: redundancy, backups, DDoS protection.
Attack: Mirai DDoS on Dyn DNS 2016 — Twitter, Netflix, Reddit offline.
Availability failure.

### Threat vs Vulnerability vs Risk — Interview Answer

Threat = WHO. Vulnerability = WHERE. Risk = probability × impact.

Equifax 2017 example:
- Threat: criminal hackers seeking financial data
- Vulnerability: unpatched Apache Struts (CVE-2017-5638), patch available 2 months prior
- Risk: high — known vulnerability, active exploitation, 147M people's data
- Impact: 147M records exposed. $700M settlement.

### Real Breach — NotPetya 2017

Disguised as ransomware. Actually a cyberweapon — no decryption key.
Spread: EternalBlue + Mimikatz credential harvesting.
Entry: compromised Ukrainian accounting software update server.
Destroyed master boot records — machines completely unbootable.

CIA analysis:
- Confidentiality: minimal impact (data destroyed, not stolen)
- Integrity: violated (all data on infected machines unrecoverable)
- Availability: catastrophic (every infected machine unbootable)

Maersk: 45,000 PCs destroyed, $300M. Merck: $870M. FedEx: $400M.
Total global: ~$10B.
Lesson: availability attacks on critical infrastructure have physical consequences.

---

## Interview Prep — in interview-prep/Vol1_QA.md

**Networks:**
1. LAN vs WAN — security example for each?
2. Network segmentation and Target breach?
3. VLAN vs physical separation?
4. ARP table and what attack targets it?
5. Bandwidth vs latency?

**Cybersecurity:**
6. Threat vs vulnerability vs risk — one breach example covering all three?
7. CIA Triad — one attack violating each property?
8. Defense in depth — why not one strong control?
9. Least privilege — concrete violation example?
10. Zero trust — why it replaced perimeter model?

**Scenarios:**
11. Flat network — how do you make the business case for segmentation?
12. New web app — three CIA risks and one control each?