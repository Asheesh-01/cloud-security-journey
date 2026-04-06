# Day 26 — Labs

**Topics:** Types of Cyber Threats + Hacker Types
**Environment:** Kali Linux VM

---

## Lab 1 — Install ClamAV
```bash
sudo apt install clamav clamav-daemon -y
```

- `sudo` — root privileges required for package installation
- `apt install` — install from Kali repository
- `clamav` — the antivirus scanning engine
- `clamav-daemon` — background service enabling on-access scanning
- `-y` — skip confirmation prompt, auto-accept installation

---

## Lab 2 — Update Signature Database
```bash
sudo systemctl stop clamav-freshclam
sudo freshclam
sudo systemctl start clamav-freshclam
```

- `systemctl stop clamav-freshclam` — stops the automatic update daemon
  before running freshclam manually to prevent conflicts between two
  processes writing to the same database simultaneously
- `freshclam` — downloads latest malware signature definitions from
  ClamAV's servers. Without current signatures, known malware goes undetected.
- `systemctl start clamav-freshclam` — restarts the automatic update
  daemon after manual update completes

---

## Lab 3 — Scan Home Directory
```bash
clamscan -r /home/kali
```

- `clamscan` — the command-line scanning binary
- `-r` — recursive. Scans all files in the directory and every
  subdirectory within it
- `/home/kali` — starting scan directory

---

## Lab 4 — Scan With Logging
```bash
clamscan -r /home/kali --verbose --log=/tmp/clamav_scan.log
```

- `--verbose` — prints every file being scanned to the terminal,
  not just infected files. Shows the scanner working in real time.
- `--log=/tmp/clamav_scan.log` — writes complete scan results to this
  file path. Allows review after the scan completes without scrolling
  through terminal history.

---

## Lab 5 — Scan Temp Directory
```bash
clamscan -r /tmp --verbose
```

- `/tmp` — the world-writable directory where malware commonly
  downloads and stages payloads before execution.
  Scanning /tmp is the first malware check on any suspected Linux machine.
- No `--remove` flag here — always review findings before deleting.

---

## Lab 6 — Review Scan Log
```bash
cat /tmp/clamav_scan.log
```

- `cat` — print file contents to terminal
- Key lines to look for in output:
  - `Infected files: 0` — clean scan
  - `Infected files: N` — N files flagged for review
  - Any line with `FOUND` — specific detection with filename and signature name