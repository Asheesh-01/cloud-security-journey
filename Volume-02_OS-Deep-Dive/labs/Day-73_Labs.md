# Day 73 — Labs: Windows Security Features

**Environment:** Windows 10 VM inside VirtualBox inside Ubuntu 24.04
**Commands run in PowerShell as Administrator**

---

## Lab 1 — Check Defender Status

```powershell
Get-MpComputerStatus
```
- `Get-MpComputerStatus` — retrieves full Defender status
- Shows AntivirusEnabled, RealTimeProtectionEnabled, definition version, cloud protection status

---

## Lab 2 — Check Critical Status Fields Only

```powershell
Get-MpComputerStatus | Select-Object AntivirusEnabled, RealTimeProtectionEnabled, AntivirusSignatureLastUpdated, CloudProtectionEnabled
```
- `| Select-Object` — filters output to specific properties only
- AntivirusSignatureLastUpdated — if days old = vulnerability

---

## Lab 3 — View Threat Detection History

```powershell
Get-MpThreatDetection
```
- Shows all threats Defender has detected on this machine
- Output: threat name, severity, detection time, file path, action taken

---

## Lab 4 — Check Active Threats

```powershell
Get-MpThreat
```
- Shows threats detected but not yet fully remediated
- Empty output = no active threats according to Defender

---

## Lab 5 — Update Definitions

```powershell
Update-MpSignature
```
- Forces immediate definition update
- Always run before scanning during incident investigation

---

## Lab 6 — Run Quick Scan

```powershell
Start-MpScan -ScanType QuickScan
```
- Scans common malware locations
- Use FullScan during actual incident investigation

---

## Lab 7 — Check BitLocker Status

```powershell
manage-bde -status
```
- Shows encryption status, method, percentage complete, key protectors for all drives

---

## Lab 8 — Check BitLocker Key Protectors

```powershell
manage-bde -protectors -get C:
```
- Lists all key protectors configured for C: drive
- Shows TPM protector, recovery key ID
- No recovery key listed = critical finding — no way to recover if TPM fails

---

## Lab 9 — Check SmartScreen Setting

```powershell
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" -Name SmartScreenEnabled
```
- Reads SmartScreen enforcement level from registry
- Off = SmartScreen disabled = critical security finding
- Warn = shows warning but allows bypass
- RequireAdmin = cannot bypass without admin credentials