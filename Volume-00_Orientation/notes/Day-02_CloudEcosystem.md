# Day 02 — Cloud Ecosystem & Service Models

**Date:** [07-03-26]
**Volume:** 00 — Orientation
**Topic:** Understanding the Cloud Ecosystem

---

## What I Learned Today

The entire cloud market is dominated by three platforms —
AWS, Azure, and GCP. As a cloud security engineer you will
work across all three but must master AWS first for Indian
job market entry.

---

## The Three Cloud Providers

### AWS — Amazon Web Services
- Founded: 2006 — oldest and most established
- Market share: approximately 32% globally
- Known for: most mature security tooling, largest ecosystem,
  most job postings in India
- Used by: Indian startups, fintechs, e-commerce
- Examples: Paytm, Razorpay, Swiggy, Zomato, Flipkart
- Security tools: CloudTrail, GuardDuty, Security Hub, IAM
- India regions: Mumbai (ap-south-1), Hyderabad (ap-south-2)

### Azure — Microsoft Azure
- Owner: Microsoft
- Market share: approximately 22% globally
- Known for: enterprise and government sector dominance
- Used by: large IT companies and their enterprise clients
- Examples: TCS, Wipro, Infosys, HCL client workloads
- Security tools: Microsoft Sentinel (widely used SIEM),
  Azure Entra ID (identity management)
- India data centers: Pune, Chennai

### GCP — Google Cloud Platform
- Owner: Google
- Market share: approximately 11% globally
- Known for: AI, machine learning, data analytics
- Used by: AI and ML focused startups
- Growing fast in India
- Security tools: Strong container and Kubernetes security
- India data centers: Mumbai, Delhi

---

## Global Infrastructure

### Regions
A Region is a physical geographic location where a cloud
provider has built data centers.

Example — AWS ap-south-1 = Mumbai region. Data stored here
physically sits on servers in Mumbai.

Why it matters for security — Indian regulations like RBI
guidelines require certain financial data to stay within
Indian borders. Choosing the wrong region = compliance failure.

### Availability Zones (AZ)
Availability Zones are subdivisions inside a region.
Each region has 2 to 3 AZs minimum.
Each AZ is one or more data centers with independent
power, cooling, and networking.

AWS Mumbai region has 3 AZs.

Why it matters for security — if one AZ goes down due to
fire, flood, or attack, your application keeps running in
another AZ. Service stays available even during incidents.

Rule to remember:
Region = the city
Availability Zone = individual building inside that city

---

## Cloud Service Models

### IaaS — Infrastructure as a Service

Provider gives you: virtual machine, storage, networking
You manage: OS, applications, data, security configuration
Responsibility: highest on your side
Control: highest on your side
Example: AWS EC2

Security implication — you configure the firewall, you
patch the OS, you manage all access. One wrong setting
and you are exposed.

### PaaS — Platform as a Service

Provider gives you: infrastructure, OS, runtime environment
You manage: application code and data only
Responsibility: shared, more towards provider for infrastructure
Control: medium
Example: AWS RDS, AWS Elastic Beanstalk

Security implication — AWS patches the database engine
in RDS. You patch your application. Know the boundary.

### SaaS — Software as a Service

Provider gives you: complete working software
You manage: your data and who has access
Responsibility: mostly provider
Control: lowest
Example: Gmail, Office 365, Salesforce

Security implication — if you give wrong person access
to your company Gmail account, that is your fault.
Provider cannot control who you give access to.

### The Core Rule

More control = more responsibility
Less control = less responsibility but also less flexibility

| Model | Provider Manages | You Manage |
|-------|-----------------|------------|
| IaaS | Hardware, network | OS, apps, data, security |
| PaaS | Hardware, OS, runtime | App code, data |
| SaaS | Everything | Data and access only |

---

## Multi-Cloud Strategy

Using two or more cloud providers simultaneously.

### Why Companies Use It
- Avoid vendor lock-in — not dependent on one provider
- Use best service from each provider
- Regulatory requirements — data must stay in specific regions
- Redundancy — if one provider goes down, others handle load

### Security Challenge It Creates
- Misconfigurations can happen across multiple platforms
- Each cloud has different security tools and IAM models
- Security engineer must monitor all clouds simultaneously
- More platforms = larger attack surface = more complexity

---

## Indian Job Market Reality

| Target Company Type | Primary Cloud | What to Learn |
|--------------------|--------------|---------------|
| Startups, Fintechs | AWS | AWS deep knowledge |
| TCS, Wipro, Infosys | Azure | Azure awareness |
| AI/ML Startups | GCP | GCP awareness |
| MSSP Companies | All three | Multi-cloud monitoring |

Key insight — AWS knowledge gets you the most fresher
job opportunities in India. This is why the roadmap
focuses on AWS first in Volume 07.

---

## Key Lessons

1. Three providers dominate — AWS, Azure, GCP
2. AWS = most jobs in India for fresher cloud security
3. Service model determines who is responsible for security
4. Managed services (PaaS, SaaS) = provider patches infrastructure
5. Multi-cloud = more resilience but bigger security challenge
6. Region and AZ choices affect compliance and availability

---

## Connection to Previous Topics

Topic 1 established the Shared Responsibility Model.
Topic 2 shows where that model applies differently —
IaaS gives you most responsibility, SaaS gives you least.
The cloud ecosystem is the playground. The Shared
Responsibility Model are the rules of that playground.