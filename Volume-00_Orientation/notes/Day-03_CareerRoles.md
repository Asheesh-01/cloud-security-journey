# Day 03 — Career Roles & Goal Setting

**Date:** [fill today's date]
**Volume:** 00 — Orientation
**Topic:** Career Roles in Cloud Security

---

## Why This Topic Exists

Cannot build a learning roadmap without knowing the destination.
Every volume studied, every lab run, every project built must
connect to a specific role. Vague goal = vague preparation.

---

## The Five Main Roles

### 1. SOC Analyst — Security Operations Center Analyst

**My immediate target role.**

What they do daily:
- Sit in SOC monitoring SIEM dashboard for suspicious activity
- SIEM = Security Information and Event Management — platform
  that collects logs from all systems and generates alerts
- Triage alerts — real attack or false positive
- Investigate suspicious logins, malware alerts, brute force attempts
- Escalate serious incidents to Tier 2 or Tier 3
- Write incident tickets documenting findings
- Monitor logs from firewalls, endpoints, cloud environments

Three tiers:
- Tier 1 — alert triage, first response, entry level — where I start
- Tier 2 — deeper investigation, 1 to 2 years experience
- Tier 3 — threat hunting, advanced analysis, 3 plus years

Skills needed for Tier 1 fresher:
- Linux basics
- Networking fundamentals — TCP/IP, ports, protocols
- Log analysis — reading Windows and Linux logs
- SIEM basics — Wazuh, Splunk awareness
- Common attacks — phishing, brute force, malware
- Communication — findings must be clearly documented

Salary India:
- Fresher — ₹4 to ₹8 LPA
- After 1 to 2 years — ₹8 to ₹15 LPA

Where jobs are:
- MSSP companies — Tata Communications, Secureworks India,
  IBM Security, Wipro CyberDefense
- Large IT — TCS, Wipro, Infosys security divisions
- Banks and fintechs — internal SOC teams

Ready after: Volume 06 — Blue Team Core

---

### 2. Cloud Security Engineer

**My long term target role.**

What they do daily:
- Design and implement security controls in cloud environments
- Configure IAM policies — who gets access to what in AWS
- Set up logging and monitoring across cloud infrastructure
- Find and fix misconfigurations before attackers exploit them
- Build automated security scanning tools
- Review infrastructure as code for security issues
- Respond to cloud specific security incidents
- Work with developers to build features securely

Difference from SOC Analyst:
- SOC Analyst — reactive, waits for alerts and investigates
- Cloud Security Engineer — proactive, prevents alerts from
  happening in the first place
- Both work together — SOC finds the fire, engineer builds
  the system that stops the fire

Skills needed:
- AWS — IAM, VPC, CloudTrail, GuardDuty, Security Hub
- Python — automation and scripting
- Networking — deep cloud networking knowledge
- Linux — system administration and hardening
- Infrastructure as Code — Terraform, Checkov
- Container security — Docker, Kubernetes basics

Salary India:
- Fresher — ₹8 to ₹15 LPA
- Mid level 2 to 3 years — ₹15 to ₹25 LPA
- Senior 4 plus years — ₹25 to ₹45 LPA

Ready after: Volume 07 — Cloud Security Core

---

### 3. Detection Engineer

**High paying, growing fast in India.**

What they do daily:
- Write detection rules — logic that tells SIEM when to fire alert
- Test rules — make sure they catch real attacks
- Tune alerts — reduce false positives
- Map detections to MITRE ATT&CK framework
  MITRE ATT&CK = globally recognized database of attacker
  techniques used as reference by all security teams worldwide
- Study new attack techniques and build detections for them
- Run purple team exercises — simulate attack then build detection

Difference from SOC Analyst:
- SOC Analyst consumes alerts — reads and investigates them
- Detection Engineer creates alerts — writes rules that generate them
- SOC Analyst is firefighter. Detection Engineer builds fire alarm.

Skills needed:
- SIEM query languages — Splunk SPL, Elastic KQL
- Sigma rules — vendor neutral detection rule format
- MITRE ATT&CK framework — must know deeply
- Python — automating detection creation and testing
- Log analysis — deep understanding of normal vs abnormal
- Threat intelligence — understanding attacker techniques

Salary India:
- Fresher to junior — ₹8 to ₹15 LPA
- Mid level — ₹15 to ₹30 LPA
- Senior — ₹30 to ₹50 LPA

Ready after: Volume 06 + Volume 11 Option B

---

### 4. Penetration Tester

**Offensive security role.**

What they do daily:
- Get hired to legally attack company's own systems
- Find vulnerabilities before real attackers do
- Write detailed report — vulnerability found, how exploited, how to fix
- Test web apps, networks, cloud environments, mobile apps
- Social engineering — send fake phishing emails to test employees
- Present findings to management and developers

Important reality:
- 20% hacking, 80% report writing
- Communication skills as important as technical skills

Two types relevant to roadmap:
- Web app pentester — tests websites and APIs
- Cloud pentester — tests AWS, Azure, GCP misconfigurations

Difference from other roles:
- SOC Analyst — defensive, reactive
- Cloud Security Engineer — defensive, proactive
- Detection Engineer — defensive, builds detection
- Penetration Tester — offensive, simulates attacker

Skills needed:
- Networking — deep protocol and attack knowledge
- Linux — advanced command line
- Web security — OWASP Top 10, Burp Suite
- Cloud attack techniques — AWS enumeration
- Python — custom scripts and automation
- Report writing — clear professional English

Salary India:
- Fresher — ₹5 to ₹10 LPA
- Mid level — ₹15 to ₹30 LPA
- Senior/consultant — ₹30 to ₹60 LPA

Ready after: Volume 07 + Volume 11 Option C

---

### 5. Incident Responder

**Called when breach is confirmed.**

What they do daily:
- Respond to confirmed security incidents — ransomware, breach,
  account compromise
- Contain damage — isolate infected machines, block attacker
- Collect evidence without destroying it — disk images, memory dumps
- Investigate — how attacker got in, what they accessed, what they took
- Write incident report — timeline, root cause, damage assessment
- Recommend fixes to prevent repeat attacks
- Work under extreme pressure — breaches cost companies lakhs per hour

PICERL Framework — every incident responder follows this:
- Preparation — before incident, have tools and playbooks ready
- Identification — detect and confirm incident is happening
- Containment — stop spread, isolate affected systems
- Eradication — remove attacker and malware completely
- Recovery — restore systems to normal operation
- Lessons Learned — document and improve defenses

Skills needed:
- Digital forensics — disk imaging, memory analysis
- Malware analysis — understanding what malware did
- Log analysis — build attack timeline across all systems
- Chain of custody — keeping evidence legally valid
- Report writing — technical and executive communication
- Calm under pressure — breaches are chaotic

Salary India:
- Fresher — ₹5 to ₹10 LPA
- Mid level — ₹12 to ₹22 LPA
- Senior — ₹22 to ₹40 LPA

Ready after: Volume 06 — Blue Team Core

---

## My Goal — Three Phases

### Phase 1 — Internship Entry (Next 6 to 8 months)
- Target role: SOC Tier 1 Analyst intern
- Needs: Volume 01 to 06 complete, 180 plus GitHub commits,
  Log Analyzer project, Wazuh home lab
- Where to apply: MSSP companies, security startups
- Expected: ₹10,000 to ₹25,000 per month stipend
- Purpose: real experience before placement, not money

### Phase 2 — Placement Ready (Month 8 to 14)
- Target role: Cloud Security Engineer fresher
- Needs: Volume 07 complete, AWS Cloud Practitioner cert,
  aws-security-scanner on GitHub
- Expected: ₹8 to ₹15 LPA

### Phase 3 — Growth (Year 2 to 3)
- Target role: Mid level Cloud Security Engineer
- Needs: 1 to 2 years experience, AWS Security Specialty cert
- Expected: ₹15 to ₹30 LPA

### International Remote Option
Open to international remote roles.
Same skills. Pay equivalent to ₹30 to ₹80 LPA.
Requires strong English communication and public presence.
LinkedIn and Hashnode become even more critical.

---

## Key Lessons

1. Know your target role before learning anything
2. All five roles need the same foundation
3. SOC Analyst is reactive — Detection Engineer is proactive
4. Penetration Tester is offensive — all others are defensive
5. Incident Responder works after breach — Pentester works before
6. My path — SOC intern → Cloud Security Engineer → Specialization

---

## Connection to Previous Topics

Topic 1 — what cloud security is
Topic 2 — where it operates (AWS, Azure, GCP)
Topic 3 — who does what inside that world
Every role connects back to protecting the ecosystem from Topic 2
using the responsibility model from Topic 1