# Day 31 — Least Privilege

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 15 — Basic Security Principles
**Subtopic:** 5 — Least Privilege

---

## What It Is

Every entity operates with only the minimum permissions needed.
Reduces blast radius — compromise of low-privilege account = limited damage.
Not about trust — about limiting damage when trust is broken.

## Key Terms

**Privilege** — ability to perform an action (read, write, execute, admin).

**Privilege escalation** — gaining permissions beyond what was granted.
Horizontal = same level, different user. Vertical = higher level (root/admin).

**Service account** — account used by software/services.
Frequently overprivileged. Should have narrowest possible scope.

**PAM** — Privileged Access Management. Tools for managing, monitoring,
recording privileged account usage. Enterprise security standard.

**Just-in-time access** — elevated access granted for specific window,
automatically revoked after. Better than standing privileges.

**RBAC** — Role-Based Access Control. Permissions assigned to roles,
users inherit role permissions. Easier to manage than per-user permissions.

## How Least Privilege Is Violated

**Overprivileged users** — admin rights given for convenience, not need.
**Overprivileged service accounts** — application gets full DB admin instead of one table.
**Unused privileges** — permissions granted but never used. Pure risk.
**Standing privileges** — permanent elevated access that is never revoked.
**Shared accounts** — multiple people, one account. Cannot audit. Cannot scope.

## Real Breach — Capital One 2019

SSRF vulnerability in WAF → EC2 metadata service (169.254.169.254)
→ retrieved IAM credentials for attached EC2 role
→ IAM role had excessive S3 access across entire AWS account
→ 106 million customer records downloaded from S3

Least privilege failure: EC2 IAM role should have accessed only specific
buckets needed for its function. Instead it could read virtually everything.

Fix: IMDSv2 prevents SSRF to metadata. Least privilege IAM roles limit
damage even if credentials are stolen. Covered in depth in Volume 7 Topic 4.

## Commands

```bash
id && whoami && groups               # your permissions and memberships
sudo -l                              # what sudo commands you can run
find / -perm -u=s -type f 2>/dev/null  # all SUID binaries (privesc paths)
sudo cat /etc/sudoers                # full sudo privilege configuration
sudo useradd -m -s /bin/bash limiteduser  # create minimal user
sudo passwd limiteduser
sudo -u limiteduser id               # verify limited permissions
sudo -u limiteduser sudo -l          # confirm no sudo access
sudo -u limiteduser cat /etc/shadow  # confirm no shadow access
ps aux | grep "^root" | head -20     # processes running as root
sudo userdel -r limiteduser          # clean up test user
```