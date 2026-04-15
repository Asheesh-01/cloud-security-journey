# Day 36 — Biometrics

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 16 — Authentication and Authorization
**Subtopic:** 4 — Biometrics

---

## What It Is

Physical or behavioral characteristics used as "something you are" factor.
Cannot be forgotten or lost. Cannot be changed if compromised.

## Types

**Physiological** — fingerprint, face, iris, retina, hand geometry, vein pattern.
**Behavioral** — keystroke dynamics, mouse movement, gait, voice pattern.

## Key Terms

**FAR** — False Acceptance Rate. Impostors accepted. Security risk.
**FRR** — False Rejection Rate. Legitimate users rejected. Usability risk.
**EER** — Equal Error Rate. Point where FAR = FRR. Lower = better system.
**Liveness detection** — distinguishes live person from spoof (photo, silicone).
**Template** — mathematical representation stored, not raw biometric image.
**Cancelable biometrics** — one-way transform applied to template.
  If stolen, change transform key, re-enroll with new templates.
**Spoofing** — presenting fake biometric to fool sensor.
**Deepfake** — AI-generated audio/video to spoof voice/face recognition.

## Strengths

Cannot be forgotten. Cannot be easily shared. Fast authentication.
Behavioral biometrics work continuously through session.

## Limitations

Cannot be changed if compromised — permanent liability.
FAR/FRR tradeoff — no perfect setting.
Spoofable without liveness detection.
Environmental factors affect accuracy (wet fingers, low light, noise).
Disability and inclusivity — must have fallback options.
Privacy — regulated (Illinois BIPA, GDPR). Facebook: $650M settlement.

## Correct Architecture

Biometric processed locally on device (secure enclave).
Never leaves device. Only cryptographic result transmitted.
FIDO2 passkeys: biometric authorizes private key signing.
Raw biometric never travels. Phishing-resistant. No shared secret.

## Real Incidents

Samsung Galaxy S10 2019: cheap screen protector fooled ultrasonic fingerprint
sensor. Any finger accepted. Software patch required.
Lesson: biometric security = implementation quality, not concept alone.

Clearview AI 2020: scraped billions of faces without consent.
Breached — entire client list stolen.
Billions of permanent biometric records held without people's knowledge.
UK fine: £7.5M. Multiple enforcement actions globally.
Lesson: biometric data requires highest protection. Permanent consequences.

## Commands

```bash
lsusb | grep -i finger
lspci | grep -i finger
which fprintd-enroll 2>/dev/null || echo "No fingerprint hardware"
dpkg -l | grep fprint 2>/dev/null
python3 -c "import time; ..."       # keystroke timing demo
echo -n "biometric_data" | sha256sum  # template concept demo
sudo cat /etc/pam.d/common-auth     # current PAM authentication chain
```