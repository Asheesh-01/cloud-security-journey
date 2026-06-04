# Day 68 — Labs: UAC

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — Check UAC Core Setting
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v EnableLUA
```
- EnableLUA: 0x1 = UAC enabled (correct)
- EnableLUA: 0x0 = UAC completely disabled = critical finding

---

## Lab 2 — Check Admin Prompt Behavior
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v ConsentPromptBehaviorAdmin
```
- 0x5 = default (prompt for non-Windows binaries)
- 0x0 = silent auto-elevation = admins never see prompts = finding

---

## Lab 3 — Check Secure Desktop Setting
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v PromptOnSecureDesktop
```
- 0x1 = secure desktop used = prompts cannot be automated
- 0x0 = no secure desktop = prompts can be clicked by malware

---

## Lab 4 — All UAC Policy Values
```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System"
```
- Shows all UAC configuration values in one command
- Compare each against secure baseline

---

## Lab 5 — Current Integrity Level
```cmd
whoami /groups | findstr /I "integrity"
```
- Medium Mandatory Level = standard user token
- High Mandatory Level = elevated token
- System Mandatory Level = SYSTEM process
- Run this from both normal cmd and elevated cmd to see difference

---

## Lab 6 — Current Token Privileges
```cmd
whoami /priv
```
- Lists all privileges in current access token
- Standard token: limited privilege set
- Elevated token: includes SeDebugPrivilege (Mimikatz requirement)
- Run from both normal and elevated cmd to compare

---

## Lab 7 — UAC Virtualization Store
```cmd
dir /A "C:\Users\%USERNAME%\AppData\Local\VirtualStore\" 2>nul
```
- VirtualStore = where redirected writes from protected locations land
- Legacy programs writing to System32 end up here instead
- Unexpected program data here = possible legacy malware compatibility behavior

---

## Lab 8 — Verify UAC Protection Active
```cmd
echo test > "C:\Windows\System32\uac_test.txt" 2>nul
if exist "C:\Windows\System32\uac_test.txt" (
    echo WARNING: UAC may be disabled
    del "C:\Windows\System32\uac_test.txt"
) else (
    echo CORRECT: UAC protecting System32 from standard writes
)
```
- Run from a non-elevated command prompt
- Write attempt to System32 should fail with UAC enabled
- Success means UAC is not protecting system directories