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



---

## Topic 3 — Career Roles & Goal Setting

---

### Q1. What does a SOC Tier 1 Analyst do daily? What is the difference between Tier 1, Tier 2, and Tier 3?

> Try to answer yourself first before reading below.

**Answer:**
A SOC Tier 1 Analyst monitors a SIEM dashboard for suspicious
activity, triages incoming alerts to determine if they are
real attacks or false positives, investigates suspicious
logins, brute force attempts, and malware alerts, and
escalates serious incidents to higher tiers.

SIEM — Security Information and Event Management — is the
platform that collects logs from all systems in one place
and generates alerts when something suspicious happens.

Tier 1 — alert triage and first response, entry level.
Tier 2 — deeper investigation and incident handling,
requires 1 to 2 years experience.
Tier 3 — threat hunting and advanced analysis,
requires 3 plus years experience.

---

### Q2. What is the difference between a SOC Analyst and a Cloud Security Engineer?

> Try to answer yourself first before reading below.

**Answer:**
SOC Analyst is reactive — monitors alerts and investigates
incidents after they are detected. Works across all systems
including cloud, endpoints, and network.

Cloud Security Engineer is proactive — designs and builds
security controls in cloud environments to prevent incidents
from happening. Configures IAM policies, sets up logging,
finds misconfigurations before attackers exploit them.

Key difference — SOC Analyst finds the fire and responds.
Cloud Security Engineer builds the system that prevents fire.

A SOC Analyst can move into cloud security with additional
AWS knowledge. A Cloud Security Engineer already has SOC
fundamentals as part of their foundation.

---

### Q3. What is PICERL? Walk through each stage using ransomware as example.

> Try to answer yourself first before reading below.

**Answer:**
PICERL is the incident response framework every security
team follows when handling a confirmed breach.

Preparation — before ransomware hits, have tools ready,
playbooks written, backups tested, team trained.

Identification — Monday 2AM alert fires. SIEM detects
unusual file encryption activity. Confirm this is ransomware
not a false positive. Incident is declared.

Containment — immediately isolate infected machines from
network. Block attacker's IP addresses. Prevent ransomware
from spreading to other systems.

Eradication — remove the ransomware completely from all
infected systems. Find and eliminate the initial entry
point the attacker used to get in.

Recovery — restore systems from clean backups. Verify
systems are clean before bringing back online. Resume
normal business operations.

Lessons Learned — write full incident report. Document
timeline, root cause, how attacker got in, what they
accessed, what was done to fix it, what to improve.

---

### Q4. What is the difference between a penetration tester and an incident responder?

> Try to answer yourself first before reading below.

**Answer:**
Penetration Tester works before any real attack happens.
They are hired to legally simulate an attack on the
company's own systems, find vulnerabilities, exploit them
in a controlled way, and report what they found with
recommendations to fix everything. Offensive approach.

Incident Responder is called after a real breach has
already happened. They contain the damage, collect
evidence, investigate what the attacker did, recover
systems, and write a report on what happened. Defensive
and reactive approach.

Penetration Tester — before attack, offensive, finds
weaknesses.
Incident Responder — after attack, defensive, stops damage
and recovers.

---

### Q5. You are a fresher applying for SOC Analyst role. What skills would you highlight?

> Try to answer yourself first before reading below.

**Answer:**
Five skills I would highlight as a fresher:

Linux — almost all SOC work happens on Linux. Log reading,
command line investigation, process monitoring all require
Linux fundamentals.

Networking — must understand how packets travel, how
protocols work, how malware enters a network, how to
read network logs and identify suspicious traffic.

Log Analysis — SOC analyst reads logs all day. Windows
Event IDs, Linux auth.log, firewall logs, cloud logs.
Understanding what normal looks like helps spot abnormal.

SIEM knowledge — hands on experience with Wazuh or Splunk.
This is the primary tool of a SOC analyst. Knowing how to
write basic queries and read alerts is essential.

Communication — findings must be written clearly in
incident tickets. Escalations must be precise. Reports
must be readable by both technical and non-technical
stakeholders. English communication is evaluated heavily
at ₹20 plus LPA companies.



---

## Topic 4 — Environment Setup

---

### Q1. What is the difference between root user and IAM user in AWS?
Why should you never use root for daily work?

> Try yourself first.

**Answer:**
Root user is the account created when you first sign up for AWS.
It has unlimited access to everything in the account and cannot
be restricted by any policy. If root credentials are compromised
the attacker can delete the entire account, remove billing alerts,
create hundreds of paid resources, and nothing can stop them
because root overrides all restrictions.

IAM user — Identity and Access Management user — is created by
root with specific limited permissions. Has only the access
needed for the task. If an IAM user is compromised it can be
immediately deactivated from the root account. Damage is limited
and controllable.

Rule: Create one IAM user for daily work. Lock away root
credentials. Enable MFA on root. Never use root again.

---

### Q2. What is an AWS Access Key? What happens if it leaks on GitHub?

> Try yourself first.

**Answer:**
An AWS Access Key consists of two parts — an Access Key ID
which starts with AKIA and a Secret Access Key which is a
longer random string. Together they authenticate the AWS CLI
and SDK to your AWS account from the terminal or from code.
They work like a username and password for programmatic access.

If leaked on GitHub — automated bots scan GitHub every few
minutes specifically looking for exposed AWS keys. Within
minutes of a key appearing on GitHub, bots find it,
authenticate to your account, and immediately spin up expensive
resources — typically GPU instances used for crypto mining.
Students have received bills of $50,000 or more from a single
leaked key. AWS sometimes waives the charge but not always.

Immediate action if leaked:
Go to IAM console, deactivate and delete the key immediately.
Check CloudTrail for any unauthorized activity.
Create a new key. Use git-secrets to prevent future leaks.

---

### Q3. What does aws sts get-caller-identity do? What is an ARN?

> Try yourself first.

**Answer:**
aws sts get-caller-identity asks AWS — who am I authenticated
as right now. AWS responds with three values — your account ID,
your user ID, and your ARN. It is used to verify that AWS CLI
is correctly connected to the right account and the right user.

STS stands for Security Token Service — the AWS service that
handles identity verification and can issue temporary credentials.

ARN stands for Amazon Resource Name. It is a unique identifier
for every single resource in AWS. No two resources have the
same ARN. Format is always:
arn:aws:service::accountnumber:resourcetype/resourcename

Example:
arn:aws:iam::811430801576:user/asheesh01
This identifies IAM user asheesh01 in account 811430801576.

---

### Q4. What is ap-south-1? Why use it as your default region?

> Try yourself first.

**Answer:**
ap-south-1 is the AWS region code for Mumbai, India.

A region is a physical geographic location where AWS has
multiple data centers. AWS has regions across the world —
US, Europe, Asia Pacific, and more.

You choose ap-south-1 because:
It is physically closest to India which means lowest network
latency — your CLI commands and application requests reach
AWS servers faster.
Data stored in Mumbai stays in India which is relevant for
compliance with Indian data protection laws including the
DPDP Act — Digital Personal Data Protection Act.
For Indian users and Indian applications Mumbai region is
always the correct default choice.

---

### Q5. You accidentally commit your AWS Access Key to GitHub.
What are your immediate steps?

> Try yourself first.

**Answer:**
Step 1 — immediately go to AWS IAM console and deactivate
and delete the exposed access key. Not just change it —
delete it completely so it cannot be used at all even if
someone already copied it.

Step 2 — check CloudTrail logs immediately. CloudTrail records
every API call made with your credentials. Look for any
calls you did not make — unusual regions, unusual services,
resource creation. This tells you if the key was already used.

Step 3 — if unauthorized activity is found, immediately revoke
all active sessions, change root account password, verify MFA
is enabled on root, check for any new IAM users created by
attacker, delete any resources created by attacker.

Step 4 — remove the key from Git history completely using
BFG Repo Cleaner or git filter-branch. Deleting the file
and committing again is not enough — Git keeps full history
and the key is still visible in old commits.

Step 5 — create a new access key, configure AWS CLI with
new credentials, install git-secrets on the repository to
prevent this happening again.

Lesson: treat Access Keys like passwords. Never put them
in any file that touches Git.