# Day 22 — Labs

**Topic:** What is Software
**Environment:** Kali Linux VM

---

## Lab 1 — Check File Type

**Command:**
```bash
file /bin/ls
file /usr/bin/python3
file /etc/passwd
```

**What each part does:**
- `file` — reads file magic bytes and reports what type of file it is.
  Does not rely on the extension — reads actual content.
- `/bin/ls` — the ls binary
- `/usr/bin/python3` — the Python interpreter
- `/etc/passwd` — a text config file

**Expected output patterns:**
- ls → ELF 64-bit LSB executable — compiled binary, 64-bit Linux format
- python3 → ELF 64-bit LSB executable or symbolic link
- passwd → ASCII text

ELF = Executable and Linkable Format. The binary format Linux uses for executables.

**Paste output here.**

---

## Lab 2 — Check Shared Library Dependencies

**Command:**
```bash
ldd /bin/ls
ldd /usr/bin/python3
```

**What each part does:**
- `ldd` — List Dynamic Dependencies. Shows every shared library a
  binary loads at runtime.
- Each line shows: library name → resolved path → memory address

**Security relevance:** every library listed is a dependency. If any
of these libraries has a known vulnerability, this binary is affected.
This is how you start mapping dependency attack surface on Linux.

**Paste output here.**

---

## Lab 3 — List Installed Packages

**Command:**
```bash
dpkg -l | head -30
```

**What each part does:**
- `dpkg` — Debian Package manager. Manages installed packages on
  Debian/Ubuntu/Kali.
- `-l` — list all installed packages
- `|` — pipe output to head
- `head -30` — show only first 30 lines (full list is very long)

**Output columns:** status, name, version, architecture, description.
`ii` in status = installed correctly. `rc` = removed but config kept.

**Paste output here.**

---

## Lab 4 — Find Files Belonging to a Package

**Command:**
```bash
dpkg -L bash | head -20
```

**What each part does:**
- `dpkg -L` — List files owned by this package
- `bash` — the package to query
- `head -20` — first 20 files only

**Security relevance:** this is how you verify a binary belongs to a
legitimate package. If /bin/bash was modified by malware, dpkg -L bash
would still list it — but a hash check would reveal the modification.
`dpkg --verify bash` checks file integrity against package database.

**Paste output here.**

---

## Lab 5 — Read Strings from a Binary

**Command:**
```bash
strings /bin/ls | head -30
```

**What each part does:**
- `strings` — extracts printable character sequences from a binary file.
  Minimum 4 characters by default.
- `/bin/ls` — the target binary
- `head -30` — first 30 strings

**Security relevance:** strings is the first step in basic static malware
analysis. Running strings on an unknown binary reveals: error messages,
URLs the binary connects to, function names, file paths it references,
and sometimes hardcoded credentials. No reverse engineering skills needed.

**Paste output here.**

---

## Lab 6 — Check Python

**Commands:**
```bash
python3 --version
which python3
pip3 list 2>/dev/null | head -20
```

**What each part does:**
- `python3 --version` — shows installed Python version
- `which python3` — shows the full path to the python3 binary.
  `which` searches your PATH and returns the first match.
- `pip3 list` — shows installed Python packages.
  `2>/dev/null` — suppresses error messages.

**Paste output here.**

---

## Lab 7 — Find Recently Modified Files in /usr/bin

**Command:**
```bash
find /usr/bin -newer /etc/passwd -type f 2>/dev/null
```

**What each part does:**
- `find /usr/bin` — search the /usr/bin directory
- `-newer /etc/passwd` — find files modified more recently than /etc/passwd.
  /etc/passwd is modified when users are created — using it as a reference
  point finds software installed after user setup.
- `-type f` — regular files only
- `2>/dev/null` — suppress permission errors

**Security relevance:** on a compromised machine, malware often replaces
or adds binaries to /usr/bin. Finding recently modified files in system
binary directories helps identify what changed after an incident began.

**Paste output here.**

---

## Lab Summary

| Lab | Command | Security Purpose |
|-----|---------|-----------------|
| 1 | file | Identify file type regardless of extension |
| 2 | ldd | Map binary dependencies for CVE checking |
| 3 | dpkg -l | Software inventory baseline |
| 4 | dpkg -L | Verify files belong to known package |
| 5 | strings | Basic static analysis of unknown binary |
| 6 | python3, pip3 | Python environment audit |
| 7 | find -newer | Detect recently added/modified binaries |