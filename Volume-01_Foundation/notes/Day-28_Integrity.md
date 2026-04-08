# Day 28 — CIA Triad: Integrity

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 15 — Basic Security Principles
**Subtopic:** 2 — Integrity

---

## What It Is

Data is accurate, complete, and unmodified by unauthorized parties.
Question integrity answers: has this data been changed and can I trust it?
You can have perfect confidentiality and still have an integrity failure.

## Key Terms

**Hash** — fixed-length fingerprint of data. Same input = same hash always.
Any change = completely different hash. SHA-256 produces 64-character output.

**Digital signature** — hash encrypted with private key.
Proves integrity (unchanged) and authenticity (from correct sender).

**Non-repudiation** — sender cannot deny sending. Digital signatures provide this.

**File Integrity Monitoring (FIM)** — monitors critical files, alerts on changes.
Wazuh has FIM. Monitor: /etc/passwd, /etc/shadow, /bin/, /usr/bin/.

**WORM storage** — Write Once Read Many. Cannot modify after writing.
Used for audit logs and financial records.

**Tamper-evident** — modifications leave detectable traces.
Audit logs must be tamper-evident.

## How Integrity Is Broken

**Data tampering** — directly modifying records, logs, or files.
**MITM** — intercepting and modifying data in transit.
**Malware** — ransomware encrypts, rootkits replace binaries, viruses attach.
**Log tampering** — deleting/editing logs to erase attack evidence.
**Supply chain** — compromising software before it reaches users.

## Real Breach — SolarWinds 2020

APT29 (Russian SVR) compromised SolarWinds build environment.
Inserted SUNBURST backdoor into Orion software during compilation.
Software signed with legitimate certificate — all integrity checks passed.
18,000 organizations installed malicious update.
US Treasury, Commerce, Homeland Security, State Department compromised.

Lesson: digital signature proves match with what signer released.
Does not prove signer was not compromised.
Build process integrity must be protected. Led to SBOM requirements.

## Commands

```bash
echo "data" > /tmp/testfile.txt
sha256sum /tmp/testfile.txt                 # baseline hash
sha256sum /tmp/testfile.txt > /tmp/testfile.sha256
sha256sum -c /tmp/testfile.sha256           # verify - OK
echo "tampered" > /tmp/testfile.txt
sha256sum -c /tmp/testfile.sha256           # verify - FAILED
sha256sum /bin/ls /bin/bash /usr/bin/python3  # system binary baseline
echo "test" | md5sum                        # 128-bit, deprecated
echo "test" | sha256sum                     # 256-bit, current standard
```