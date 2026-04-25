# Day 45 — Labs: Kernel in Depth

**Environment:** Kali Linux VM

---

## Lab 1 — Kernel Version
```bash
uname -r
uname -a
```
- `uname -r` — kernel release number only
- `uname -a` — full info: name, hostname, release, version, hardware, OS
- Format: major.minor.patch-extraversion-architecture

---

## Lab 2 — Loaded Kernel Modules
```bash
lsmod
```
- Lists all kernel modules currently loaded
- Every entry is code running in Ring 0
- Columns: name, size in memory, dependency count and names
- Unfamiliar module with no dependencies = investigate

---

## Lab 3 — Module Details
```bash
modinfo bluetooth 2>/dev/null || modinfo e1000 2>/dev/null
```
- `modinfo` — detailed info about a specific kernel module
- Shows filename, description, license, author, version
- License: GPL = open source auditable. Proprietary = cannot inspect source.

---

## Lab 4 — Kernel Messages
```bash
dmesg | head -30
```
- `dmesg` — kernel ring buffer — messages since boot
- Hardware detection, driver loading, initialization events
- `head -30` — boot-time messages only

---

## Lab 5 — Security-Relevant Kernel Events
```bash
dmesg | grep -i -E "error|fail|denied|blocked|warning" | head -20
```
- `grep -i -E` — case-insensitive extended regex
- Finds kernel-level errors, denials, and warnings
- Access denial messages are kernel-level security events

---

## Lab 6 — Hardware Events
```bash
dmesg | grep -i "usb\|pci\|driver" | head -15
```
- Hardware detection and driver loading messages
- Unauthorized USB device appearing here = forensic indicator

---

## Lab 7 — Kernel Build Info
```bash
cat /proc/version
```
- Kernel version, GCC compiler version, build date
- Confirms what kernel is actually running

---

## Lab 8 — CPU Security Features
```bash
cat /proc/cpuinfo | grep "flags" | head -1 | tr ' ' '\n' | grep -E "^vmx$|^svm$|^smep$|^smap$"
```
- `grep flags` — finds CPU feature flags line
- `tr ' ' '\n'` — converts space-separated flags to one per line
- Filters for virtualization and kernel exploit mitigation flags
- smep and smap present = CPU-level Ring 0/3 boundary enforcement active