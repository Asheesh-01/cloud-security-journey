# Day 01 — Cybersecurity Basics for Cloud Security

## Goal (Why this day matters)
Cloud Security is built on cybersecurity fundamentals.
If I don’t understand core security concepts, IAM + cloud logs will feel confusing later.

---

## 1) Core Terms (Must Know)

### 1.1 Asset
Anything valuable that must be protected.
Examples:
- Data, servers, credentials, cloud account, API keys, logs

Cloud examples:
- S3 bucket data
- IAM admin role
- CloudTrail logs

---

### 1.2 Threat
Something that can cause harm.
Examples:
- Attacker, malware, insider, bot, phishing campaign

Cloud examples:
- Stolen AWS keys used to log in
- Automated scanners looking for public buckets

---

### 1.3 Vulnerability
A weakness that can be exploited.
Examples:
- Weak password
- Unpatched OS
- Public storage
- Open SSH to the world

Cloud examples:
- S3 bucket public access misconfiguration
- Security Group: 0.0.0.0/0 on port 22
- IAM policy with "*" permissions

---

### 1.4 Exploit
The method used to take advantage of a vulnerability.
Examples:
- Using leaked credentials
- Exploiting weak config
- Abuse of exposed ports/services

---

### 1.5 Risk
Risk = Threat × Vulnerability × Impact

Example:
- Vulnerability: public bucket
- Threat: attacker scanning
- Impact: data leak
= High risk

---

## 2) CIA Triad (Security Backbone)

### Confidentiality
Only authorized people can access data.
Cloud mapping:
- Private buckets, least privilege IAM, encryption, MFA

### Integrity
Data should not be altered without authorization.
Cloud mapping:
- Versioning, signing, audit logs, approvals

### Availability
System should stay accessible.
Cloud mapping:
- Scaling, redundancy, DDoS protection, multi-AZ

---

## 3) Authentication vs Authorization (Cloud-critical)

### Authentication (Who are you?)
Examples:
- Password, MFA, SSH key, access keys

### Authorization (What can you do?)
Examples:
- IAM policy permissions, RBAC roles

Cloud reality:
- Most breaches = authorization mistakes (too much permission)

---

## 4) Malware Basics (What I need to know as a defender)
Malware = malicious software.

Common types:
- Virus, Worm, Trojan, Ransomware, Spyware, Adware, Rootkit, Botnet

Key point:
- Antivirus scanning is mostly **signature-based**
- Advanced attacks can bypass signatures → logs + behavior detection matter more

---

## 5) Practical Lab — ClamAV (Signature-Based Scanning)

### Tools Used
- clamav, clamscan, freshclam

### Install (Debian/Kali/Ubuntu)
```bash
sudo apt update
sudo apt install clamav -y