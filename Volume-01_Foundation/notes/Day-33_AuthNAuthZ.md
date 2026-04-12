# Day 33 — Authentication vs Authorization

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 16 — Authentication and Authorization
**Subtopic:** 1 — Authentication vs Authorization

---

## What It Is

Authentication — proving who you are. Identity verification.
Authorization — deciding what you are allowed to do. Permission check.
Order is fixed: authenticate first, authorize second.

## Key Terms

**Principal** — entity being authenticated. User, service account, API key, IAM role.
**Credential** — what is presented during authentication. Password, key, token, cert.
**Session** — temporary authenticated state. Session cookie in web apps.
**Token** — data representing authenticated identity. JWT in modern APIs.
**Access control** — mechanism enforcing authorization decisions.

**401** — authentication failed or not provided. Server does not know who you are.
**403** — authentication succeeded, authorization failed. Server knows you, says no.

## Where Each Fails

Authentication failures:
- Brute force, credential stuffing, phishing, session hijacking, MFA bypass

Authorization failures:
- IDOR — change URL parameter to access other user's data
- Missing function-level access control — hidden UI, missing backend check
- Privilege escalation — app checks identity but not role
- BOLA — Broken Object Level Authorization, most common API vulnerability

Authentication failures = attacker gets in as someone else.
Authorization failures = authenticated user accesses what they should not.

## Real Breach — Optus 2022

API endpoint with no authentication requirement.
Attacker iterated sequential customer IDs.
9.8 million records downloaded. 40% of Australia's population.
SSNs, passport numbers, driver's license numbers.
No exploit. No stolen credentials. Authentication layer simply missing.

Lesson: every API endpoint must require authentication before returning data.
"Internal" endpoints that are publicly reachable must be treated as public.

## Commands

```bash
ssh-keygen -t ed25519 -C "test-auth-demo" -f /tmp/test_auth_key -N ""
ls -la /tmp/test_auth_key*
cat /tmp/test_auth_key.pub
ls -la /etc/passwd /etc/shadow /etc/sudoers
cat /etc/shadow                    # authentication OK, authorization FAILS
sudo cat /etc/shadow | head -3     # both pass with root elevation
who && w && last | head -10
sudo -l && id && groups
sudo cat /var/log/apache2/access.log 2>/dev/null | grep -E " 401 | 403 " | tail -10
rm /tmp/test_auth_key /tmp/test_auth_key.pub
```