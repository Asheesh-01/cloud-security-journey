# Day 36 — Labs: Biometrics

**Environment:** Kali Linux VM

---

## Lab 1 — Check for Fingerprint Hardware
```bash
lsusb | grep -i finger
lspci | grep -i finger
```
- `lsusb` — lists all USB devices connected to the system
- `lspci` — lists all PCI devices
- `grep -i finger` — case-insensitive search for fingerprint devices
- VM environment: expected no results — confirms hardware dependency

---

## Lab 2 — Check Fingerprint Software
```bash
which fprintd-enroll 2>/dev/null || echo "fprintd not installed"
dpkg -l | grep fprint 2>/dev/null || echo "No fingerprint packages"
```
- `which fprintd-enroll` — checks if fingerprint daemon tools are installed
- `dpkg -l | grep fprint` — searches installed packages for fingerprint software
- On real hardware: fprintd handles enrollment and verification

---

## Lab 3 — Behavioral Biometrics Demo
```bash
python3 -c "
import time
print('Type the word: security')
print('Press Enter when done')
start = time.time()
input()
end = time.time()
duration = end - start
print(f'Time to type: {duration:.3f} seconds')
print('Keystroke dynamics: behavioral biometric based on typing rhythm')
"
```
- `time.time()` — high-precision timestamp
- Measures total typing duration as primitive behavioral biometric
- Real systems capture inter-key timing for each individual keystroke

---

## Lab 4 — Biometric Template Concept
```bash
echo -n "fingerprint_sample_data_unique_to_user" | sha256sum
```
- Simulates how biometric systems store templates
- Raw biometric → mathematical transformation → stored template
- Original biometric not recoverable from stored template

---

## Lab 5 — Cancelable Biometrics Demo
```bash
BIOMETRIC="fingerprint_raw_data"
TRANSFORM_KEY_1="organization_secret_key_v1"
TRANSFORM_KEY_2="organization_secret_key_v2"
echo "Original:"; echo -n "${BIOMETRIC}" | sha256sum
echo "Enrolled template:"; echo -n "${BIOMETRIC}${TRANSFORM_KEY_1}" | sha256sum
echo "After breach - new template:"; echo -n "${BIOMETRIC}${TRANSFORM_KEY_2}" | sha256sum
```
- Same biometric + different transform key = completely different stored template
- If templates stolen, change transform key — stolen templates become useless
- Biometric itself (fingerprint) never needs to change

---

## Lab 6 — Check PAM Authentication Chain
```bash
ls /etc/pam.d/ | head -20
sudo cat /etc/pam.d/common-auth
```
- `/etc/pam.d/` — PAM configuration directory for all services
- `common-auth` — shared auth config used by most Linux services
- Shows current authentication chain — where biometric modules plug in