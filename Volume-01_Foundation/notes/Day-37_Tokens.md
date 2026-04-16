# Day 37 — Tokens

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 16 — Authentication and Authorization
**Subtopic:** 5 — Tokens

---

## What It Is

Data representing authenticated identity. Presented with requests after login.
Avoids re-authenticating for every action.

## Key Terms

**Session token** — random string, server looks it up in session table.
Requires server-side storage. Simple.

**JWT** — JSON Web Token. Self-contained. Three parts: header.payload.signature
Header: algorithm and type. Payload: claims. Signature: tamper proof.

**Claim** — key-value pair in JWT payload. sub, iat, exp, iss are standard.
Custom claims: role, email, permissions.

**Bearer token** — possession = access. Authorization: Bearer <token>.
Steal the token = steal the session.

**OAuth 2.0** — authorization framework. "Log in with Google."
User authenticates to provider. Provider issues token to third-party app.
App accesses user data with token — only what user approved.

**Access token** — short-lived (minutes to hours). Used in API calls.
**Refresh token** — long-lived (days to weeks). Gets new access tokens.

**API key** — identifies application, not user. Often permanent.
Leaked = permanent access until manually revoked.

**AWS STS** — issues temporary credentials: access key + secret + session token.
AKIA prefix = long-term IAM key. ASIA prefix = temporary STS credential.
Always prefer STS temporary credentials over long-term IAM access keys.

## JWT Security Vulnerabilities

**None algorithm** — legacy systems accepted alg:none, no signature required.
Attacker forges any payload. Fixed in modern libraries, still in legacy.

**Weak secret** — HS256 with short/dictionary secret is crackable.
Attacker cracks secret, forges tokens. Secret must be long and random.

**Not verifying signature** — reading claims before verifying signature.
Forged token with any payload accepted.

**Sensitive data in payload** — Base64 is NOT encryption.
Anyone with token reads payload without secret key. Never store secrets in payload.

**Token theft** — stolen bearer token = stolen session until expiry.
Store in httpOnly cookies, not localStorage. Require HTTPS always.

## AWS Connection

EC2 instance metadata: 169.254.169.254 — STS temp credentials available here.
Capital One 2022: SSRF → metadata → STS tokens → S3 access (overprivileged role).
Tokens expired — but damage already done. Least privilege limits window impact.

## Commands

```bash
pip3 install PyJWT --break-system-packages
pip3 install requests --break-system-packages
python3 -c "import jwt; ..."         # create, encode, decode JWT
base64.b64decode(jwt_part + '==')   # decode payload without secret key
jwt.decode(token, WRONG_SECRET)     # InvalidSignatureError = correct behavior
grep -r "api_key\|secret_key\|access_token" ~/.bashrc ~/.bash_history 2>/dev/null
python3 -c "import secrets; ..."    # simulate AWS STS temp credentials
```