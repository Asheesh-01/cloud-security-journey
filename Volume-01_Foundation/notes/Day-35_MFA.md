# Day 35 — Multi-Factor Authentication

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 16 — Authentication and Authorization
**Subtopic:** 3 — Multi-Factor Authentication

---

## What It Is

Two or more independent factors from different categories.
Blocks 99%+ of automated account compromise attacks (Microsoft data).
Stolen password alone is not enough to authenticate.

## Three Factor Categories

**Something you know** — password, PIN. Weakest — can be stolen or phished.
**Something you have** — phone, hardware key. Cannot be stolen remotely.
**Something you are** — biometrics. Cannot be shared.

True MFA = factors from different categories. Two passwords is not MFA.

## MFA Types — Weakest to Strongest

SMS code — SIM swappable, SS7 interceptable. Weakest.
Email OTP — slightly better, but email itself can be compromised.
TOTP (authenticator app) — changes every 30 seconds. Not remotely interceptable.
Push notification — convenient, vulnerable to MFA fatigue.
Hardware key (FIDO2/WebAuthn) — phishing-resistant, requires physical presence.
Passkey — replaces passwords entirely. Cryptographic key pair per site.

## Key Terms

**TOTP** — Time-based OTP. 6-digit code every 30 seconds. RFC 6238.
Derived from shared secret + current time. Expires automatically.

**Authenticator app** — Google Authenticator, Microsoft Authenticator, Authy.
Stores shared secret, generates TOTP codes offline.

**Hardware key** — YubiKey, FIDO2. Physical device. Touch = physical presence proof.
Verifies domain cryptographically — fake phishing sites cannot trick it.

**MFA fatigue attack** — repeated push notifications until user approves.
Fix: number matching — user must confirm a number, not just tap approve.

**SIM swapping** — attacker convinces carrier to transfer phone number.
All SMS including MFA codes go to attacker. Why SMS MFA is weak.

**Backup codes** — one-time use. Bypass MFA if device lost.
Treat like passwords — store securely, never expose.

**Passkey** — FIDO2/WebAuthn. Private key on device, public key on server.
Requires device possession + biometric. No shared secret. Phishing-resistant.

## Real Breach — Uber 2022

Contractor credentials bought from dark web marketplace.
MFA (push notification) enabled — password alone not enough.
Attacker: WhatsApp message claiming to be Uber IT, asked contractor to approve.
Repeated push notifications — contractor approved one.
Result: AWS, GCP, HackerOne access. All known unpatched vulnerabilities exposed.
Fix: number matching eliminates MFA fatigue. Uber implemented it post-breach.
Second failure: hardcoded credentials in internal PowerShell scripts on file share.

## Commands

```bash
sudo apt install libpam-google-authenticator -y
google-authenticator
cat ~/.google_authenticator
sudo apt install oathtool -y
SECRET=$(head -1 ~/.google_authenticator)
oathtool --totp --base32 "$SECRET"
for i in {1..3}; do echo "Code: $(oathtool --totp --base32 "$SECRET")"; sleep 5; done
sudo cat /etc/pam.d/sshd | head -20
sudo grep -i "ChallengeResponse\|AuthenticationMethods\|UsePAM" /etc/ssh/sshd_config
echo "Current TOTP: $(oathtool --totp --base32 "$SECRET")"
echo "Time remaining: $((30 - $(date +%s) % 30)) seconds"
```