# Day 27 — CIA Triad: Confidentiality

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 15 — Basic Security Principles
**Subtopic:** 1 — Confidentiality

---

## What It Is

Only authorized people can read the data.
Violation = unauthorized disclosure — intentional or accidental.

## Key Terms

**Encryption** — primary technical control. Scrambles data, key needed to read.
**Access control** — rules defining who accesses what (permissions, IAM, NTFS).
**Data classification** — Public, Internal, Confidential, Restricted.
**Need to know** — access only to data required for current task.
**Data at rest** — stored data. Protect with disk encryption (BitLocker, LUKS).
**Data in transit** — moving data. Protect with TLS/HTTPS.
**Data in use** — in memory. Hardest to protect. Target of memory scraping attacks.

## How Confidentiality Is Broken

**Eavesdropping** — sniffing unencrypted network traffic (HTTP, FTP, Telnet).
**Credential theft** — phishing, keyloggers, credential stuffing, password spray.
**Misconfiguration** — public S3 bucket, open Elasticsearch, exposed database port.
**Insider threat** — authorized users abusing access. Tesla 2018, Snowden.
**Physical access** — stolen laptop/drive without disk encryption = full data access.

## Real Breach — Equifax 2017

CVE-2017-5638 — Apache Struts. Patch available March 2017. Not applied.
76 days undetected. 147 million records exposed.
SSNs, birth dates, addresses, 209,000 credit card numbers.
Failed controls: no patching, no segmentation, no encryption at rest,
no traffic inspection.
Settlement: $700M. CEO resigned. CSO charged with insider trading.

## Commands
```bash
ss -tlnp                                    # listening services
curl -v http://example.com 2>&1 | head -50  # HTTP plaintext
curl -v https://example.com 2>&1 | head -50 # HTTPS encrypted
ls -la /etc/shadow                          # permission check
ls -la /etc/passwd                          # permission check
cat /etc/shadow                             # should fail - permission denied
sudo cat /etc/shadow | head -5              # authorized root access
find /etc -readable -not -group root -name "*.conf" 2>/dev/null | head -10
```