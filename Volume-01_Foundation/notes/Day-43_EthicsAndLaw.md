# Day 43 — Ethical and Legal Concepts

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 19 — Ethical and Legal Concepts
**Tools:** None — concept only

---

## IT Act 2000 — India

Section 43: unauthorized access. Compensation up to 1 crore rupees.
Section 66: computer offences dishonestly/fraudulently. 3 years or 5 lakh or both.
Section 66B: receiving stolen computer resources. 3 years or 1 lakh.
Section 66C: identity theft (passwords, electronic signatures). 3 years or 1 lakh.
Section 66D: cheating by impersonation using computer resources. 3 years or 1 lakh.
Section 43A: failure to protect sensitive personal data. No upper limit on compensation.

Career rule: unauthorized access is a crime regardless of intent.
"I was just looking" and "I wanted to help" are not legal defenses.

## DPDP Act 2023 — India

Data principal: the person whose data is processed.
Data fiduciary: organization that determines purpose and means of processing.
Consent: explicit, informed, specific to purpose. One consent ≠ all purposes.
Data localization: certain data may be required to stay in India.
Rights: access, correction, erasure, grievance redressal.
Penalties: up to 250 crore for significant breaches.
          Up to 50 crore for failure to notify Data Protection Board.

Cloud security impact: data residency decisions, retention policies,
access controls, and breach notification timelines are now legal requirements.

## Compliance — Industry Standards

Compliance = meeting defined minimum requirements and demonstrating it.
Compliance ≠ security. Can be compliant and still be breached.

ISO 27001: international ISMS certification. External auditor.
PCI-DSS: credit card data. 12 requirements. Required for fintech/e-commerce.
SOC 2: Type 1 (point in time) / Type 2 (6-12 months). US clients require this.
RBI Cloud Guidelines: Indian banking/fintech cloud requirements. Data localization,
vendor risk, audit rights, incident notification timelines.

## Ethics — Never Test What You Do Not Own

Written authorization required before any testing. Verbal is not enough.
Formal scope document: which systems, which time window, which techniques.
Anything outside scope = unauthorized access.

Scope creep: if attack path leads out of scope — stop. Document. Report.
Do not follow the path into out-of-scope systems.

Data handling: real customer data encountered during tests is not yours.
Do not copy, read, store, or use beyond what is necessary to prove the vulnerability.

No collateral damage: do not cause service disruption unless explicitly authorized.

## Responsible Disclosure

Found a real vulnerability outside a test? Follow this process:

1. Document thoroughly — reproduction steps, severity, evidence.
2. Find correct contact — security@company.com or /.well-known/security.txt.
3. Report privately — do not post publicly. Do not discuss with others.
4. Wait 90 days — industry standard remediation window.
5. Coordinate publication — ideally after fix is deployed.

Do not: demand payment before disclosing. Set ultimatums. Publish before
90 days without response. Exfiltrate data beyond proof of vulnerability.
These behaviors are extortion or unauthorized access — criminal offences.

Bug bounty programs: legal protection + payment. Use them if the company has one.