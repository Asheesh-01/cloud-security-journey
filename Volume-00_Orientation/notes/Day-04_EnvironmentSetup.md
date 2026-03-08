# Day 04 — Environment Setup

**Date:** [fill today's date]
**Volume:** 00 — Orientation
**Topic:** Complete Environment Verification and Configuration

---

## Why This Topic Exists

A broken or incomplete environment wastes hours of learning time.
Before going deeper into any technical volume every tool must be
installed, configured correctly, and verified working.
Environment discipline is a professional habit.

A security engineer with a broken environment cannot investigate
incidents, cannot run scans, cannot build tools.

---

## Tool 1 — Ubuntu and Kali VM

Ubuntu is the main operating system.
Kali Linux is a security focused Linux distribution running as a
virtual machine inside Ubuntu.

Kali comes pre-installed with hundreds of security tools:
nmap, Wireshark, Burp Suite, and many more.

Why VM and not directly on Ubuntu:
Running security tools directly on main system is risky.
If a tool crashes or misconfigures something it affects the
entire system. Kali VM is isolated — if something breaks
inside it Ubuntu stays safe. This is how professional security
engineers work — isolated lab environments for all testing.

Verified output:
- Kernel: Linux 6.18.9 kali-amd64
- OS: Kali GNU/Linux Rolling 2025.4
- User: krishna (not root — correct)
- RAM available: 3.4GB
- Disk free: 24GB on main partition

Status: VERIFIED

---

## Tool 2 — Git and GitHub

Git is a version control system.
It tracks every change made to every file over time.
You can see what changed, when it changed, and go back to
any previous version.

GitHub is a website that hosts Git repositories online.
Local Git tracks changes. GitHub stores them in the cloud
so recruiters can find your work from anywhere.

Why this matters for career:
GitHub contribution graph — green squares showing daily
commits — is the first thing recruiters check before reading
your resume. An empty graph means you are not serious.
A 180 day streak with real security content means you are.

Configuration verified:
- Git version: 2.53.0
- user.name: Asheesh Sharma
- user.email: asheeshsharma0001@gmail.com
- Last 5 commits confirmed active

Important: Email in git config must match GitHub account email.
If they do not match commits do not show as green squares on
contribution graph.

Status: VERIFIED

---

## Tool 3 — VS Code

VS Code — Visual Studio Code — is a code editor made by Microsoft.
It is the most widely used editor by developers and security
engineers worldwide.

Used for:
- Writing notes in Markdown format
- Writing Python security scripts
- Editing configuration files
- Viewing and managing entire repo folder structure

Useful command:
From any project folder type: code .
This opens VS Code with that folder loaded in the sidebar.
The dot means current directory.

Version verified: 1.109.5
Location: /snap/bin/code

Status: VERIFIED

---

## Tool 4 — Python 3

Python is the primary scripting language for security engineering.
Used for log parsing, automation, AWS SDK boto3 calls,
building security tools, and writing detection scripts.

boto3 — the official AWS SDK for Python. SDK stands for
Software Development Kit. It is a library that lets you
control AWS services from Python code.

Versions verified:
- Python 3.12.3
- pip 25.2 (pip is the package manager for Python —
  installs third party libraries)

Note: Two Python versions present (3.12 and 3.13).
Not a problem. Virtual environments will isolate each
project in Volume 04.

Virtual environment — an isolated Python environment
per project so different projects can use different
library versions without conflict.

Status: VERIFIED

---

## Tool 5 — AWS CLI

AWS CLI — Amazon Web Services Command Line Interface.
A tool that lets you control your entire AWS account from
the terminal instead of clicking in the browser console.

Why it matters:
From Volume 07 onward everything in AWS — creating buckets,
scanning IAM policies, reading CloudTrail logs, running
Prowler — all happens through this tool from the terminal.

Version verified: aws-cli/2.34.4

Status: VERIFIED

---

## Tool 6 — AWS Free Tier Account

AWS Free Tier — Amazon's program that gives new accounts
free usage of many services for 12 months.

Free Tier includes:
- 750 hours per month of EC2 t2.micro or t3.micro instance
- 5GB of S3 storage
- 1 million Lambda requests per month
- And many more services

EC2 — Elastic Compute Cloud — virtual servers in AWS cloud.
S3 — Simple Storage Service — object storage in AWS cloud.
Lambda — serverless compute — runs code without managing servers.

These terms will be covered in depth in Volume 07.

Account verified via:
aws sts get-caller-identity

Output:
- UserId: AIDA3Z3ILOCUCPMLSCAUD
- Account: 811430801576
- ARN: arn:aws:iam::811430801576:user/asheesh01

ARN — Amazon Resource Name — unique identifier for every
resource in AWS. Format:
arn:aws:service::accountnumber:resourcetype/resourcename

Authenticated as IAM user asheesh01 — not root. Correct.

Status: VERIFIED AND CONNECTED

---

## Tool 7 — AWS Billing Alert

Zero Spend Budget alert created.
Any charge above $0.01 sends immediate email to
asheeshsharma0001@gmail.com

Why $0.01 threshold:
On Free Tier there should be zero charges ever.
Any charge means something went wrong — service left running,
limit exceeded, paid feature accidentally enabled.
Want to know immediately at $0.01 not at $5.

Status: ACTIVE

---

## IAM User vs Root User

Root user — the account created when you first signed up for AWS.
Has unlimited access to everything. Cannot be restricted by
any policy. If compromised — attacker can delete entire account,
remove billing alerts, create hundreds of paid resources,
and nothing can stop them.

IAM user — asheesh01 — created by root with specific permissions.
Has only the access needed for daily work.
If compromised — can be immediately deactivated from root account.
Damage is limited and controllable.

Rule: Never use root for daily work. Ever.

---

## AWS Region

Region: ap-south-1 — Mumbai, India

A region is a physical geographic location where AWS has
data centers. You choose the region closest to you for
lowest latency — commands reach AWS servers faster.

Mumbai is chosen because:
- Physically closest to India
- Lowest latency for Indian users
- Data stored in Mumbai stays in India
- Relevant for Indian data compliance laws

---

## Key Security Lesson From This Topic

AWS Access Keys — the Access Key ID and Secret Access Key
used to authenticate AWS CLI — must never be committed to GitHub.

Automated bots scan GitHub every few minutes looking for
exposed keys. Within minutes of leaking a key, bots find it,
authenticate to your account, and spin up expensive resources
like GPU instances for crypto mining.

Students have received $50,000 bills from a single leaked key.

Prevention:
- Never hardcode credentials in any file
- Use git-secrets tool — installed in Volume 08
- Store credentials only in ~/.aws/credentials file
  which is never committed to GitHub

---

## Correct Daily Workflow

Kali VM:
- Run commands
- Take screenshots
- Observe output

Ubuntu:
- Write notes in VS Code
- Paste lab outputs into lab files
- git add .
- git commit -m "Day XX - Topic - description"
- git push origin main

---

## Key Lessons

1. Environment is your lab — broken environment = broken learning
2. Never use root for daily AWS work
3. AWS Access Keys must never appear in GitHub
4. Git email must match GitHub email for green squares
5. Kali VM is isolated lab — Ubuntu is your main workspace
6. Zero Spend Budget alert protects against surprise charges
7. ap-south-1 is AWS Mumbai — closest region for India