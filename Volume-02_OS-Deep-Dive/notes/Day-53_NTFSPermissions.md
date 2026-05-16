# Day 53 — NTFS Permissions and ACLs

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 2 — Windows File System
**Subtopic:** 3 — NTFS Permissions and ACLs

---

## What It Is

ACL = Access Control List. Attached to every NTFS file and directory.
Defines who can do what. Checked before every access operation.
Misconfigured permissions = most common Windows privilege escalation path.

## Key Terms

**DACL** — Discretionary ACL. Controls access permissions. Owner controls it.
**SACL** — System ACL. Controls auditing. Which accesses get logged (Event ID 4663).
**ACE** — Access Control Entry. One entry in an ACL.
  Contains: security principal SID, access mask, Allow or Deny type.
**SID** — Security Identifier. Unique per principal. Never reused.
  ACLs store SIDs not names — rename does not break permissions.
**Access mask** — 32-bit value representing specific permissions.

## NTFS Permission Levels

Full Control (F) — read, write, modify, delete, change permissions, take ownership.
Modify (M) — read, write, execute, delete. Cannot change permissions.
Read and Execute (RX) — read and run. Cannot modify.
List Folder Contents — see folder contents only. Directories only.
Read (R) — read contents and attributes only.
Write (W) — create and write. Cannot read existing files.

(I) = Inherited. (OI) = Object Inherit (files). (CI) = Container Inherit (subfolders).

## Permission Evaluation Order

1. Collect user SID + all group SIDs
2. Evaluate Deny ACEs first — any match = immediate denial
3. Evaluate Allow ACEs — cumulative grants across all groups
4. No matching Allow = denied by default

**DENY BEATS ALLOW. Always. No exceptions.**
Administrators group membership does not override explicit Deny.

## Security Relevance

**Writable service binary** — Write on service binary directory = replace binary
= malware runs with service privileges. Standard privesc check.

**Weak Program Files permissions** — Users/Everyone with Write = DLL hijacking path.

**World-writable directories** — C:\Windows\Temp, C:\Users\Public intentional.
Same permissions on system directories = critical misconfiguration.

**ACL inheritance abuse** — modify parent ACL = propagates to all children.

## Commands (Windows 10 VM)

```cmd
icacls C:\Windows\System32\notepad.exe
icacls C:\Windows\System32\
icacls C:\Users\
icacls "C:\Program Files\"
icacls C:\Windows\Temp\
icacls C:\Users\Public\
icacls C:\Users\%USERNAME%\Documents\
echo "test" > C:\Users\Public\sensitive.txt
icacls C:\Users\Public\sensitive.txt /grant Everyone:R
icacls C:\Users\Public\sensitive.txt /deny Everyone:W
icacls C:\Users\Public\sensitive.txt
icacls C:\Users\Public\sensitive.txt /reset
del C:\Users\Public\sensitive.txt
```