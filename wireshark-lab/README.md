# Wireshark Lab: Network Security Analysis

**Date:** July 28, 2026  
**Environment:** Ubuntu 24.04, Wireshark 4.2.2

---

## Lab Overview

This repository documents my complete Wireshark journey from a complete beginner to performing real network attacks on my home network. All experiments were conducted on my own network for educational purposes.

---

## Network Details

| Detail | Value |
|--------|-------|
| **IP Address** | `10.26.4.216` |
| **Router IP** | `10.26.0.1` |
| **Network Range** | `10.26.4.0/20` |
| **Interface** | `wlp85s0f0` |

---

## Modules

| # | Module | What I Learned |
|---|--------|----------------|
| 01 | [Installation](1-installation.md) | Installing Wireshark, fixing permissions |
| 02 | [First Capture](2-first-capture.md) | Capturing live traffic, basic filters |
| 03 | [HTTP Credentials](3-http-credentials.md) | Finding plaintext credentials in Wireshark |
| 04 | [mDNS Analysis](4-mdns-analysis.md) | Understanding network discovery |
| 05 | [SSL Stripping](5-ssl-stripping.md) | Forcing HTTP with BetterCap |
| 06 | [MITMProxy](6-mitmproxy.md) | HTTPS decryption attempts |

---

## Tools Used

| Tool | Purpose |
|------|---------|
| **Wireshark** | Packet capture and analysis |
| **BetterCap** | ARP spoofing and network manipulation |
| **MITMProxy** | HTTPS traffic decryption |
| **curl** | Generating test HTTP/HTTPS traffic |
| **openssl** | Certificate generation |

---

## Security Principles Learned

1. **Defense in Depth** — Never rely on a single security layer
2. **Principle of Least Privilege** — Give only necessary permissions
3. **HTTPS Everywhere** — Always use encryption
4. **HSTS** — Force HTTPS to prevent downgrade attacks
5. **MFA** — Even if credentials are stolen, account is protected
6. **VPN** — Encrypt all traffic end-to-end

---

## ⚠️ Disclaimer

All experiments were performed on **my own home network** for educational purposes. These techniques should **never** be used on networks you don't own or without explicit permission.