# Day 35 — Labs: Multi-Factor Authentication

**Environment:** Kali Linux VM

---

## Lab 1 — Install Google Authenticator PAM
```bash
sudo apt install libpam-google-authenticator -y
```
- `libpam-google-authenticator` — PAM plugin adding TOTP to Linux auth
- PAM = Pluggable Authentication Modules, Linux's authentication framework
- `-y` — auto-confirm installation

---

## Lab 2 — Generate TOTP Secret
```bash
google-authenticator
```
- Interactive setup wizard
- Generates shared secret and QR code for authenticator app
- Answer y to time-based, y to update file, y to disallow reuse,
  n to extended window, y to rate limiting
- Scan QR code with Google Authenticator or Authy
- Save emergency scratch codes — treat them like passwords

---

## Lab 3 — View Secret File
```bash
cat ~/.google_authenticator
```
- First line: base32-encoded shared secret
- Remaining lines: configuration flags and emergency backup codes
- This file = ability to generate all future TOTP codes
- Permissions should be 600 — user read/write only

---

## Lab 4 — Install oathtool
```bash
sudo apt install oathtool -y
```
- `oathtool` — command-line OATH/TOTP toolkit
- Generates OTP codes from the command line without a phone
- Useful for scripting and verification

---

## Lab 5 — Generate Current TOTP Code
```bash
SECRET=$(head -1 ~/.google_authenticator)
oathtool --totp --base32 "$SECRET"
```
- `head -1` — reads first line of file = the shared secret
- `$()` — command substitution, stores output in variable
- `oathtool --totp --base32` — generates current TOTP code
- Output should match what your authenticator app shows

---

## Lab 6 — Watch Code Change Over Time
```bash
for i in {1..3}; do
  echo "Code: $(oathtool --totp --base32 "$SECRET")"
  sleep 5
done
```
- Loop runs 3 times with 5-second gaps
- Observe code remains same within 30-second window
- At window boundary, code changes completely

---

## Lab 7 — Check PAM SSH Config
```bash
sudo cat /etc/pam.d/sshd | head -20
sudo grep -i "ChallengeResponse\|AuthenticationMethods\|UsePAM" /etc/ssh/sshd_config
```
- `/etc/pam.d/sshd` — PAM config for SSH service
- In production, adding `auth required pam_google_authenticator.so` here
  enforces TOTP as second factor for SSH login
- `ChallengeResponseAuthentication yes` required for PAM MFA to work

---

## Lab 8 — Show Time Window Remaining
```bash
echo "Current time: $(date)"
echo "Current TOTP: $(oathtool --totp --base32 "$SECRET")"
echo "Time remaining in window: $((30 - $(date +%s) % 30)) seconds"
```
- `date +%s` — Unix timestamp in seconds
- `% 30` — modulo gives seconds into current window
- `30 - result` — seconds until code changes
- Demonstrates why client and server must have synchronized clocks