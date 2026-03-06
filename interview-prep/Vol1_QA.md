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


---

## Topic 2 — Cloud Ecosystem & Service Models

---

### Q1. What are the three major cloud providers and what is each known for? Which dominates Indian startups?

> Try to answer yourself first before reading below.

**Answer:**
AWS — Amazon Web Services. Founded 2006. Market leader with
32% global share. Most mature security tooling. Dominates
Indian startups and fintechs. Most fresher cloud security
job postings in India require AWS knowledge.

Azure — Microsoft Azure. Second largest. Dominates enterprise
and government sector. Large IT companies like TCS, Wipro,
Infosys use Azure for their enterprise clients.

GCP — Google Cloud Platform. Third largest. Strong in AI,
machine learning, and data analytics. Growing fast in Indian
startups building on AI and ML.

AWS dominates Indian startups. Azure dominates enterprise.
GCP dominates AI and ML workloads.

---

### Q2. What is a Region and what is an Availability Zone? How are they different?

> Try to answer yourself first before reading below.

**Answer:**
A Region is a physical geographic location where a cloud
provider has built data centers. Example — AWS ap-south-1
is Mumbai. Data stored here physically sits on servers in
Mumbai. Matters for compliance — Indian regulations require
certain data to stay within Indian borders.

An Availability Zone is a subdivision inside a region.
Each region has 2 to 3 AZs minimum. Each AZ is one or more
data centers with completely independent power, cooling,
and networking. If one AZ fails due to fire or attack,
others keep running and your service stays available.

Difference — Region is the city. AZ is the individual
building inside that city.

---

### Q3. What is the difference between IaaS, PaaS, and SaaS? Give one real example of each.

> Try to answer yourself first before reading below.

**Answer:**
IaaS — Infrastructure as a Service. Provider gives virtual
machine, storage, networking. You manage OS, applications,
data, and all security. Most control, most responsibility.
Example — AWS EC2. You get a server, install everything yourself.

PaaS — Platform as a Service. Provider gives infrastructure,
OS, runtime. You manage application code and data only.
Example — AWS RDS. AWS manages the database engine and OS.
You manage data and the application connecting to it.

SaaS — Software as a Service. Provider gives complete
working software. You manage only data and access.
Example — Gmail. Google manages everything. You manage
your emails and who can access your account.

Core rule — more control equals more responsibility.

---

### Q4. Your company uses AWS RDS. A vulnerability is found in the database engine. Who patches it?

> Try to answer yourself first before reading below.

**Answer:**
AWS patches it. AWS RDS is a managed database service
which makes it PaaS. Under PaaS, AWS is responsible for
the database engine, OS, and underlying infrastructure.
Your team never touches the underlying engine.

Your team is only responsible for the application layer
that connects to RDS and the data stored inside it.

This is the core value of managed services — you trade
control for reduced operational and security responsibility
on the infrastructure layer.

---

### Q5. What is multi-cloud strategy? Why do companies use it and what security challenge does it create?

> Try to answer yourself first before reading below.

**Answer:**
Multi-cloud strategy means using two or more cloud providers
simultaneously. Example — AWS for compute, GCP for AI
workloads, Azure for enterprise identity management.

Companies use it for four reasons:
1. Avoid vendor lock-in
2. Use best service from each provider
3. Regulatory compliance requirements
4. Redundancy — if one provider fails, others handle load

Security challenge — misconfigurations can happen across
multiple platforms simultaneously. Each cloud has different
security tools, different IAM models, different logging.
Security engineer must monitor all clouds at once. More
platforms equals larger attack surface and more complexity.