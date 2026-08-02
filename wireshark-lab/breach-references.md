# REAL BREACH REFERENCES

---

## Why We Study These

We study real breaches to understand:
1. How attacks happen in the real world
2. What vulnerabilities existed
3. How they could have been prevented
4. What to look for as SOC analysts

---

## 2010 - Robert Graham Demo

| Detail | Information |
|--------|-------------|
| **What happened** | Security researcher demonstrated Wi-Fi sniffing |
| **Technique used** | Packet capture on open Wi-Fi |
| **Impact** | 30% of users had credentials stolen in 1 hour |
| **How it worked** | Users visited HTTP sites on open Wi-Fi |
| **Prevention** | HTTPS everywhere, VPN |

---

## 2011 - HBGary Federal

| Detail | Information |
|--------|-------------|
| **What happened** | Security company breached |
| **Technique used** | Root access + packet sniffing |
| **Impact** | Internal network traffic captured |
| **How it worked** | Attackers got root access then sniffed traffic |
| **Prevention** | Least privilege, proper access controls |

---

## 2015 - Ashley Madison

| Detail | Information |
|--------|-------------|
| **What happened** | Dating site breached |
| **Technique used** | Open Wi-Fi + credential theft |
| **Impact** | 37 million records exposed |
| **How it worked** | Employees used open Wi-Fi near office |
| **Prevention** | VPN, never use open Wi-Fi for work |

---

## 2015 - Lenovo Superfish

| Detail | Information |
|--------|-------------|
| **What happened** | SSL Stripping certificate pre-installed |
| **Technique used** | SSL Stripping with trusted certificate |
| **Impact** | 100,000+ laptops affected |
| **How it worked** | Pre-installed certificate intercepted all HTTPS |
| **Prevention** | Never pre-install non-enterprise certificates |

---

## 2017 - Equifax

| Detail | Information |
|--------|-------------|
| **What happened** | Major credit reporting agency breached |
| **Technique used** | Unencrypted HTTP traffic |
| **Impact** | 147 million records exposed |
| **How it worked** | Internal traffic was sent over HTTP |
| **Prevention** | Encrypt all internal traffic, HTTPS everywhere |

---

## 2017 - WannaCry Ransomware

| Detail | Information |
|--------|-------------|
| **What happened** | Global ransomware attack |
| **Technique used** | Unencrypted traffic interception |
| **Impact** | 150+ countries affected |
| **How it worked** | Worms spread via unpatched systems |
| **Prevention** | Regular patching, network segmentation |

---

## 2018 - MyEtherWallet

| Detail | Information |
|--------|-------------|
| **What happened** | Cryptocurrency wallet service |
| **Technique used** | DNS Spoofing |
| **Impact** | $150,000+ in cryptocurrency stolen |
| **How it worked** | Users were redirected to fake site |
| **Prevention** | DNSSEC, HSTS |

---

## 2021 - Colonial Pipeline

| Detail | Information |
|--------|-------------|
| **What happened** | Major US pipeline ransomware attack |
| **Technique used** | Password hash capture + cracking |
| **Impact** | US gas shortage, panic buying |
| **How it worked** | Captured password hashes from network |
| **Prevention** | MFA, least privilege, regular password changes |

---

## 2021 - Mimecast

| Detail | Information |
|--------|-------------|
| **What happened** | Email security company breached |
| **Technique used** | Certificate Forgery |
| **Impact** | Major email security company breached |
| **How it worked** | Fake certificates intercepted encrypted email |
| **Prevention** | Certificate pinning, strict validation |

---

## 2022 - Optus

| Detail | Information |
|--------|-------------|
| **What happened** | Australia's second-largest telecom |
| **Technique used** | ARP Spoofing |
| **Impact** | 10 million customer records exposed |
| **How it worked** | Attackers on internal network |
| **Prevention** | Network monitoring, ARP detection |

---

## 2022 - Uber

| Detail | Information |
|--------|-------------|
| **What happened** | Ride-sharing company breached |
| **Technique used** | Session Hijacking |
| **Impact** | Full internal network access |
| **How it worked** | 18-year-old stole contractor's session cookie |
| **Prevention** | MFA, session timeout, HttpOnly cookies |

---

## 2023 - Okta

| Detail | Information |
|--------|-------------|
| **What happened** | Major identity provider breached |
| **Technique used** | Credential Harvesting (Phishing) |
| **Impact** | 16,000+ user credentials stolen |
| **How it worked** | Phishing emails with fake login pages |
| **Prevention** | MFA, user education, HSTS |

---

## 2023 - DEF CON

| Detail | Information |
|--------|-------------|
| **What happened** | Security conference research |
| **Technique used** | ARP Spoofing vulnerability |
| **Impact** | 85% of hotel Wi-Fi networks vulnerable |
| **How it worked** | Tested multiple hotel networks |
| **Prevention** | VPN, client isolation, network monitoring |

---

## Key Takeaways

| # | Lesson |
|---|--------|
| 1 | HTTPS is essential but not foolproof |
| 2 | Certificate validation is critical |
| 3 | MFA prevents credential theft |
| 4 | Network monitoring detects attacks |
| 5 | Defense in Depth is essential |
| 6 | Users are always the weakest link |
| 7 | Regular patching is non-negotiable |