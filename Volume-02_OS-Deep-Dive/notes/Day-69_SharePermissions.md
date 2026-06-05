# Day 69 — Share Permissions

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 7 — Windows Permissions and Access Control
**Subtopic:** 2 — Share Permissions

---

## What It Is

Network shares controlled by two simultaneous permission systems.
Share permissions: apply over network only. Three levels.
NTFS permissions: apply everywhere (local and network). Six levels.
Effective permission = most restrictive of both systems combined.

## Key Terms

**UNC path** — \\servername\sharename. Network share address format.
**Administrative shares** — C$, ADMIN$, IPC$. Auto-created on every machine.
  Hidden ($ suffix). Accessible to local administrators only.
  LAPS prevents lateral movement via shared local admin password.

**Share permission levels:**
Full Control — read, write, delete, change share permissions.
Change — read, write, delete. Cannot change permissions.
Read — read and execute only.

**Everyone group** — all authenticated users + potentially unauthenticated.
**Authenticated Users** — only valid credential holders. Safer than Everyone.

## The Two-Layer Model

Network access check:
Stage 1 — Share permission. No share access = denied immediately.
Stage 2 — NTFS permission. Granular operation control.
Final = most restrictive of both.

Share = Full Control + NTFS = Read → Effective = Read (NTFS wins).
Share = Read + NTFS = Full Control → Effective = Read (Share wins).

Best practice: broad share permissions (Authenticated Users: Full Control)
+ granular NTFS permissions for all access control.
Manage one system (NTFS) not two simultaneously.

## Common Misconfigurations

Everyone Full Control on share + weak NTFS = open to all network users.
Same local admin password + no LAPS = C$ accessible on all machines.
NTFS weak on shared folder = local access bypasses share entirely.
Legacy systems with NULL session = unauthenticated share enumeration.

## Share Permissions vs NTFS Scope

Share permissions: NETWORK ACCESS ONLY.
NTFS permissions: LOCAL AND NETWORK.
An attacker with local access to the machine bypasses share permissions.
NTFS is the only protection for local access.

## SMB Security

SMB signing: cryptographically signs packets.
Without SMB signing: SMB relay attacks possible.
SMB relay: intercept auth attempt → relay to other server → authenticate as victim.
All Windows machines should require SMB signing.

## Commands (Windows 10 VM)

```cmd
net share
net share C$
mkdir C:\Users\Public\TestShare
net share TestShare=C:\Users\Public\TestShare /GRANT:Everyone,READ
net share TestShare
powershell "Get-SmbShare -Name 'TestShare' | Format-List"
powershell "Get-SmbShareAccess -Name 'TestShare' | Format-Table -AutoSize"
powershell "Get-SmbShare | ForEach-Object { Write-Output \"=== $($_.Name) ===\"; Get-SmbShareAccess -Name $_.Name | Format-Table AccountName,AccessRight -AutoSize }"
dir \\localhost\TestShare
icacls C:\Users\Public\TestShare
powershell "Get-SmbShareAccess -Name 'C$' | Format-Table -AutoSize"
net share TestShare /DELETE
rmdir C:\Users\Public\TestShare
```