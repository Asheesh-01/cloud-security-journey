# Day 32 — Zero Trust

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 15 — Basic Security Principles
**Subtopic:** 6 — Zero Trust

---

## What It Is

Never trust, always verify.
No user, device, or connection is trusted by default — even inside the network.
Replaces perimeter model (castle-and-moat) with identity-centric model.

## Key Terms

**Implicit trust** — old model. Inside network = trusted. Dead in cloud era.
**Explicit verification** — every request verified independently: who, what device,
  where from, what requested, does it match normal behavior.
**Microsegmentation** — very small network zones. Compromise one = cannot reach others.
**Continuous validation** — not just at login. Monitor session for anomalies.
**Identity-centric** — identity is the new perimeter, not network location.
**Device trust** — verify device health (patched, managed, EDR running).
**Conditional access** — identity + device + location + time + sensitivity = decision.

## Why Perimeter Security Failed

Assumption 1: attackers outside, employees inside.
Reality: phishing, stolen credentials, and supply chains break the perimeter.
Cloud means no fixed "inside" anymore.

Assumption 2: authenticate once, trust continuously.
Reality: credentials stolen, sessions hijacked, insiders go rogue mid-session.

Marriott 2018: Starwood compromised 2014. Four years of attacker inside network
treated as trusted internal traffic. 500 million records. Perimeter = intact.
Implicit trust = catastrophic failure.

## Five Pillars of Zero Trust

1. Identity — MFA, behavioral analytics, privileged identity management
2. Devices — compliance checks, managed, patched, EDR running
3. Network — microsegmentation, encrypted east-west traffic
4. Applications — per-app authentication, SSO with MFA
5. Data — classify by sensitivity, highest sensitivity = highest verification

## Least Privilege + Zero Trust

Zero Trust verifies every request.
Least privilege limits what verified requests can access.
Together: limit unauthorized access AND limit damage from compromised accounts.

## Real Connection — Google BeyondCorp

Operation Aurora 2010 hit Google via zero-day.
Google rebuilt access model entirely.
BeyondCorp: no privileged internal network.
Office employee = coffee shop employee from access perspective.
Network location provides zero additional trust.
Blueprint for the industry: Zscaler, Cloudflare Access, Okta, Entra ID.

AWS IAM = Zero Trust. Every API call independently authenticated.
No network location provides implicit trust.
IAM policy design is the cloud Zero Trust implementation — covered Volume 7.

## Commands

```bash
sudo cat /etc/ssh/sshd_config | grep -E "PasswordAuthentication|PubkeyAuthentication|PermitRootLogin|MaxAuthTries"
sudo sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo grep "PasswordAuthentication" /etc/ssh/sshd_config
ss -tnp state established
sudo grep -E "Accepted|Failed" /var/log/auth.log | tail -20
sudo ufw status numbered
systemctl list-units --type=service --state=running
```