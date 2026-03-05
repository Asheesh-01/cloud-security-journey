# Volume 00 — Interview Prep Questions and Answers

---

## Topic 1 — What is Cloud Security

---

### Q1. What is the Shared Responsibility Model? Give one example of what AWS is responsible for and one example of what the customer is responsible for.

> Try to answer this yourself first before reading below.

**Answer:**
The Shared Responsibility Model defines who is responsible for what in
cloud security. AWS is responsible for securing the physical infrastructure —
servers, data centers, hardware, and the global network. This is called
security OF the cloud. The customer is responsible for everything they build
and configure on top — their data, their IAM permissions, their firewall rules,
their application security. This is called security IN the cloud.

Example of AWS responsibility: physical security of the data center.
Example of customer responsibility: configuring S3 bucket permissions correctly.

---

### Q2. A developer accidentally makes an S3 bucket public. Whose fault is it — AWS or the developer's company? Why?

> Try to answer this yourself first before reading below.

**Answer:**
The customer's fault. AWS provides the tools to secure the bucket correctly.
The customer must configure and use those tools. Under the Shared Responsibility
Model, data protection and access configuration is always the customer's
responsibility. AWS cannot know what your business data is or who should access it.

Real example: Capital One 2019 — misconfigured IAM role, 100 million records
exposed. AWS was not blamed. The customer paid the fine.

---

### Q3. What is meant by "identity is the new perimeter" in cloud security?

> Try to answer this yourself first before reading below.

**Answer:**
In traditional on-premise security, a firewall created a wall between trusted
inside and untrusted outside. In cloud there is no inside. Developers work from
anywhere. Servers run in data centers you never visit. The only security boundary
left is identity — who you are and what you are permitted to do.

If an attacker steals your AWS access key, they become you. No firewall stops them.
No wall blocks them. They walk in with your permissions. This is why IAM is the
most critical skill in cloud security.

Real example: Colonial Pipeline 2021 — one compromised VPN password, no MFA
enabled. Single stolen identity disrupted US fuel supply.

---

### Q4. Name three common cloud misconfigurations and explain the risk of each.

> Try to answer this yourself first before reading below.

**Answer:**
First — S3 bucket set to public. Risk: anyone on the internet can read,
download, or delete your files with no authentication required.

Second — Security group with port 22 open to 0.0.0.0/0. Risk: anyone on
the internet can attempt SSH brute force on your server. You left the door
open for the entire world.

Third — IAM user given admin access when they only needed read access.
Risk: if that account is compromised, the attacker has full control of your
entire AWS environment. Always give minimum access needed — nothing more.

---

### Q5. What is an attack surface? How does cloud increase the attack surface compared to on-premise?

> Try to answer this yourself first before reading below.

**Answer:**
An attack surface is every point where an attacker can attempt to enter your
system. Think of it as every door and window in a building.

On-premise has a small, fixed attack surface — your office network, your
servers, your employees. Everything is inside one boundary.

Cloud attack surface is massive and constantly changing — open APIs, IAM roles,
S3 buckets, public IPs, web applications, human identities. Every time a
developer deploys something new, a new potential door appears.

A cloud security engineer's job is to find every open door and close it
before an attacker walks through.
```

---

**Commit message:**
```
Day 01 - What is Cloud Security - notes, labs, interview prep added