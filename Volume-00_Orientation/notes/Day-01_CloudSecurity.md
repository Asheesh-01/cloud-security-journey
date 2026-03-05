# Day 01 — What is Cloud Security

**Date:** [fill today's date]
**Volume:** 00 — Orientation
**Topic:** What is Cloud Security

---

## What is Cloud Security

Cloud security is the practice of protecting everything that lives in the cloud —
data, applications, networks, and identities — from attack, misuse, and accidental
exposure.

Cloud = renting computing hardware from providers like AWS, Azure, or GCP.
You pay for what you use. You access it from anywhere via the internet.
You never see or touch the physical machine.

The core question in cloud security is not what cloud is.
It is — who is responsible when something goes wrong.

---

## Key Concepts

### Shared Responsibility Model

Defines who is responsible for what between the cloud provider and the customer.

| Responsibility | AWS | Customer |
|---------------|-----|----------|
| Physical servers and data centers | ✅ | ❌ |
| Network infrastructure | ✅ | ❌ |
| IAM permissions and roles | ❌ | ✅ |
| Data protection | ❌ | ✅ |
| Firewall rules and security groups | ❌ | ✅ |
| Application security | ❌ | ✅ |

**Rule:** AWS secures the cloud. You secure everything you build in the cloud.

**Real breach:** Capital One 2019 — misconfigured IAM role exposed
100 million customer records. AWS was not at fault. The customer
misconfigured their own permissions.

---

### Cloud vs On-Premise

| Factor | On-Premise | Cloud |
|--------|-----------|-------|
| Hardware ownership | You own it | Provider owns it |
| Physical access | You control it | Provider controls it |
| Security boundary | Your office network | Identity and configuration |
| Attack surface | Small, fixed | Large, always changing |

---

### Identity is the New Perimeter

Old model — firewall wall between trusted inside and untrusted outside.

Cloud model — no wall. Developers work from anywhere. Servers run in
data centers you never visit. The only security boundary is identity.

If an attacker steals your AWS access key, they become you.
No firewall stops them. IAM is the new wall.

**Real breach:** Colonial Pipeline 2021 — one compromised VPN password,
no MFA enabled. Single stolen identity shut down US fuel supply.

---

### Misconfiguration Risks

99% of cloud security failures are caused by the customer, not the provider.
Almost all are misconfigurations.

Common misconfigurations:
- S3 bucket set to public — anyone on internet can access your files
- Port 22 open to 0.0.0.0/0 — entire internet can attempt SSH brute force
- IAM user given admin access when only read access was needed
- CloudTrail disabled — no logs, attacker moves invisibly
- MFA not enabled on root account — one stolen password = full account takeover

---

### Cloud Attack Surface

Attack surface = every point where an attacker can attempt to enter.

On-premise: small, fixed — office network, servers, employees only.

Cloud: massive, always changing — APIs, IAM roles, S3 buckets,
public IPs, web applications, human identities. Every new deployment
adds new potential entry points.

A cloud security engineer maps and reduces this surface continuously.

---

## Why This Matters for My Career

Every SOC alert in a cloud environment connects back to something
misconfigured or left exposed. Cannot investigate cloud alerts without
understanding the Shared Responsibility Model and where responsibility starts.

SOC Analyst and Blue Team roles require this as foundation knowledge.
Every cloud security interview starts with Shared Responsibility Model.

---

## Connection to Previous Topics

This is Day 01. Foundation of everything that follows.
Volume 01 will build the technical fundamentals underneath this.
Volume 07 will go deep into AWS security implementation.