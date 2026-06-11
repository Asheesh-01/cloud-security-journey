# Day 73 — Windows Security Features

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 9 — Windows Security Features
**Subtopics:** Defender + Firewall vs Defender + BitLocker + SmartScreen

---

## Subtopic 1 — Windows Defender

### What It Is
Microsoft's built-in antivirus and security tool. Watches every file accessed, every program run, every download. Multiple detection layers working simultaneously.

### Key Terms

**Signature-based detection** — matches file against database of known malware byte patterns and hashes. Fast for known threats. Blind to new unknown threats.

**Heuristic detection** — analyzes file structure and characteristics without needing exact signature. Detects suspicious patterns — packers, obfuscated code, suspicious API calls.

**Behavioral detection** — watches what programs actually do at runtime. Process injection, registry Run key writes, reverse shell connections — flagged even if file passed all static checks.

**Quarantine** — malicious file moved to secure isolated folder. Cannot execute. Preserved for investigation. Restorable if false positive.

**Real-time protection** — continuous background monitoring. Every file access, download, execution scanned live.

**Cloud-delivered protection** — unknown file hash sent to Microsoft cloud. Global threat intelligence checked instantly. Faster than waiting for definition update.

**ASR (Attack Surface Reduction) rules** — block entire categories of dangerous behavior. Office macros spawning child processes = blocked regardless of file reputation.

**Windows Defender Antivirus** — free built-in AV tool. Signature, heuristic, behavioral detection.
**Microsoft Defender for Endpoint** — enterprise paid EDR product. Full timeline recording, threat hunting, centralized management. Different product — same brand name.

**EDR (Endpoint Detection and Response)** — records everything on endpoint. Complete timeline for incident investigation. Defender Antivirus is NOT an EDR.

### Five Detection Layers in Order
1. Signature — known malware database match
2. Heuristic — suspicious structure and characteristics
3. Behavioral — malicious actions at runtime
4. Cloud — unknown file hash checked against global database
5. ASR rules — dangerous behavior categories blocked at kernel level

### Attacker Bypass Techniques
- **Living off the land** — use legitimate Windows tools (PowerShell, WMI, certutil). No malware signature for built-in tools.
- **Obfuscation** — Base64 encode PowerShell commands. Signature matching fails on encoded string.
- **Process injection** — inject into legitimate trusted process. Defender sees legitimate process.
- **Disable Defender** — `Set-MpPreference -DisableRealtimeMonitoring $true` — requires admin. SOC alert: Defender disabled = immediate investigation.

### Commands

```powershell
Get-MpComputerStatus
```
Retrieves current Defender status — enabled, real-time protection, last update, cloud protection.

```powershell
Get-MpComputerStatus | Select-Object AntivirusEnabled, RealTimeProtectionEnabled, AntivirusSignatureLastUpdated, CloudProtectionEnabled
```
Filtered view of critical status fields only.

```powershell
Get-MpThreatDetection
```
History of all threats Defender detected — name, severity, time, path, action taken.

```powershell
Get-MpThreat
```
Currently active threats not yet fully remediated.

```powershell
Update-MpSignature
```
Forces immediate definition update. Run before any scan.

```powershell
Start-MpScan -ScanType QuickScan
```
Quick scan of common malware locations. Use FullScan during incident investigation.

### Security Angle — Uber 2022
Attacker used social engineering to get credentials. Found PowerShell script with hardcoded admin credentials on network share. Used those to access and screenshot security tools including endpoint security platform. Lesson: Defender is only as strong as the credentials protecting its management console.

---

## Subtopic 2 — Windows Firewall vs Windows Defender

### The Difference

| | Windows Defender | Windows Firewall |
|---|---|---|
| Watches | Files, processes, behavior | Network traffic, ports, connections |
| Blocks | Malware, malicious behavior | Unauthorized network connections |
| Acts when | File accessed or program runs | Network packet arrives or leaves |
| Attacker bypass | Obfuscation, living off land | Open allowed port, outbound default allow |
| Key question | Is this program malicious | Is this connection allowed |

### Critical Point
They complement — they do not replace each other. Firewall does not stop malware already on machine. Defender does not stop network attacks against open ports. Both must run simultaneously.

### Attack Chain Example
Phishing email → employee downloads malware → Defender catches = stopped.
Defender misses → malware runs → tries C2 connection → Firewall blocks outbound = stopped.
Both miss → full compromise.

---

## Subtopic 3 — BitLocker

### What It Is
Full disk encryption. Entire drive encrypted with AES-256 including OS. Drive removed from machine = unreadable noise without key. Protects data at rest from physical theft.

### Key Terms

**Encryption at rest** — data encrypted when stored on disk. Protects against physical theft of device or drive.

**AES-256** — encryption algorithm BitLocker uses. Military-grade. Breaking by brute force = longer than age of universe.

**TPM (Trusted Platform Module)** — dedicated security chip on motherboard. Stores BitLocker key. Verifies boot environment integrity at startup. Releases key automatically if boot environment untampered.

**Recovery key** — 48-digit key generated at BitLocker setup. Only way to decrypt if TPM fails or drive moved to another machine. Store in Active Directory or Azure AD — never on encrypted drive.

**Pre-boot authentication** — optional PIN or USB key required before Windows loads. Combined with TPM = two factors before OS boots.

**Cold boot attack** — freeze RAM, move to another machine, read encryption keys in RAM. TPM-only BitLocker vulnerable. TPM + PIN stops this — key never enters RAM until PIN entered.

**Evil maid attack** — attacker modifies bootloader on unattended machine. TPM detects tampered bootloader — refuses to release key. Attack blocked.

### What BitLocker Protects vs Does NOT Protect

**Protects against:** Physical theft of laptop or drive. Drive removed and read on another machine. Boot from USB attack.

**Does NOT protect against:** Malware on running machine (drive is decrypted when running). Network attacks against running machine. Authenticated malicious insider.

### Commands

```powershell
manage-bde -status
```
Current encryption status of all drives — method, percentage encrypted, key protectors.

```powershell
manage-bde -status C:
```
Status of C: drive specifically.

```powershell
manage-bde -protectors -get C:
```
Lists all key protectors — TPM, recovery key ID, numerical password.

### Security Angle — US Department of Veterans Affairs 2006
Employee took home laptop with unencrypted data of 26.5 million veterans. Laptop stolen. All records immediately accessible. $20 million settlement. BitLocker would have made stolen drive unreadable — hardware loss only, not data breach.

---

## Subtopic 4 — SmartScreen

### What It Is
Reputation-based filtering system. Checks every downloaded file and visited URL against Microsoft's global database. Known malicious = blocked. Unknown new file = warning shown. Known safe = silent pass.

### Key Terms

**Reputation-based detection** — checks how many users have safely used this file. New file = suspicious. Widely used file = trusted.

**Hash** — unique fingerprint of file. SmartScreen sends hash to Microsoft cloud — not file itself. Privacy preserved.

**Mark of the Web (MotW)** — hidden flag added to every file downloaded from internet. Stored as Alternate Data Stream. SmartScreen only checks files with MotW. Files from USB or internal network have no MotW — SmartScreen skips them.

**Authenticode signature** — digital signature from code signing certificate. Signed files from known publishers bypass SmartScreen reputation check.

**Unknown publisher warning** — blue dialog shown for unrecognized files. User must click "Run anyway." Friction that stops casual malware execution.

### SmartScreen Check Flow
1. File downloaded — MotW applied
2. User double-clicks — MotW detected — SmartScreen activated
3. File hash sent to Microsoft cloud
4. Known malicious — blocked, red warning, cannot run
5. Unknown — blue warning shown, user can click through
6. Known safe — silent pass, no warning
7. Signed by trusted publisher — passes without cloud check

### MotW Bypass — Real Attack 2022-2023
Files inside zip/iso/img containers did not consistently inherit MotW when extracted. Malware delivered in zip attachment — extracted exe had no MotW — SmartScreen skipped. Qbot, Emotet, multiple ransomware groups used this. Patched in CVE-2022-41091 but variants continued into 2023.

### Registry Check

```powershell
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" -Name SmartScreenEnabled
```
Reads SmartScreen enforcement level. Off = disabled = critical finding during audit.

---

## All Four Layers Together — Defense in Depth

Phishing email with malicious zip:
- SmartScreen checks zip reputation → warning shown → user clicks through
- MotW bypass → extracted exe has no MotW → SmartScreen skipped
- Defender signature → new custom malware → no match → passes
- Defender heuristic → suspicious structure → flagged → blocked here if updated
- Defender behavioral → if runs, reverse shell attempt → blocked and quarantined
- Firewall → if malware fully executes, C2 outbound → blocked by outbound rule
- BitLocker → irrelevant — protects physical theft not running malware

No single layer sufficient. Each catches what others miss. This is defense in depth in practice.