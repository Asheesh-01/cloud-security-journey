# Day 26 — Types of Cyber Threats + Hacker Types

**Volume:** 01 — Computer and Internet Foundations
**Topics:** 13 and 14

---

## Topic 13 — Types of Cyber Threats

### Malware — Umbrella Term
Any software designed to harm, gain unauthorized access, or disrupt.
Payload = what malware does after delivery.
C2 = command and control server malware calls home to.
Persistence = surviving reboots (registry, scheduled tasks, services).
Propagation = how malware spreads.
Obfuscation = hiding from antivirus through encoding, packing, encryption.
Zero-day = unknown vulnerability with no patch — extremely valuable.

### Virus
Attaches to legitimate files. Spreads when files are shared.
Requires user action — someone must open/run the infected file.
Example: ILOVEYOU 2000 — email attachment VBS script.
Hidden extension (.TXT.vbs showed as .TXT). 45M machines. $10B damage.
Defense: show file extensions, email attachment filtering, user awareness.

### Worm
Self-replicating. Spreads through networks autonomously.
No user action required — exploits vulnerabilities in network services.
Example: WannaCry 2017 — EternalBlue, port 445, 230,000 machines, 24 hours.
Example: Slammer 2003 — SQL Server vulnerability, 75,000 machines in 10 minutes.
Defense: patch management, network segmentation, block unnecessary ports.

### Trojan
Disguised as legitimate software. Does not self-replicate.
Tricks users into installing voluntarily through social engineering.
RAT (Remote Access Trojan) = full remote control, keylogging, webcam access.
Example: Operation Aurora 2010 — zero-day IE exploit, targeted Google and 30 others.
Defense: application whitelisting, code signing, sandboxing unknown files.

### Ransomware
Encrypts files with asymmetric encryption. Demands cryptocurrency payment.
Public key encrypts. Private key (attacker holds) decrypts.
Double extortion: exfiltrate before encrypt. Backups alone insufficient.
Example: Colonial Pipeline 2021 — DarkSide, VPN credential, no MFA.
  45% US East Coast fuel supply disrupted. $4.4M paid.
Example: WannaCry 2017 — UK NHS. 19,000 appointments cancelled.
Defense: MFA on all remote access, offline backups, network segmentation, patching.

### Spyware
Silently collects data and transmits to attacker.
Keyloggers, screen capture, credential stealers, banking trojans.
Example: Pegasus — NSO Group, zero-click iOS/Android exploit.
  Missed WhatsApp call = full compromise. Used against journalists, activists.
Defense: keep devices patched, avoid unknown software, use encrypted messaging.

### Botnet
Network of compromised machines (bots/zombies) controlled via C2 server.
Used for DDoS, spam, cryptocurrency mining, credential stuffing.
Beaconing: bot connects to C2 at regular intervals — detection indicator.
Example: Mirai 2016 — 600,000 IoT devices, default credentials, port 23.
  DDoS against Dyn DNS — 1.2 Tbps — Twitter, Netflix, Reddit offline.
Example: Emotet — banking trojan → botnet delivery service.
  Delivered TrickBot, Ryuk ransomware. Taken down Jan 2021.
Defense: C2 beaconing detection, block unknown outbound connections, IoT default credential policy.

### ClamAV Commands
```bash
sudo apt install clamav clamav-daemon -y
sudo systemctl stop clamav-freshclam
sudo freshclam
sudo systemctl start clamav-freshclam
clamscan -r /home/kali
clamscan -r /home/kali --verbose --log=/tmp/clamav_scan.log
clamscan -r /tmp --verbose
cat /tmp/clamav_scan.log
```
Signature-based — detects known threats only.
Misses obfuscated, packed, or new malware variants.

---

## Topic 14 — Hacker Types

### Black Hat
Motivation: financial gain, malice, ideology. No authorization. Illegal.
Example: REvil — ransomware-as-a-service. Kaseya attack hit 1,500 businesses.
  JBS meat processor paid $11M ransom.
SOC relevance: primary threat you defend against daily.

### White Hat
Motivation: improve security. Explicit written authorization.
Penetration testers, bug bounty researchers, red team operators.
Bug bounties: Google paid $12M in 2022. Legal, ethical, paid.
Certifications: CEH, OSCP (gold standard), GPEN, eJPT.
Roadmap: Volume 11 Option C leads here.

### Gray Hat
No authorization but no malicious intent.
Reports vulnerabilities after unauthorized access.
Still illegal — unauthorized access regardless of intent.
Example: Verkada camera network 2021 — researcher accessed without permission,
  exposed 150,000 cameras at hospitals, prisons, Tesla. Faced criminal charges.

### Script Kiddie
No understanding of tools being used. Downloads and runs pre-built exploits.
Still dangerous: automated tools scan millions of IPs constantly.
Your exposed SSH port is being brute forced right now by automated tools.
Defense: basic hardening stops script kiddies — disable root SSH,
  use key authentication, fail2ban, close unused ports.

### Hacktivist
Motivation: political, social, or ideological cause.
Anonymous: OpPayback (PayPal, Visa, Mastercard), OpISIS.
IT Army of Ukraine 2022: DDoS against Russian infrastructure.
Attacks: usually DDoS, defacement, data leaks. Lower persistence goal.
SOC response differs: focus on availability protection, not persistence hunting.

### Nation State
Motivation: espionage, sabotage, economic advantage, geopolitical goals.
Resources: unlimited budget, zero-days, years of patience, specialized teams.
Characteristics: stealth, patience, specific targeting, deniability.

APT29 (Cozy Bear) — Russian SVR. SolarWinds 2020. 9 months undetected.
APT41 — Chinese. Espionage + financial crime simultaneously.
Lazarus Group — North Korea. Bangladesh Bank $81M. WannaCry. Crypto theft.
Equation Group — believed NSA. Stuxnet. EternalBlue.

Tools trickle down: NSA zero-day → EternalBlue → WannaCry in 6 weeks.
Defense: detection-focused. Prevention alone fails against nation-state resources.

---

## Interview Prep — in interview-prep/Vol1_QA.md

**Threats:**
1. Virus vs worm — which is more dangerous and why?
2. Ransomware double extortion — why backups alone are insufficient?
3. Botnet + C2 — how does beaconing detection work?
4. RAT vs standard trojan?
5. Colonial Pipeline — what single control would have prevented it?

**Hackers:**
6. Black hat vs gray hat — can gray hat face charges?
7. Script kiddie — why still a real threat?
8. Nation-state example + attributed operation?
9. What makes nation-state more dangerous than criminals?
10. Bug bounty programs — relation to white hat?

**Scenarios:**
11. Machine beaconing to same IP every 60 seconds for 6 hours?
12. 100% CPU, unknown process — investigation order?