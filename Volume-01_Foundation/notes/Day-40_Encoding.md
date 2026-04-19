# Day 40 — Encoding

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 17 — Data Protection Basics
**Subtopic:** 3 — Encoding

---

## What It Is

Reversible format conversion. No key. No secret. Not security.
Purpose: compatibility and transmission between different systems.

## Key Terms

**Base64** — converts binary to 64 printable ASCII chars.
A-Z, a-z, 0-9, +, /. Padding with =. ~33% size increase.
No key to decode. Anyone can reverse it instantly.

**Base64url** — URL-safe variant. + → -, / → _. Used in JWTs.

**URL encoding** — % + two hex digits. Space = %20. / = %2F.
Makes special URL characters safe as data.

**HTML encoding** — < → &lt;, > → &gt;, & → &amp;.
Prevents browser from interpreting user input as HTML tags.
Critical for XSS prevention — Volume 5.

**Hex encoding** — each byte as two hex characters. Output of sha256sum.

**ASCII** — 128 characters mapped to 0-127. A=65, space=32.
**UTF-8** — backward-compatible with ASCII. Supports all languages.

## Encoding Is NOT Security

Base64 requires no key to decode. Zero confidentiality protection.
Encoding-as-security failures:

HTTP Basic Auth — credentials as Base64 in Authorization header.
`Authorization: Basic dXNlcjpwYXNzd29yZA==` → decoded instantly → user:password.
Over HTTP: completely exposed. Over HTTPS: TLS provides actual encryption.

Hardcoded encoded credentials — Base64 in config files is not obfuscation.
One command decodes it. Attackers always try this.

JWT payload — Base64url encoded, not encrypted.
Any intercepted JWT has fully readable payload with no key.

## Where Encoding Legitimately Appears

PEM format — certificates and keys in Base64 with BEGIN/END headers.
Security comes from cryptography, not the encoding.

Malware obfuscation — `powershell -EncodedCommand <Base64>`.
Common IOC. Decode to reveal actual malicious command.
Detection skill, not a security control.

Network/log analysis — URL-encoded params, Base64 POST bodies.
Must understand encoding to read what was transmitted.

XSS bypass — `<script>` blocked → try `%3Cscript%3E` → try `%253Cscript%253E`.
Double encoding defeats WAFs that only check one decode layer.

## The Three-Way Distinction

Encryption: reversible WITH key. Confidentiality.
Hashing: irreversible. Integrity and password verification.
Encoding: reversible WITHOUT key. Compatibility only.

## Commands

```bash
echo -n "text" | base64                      # encode
echo "Base64string==" | base64 -d            # decode
echo -n "admin:password" | base64            # HTTP Basic Auth demo
python3 -c "from urllib.parse import quote, unquote; ..."  # URL encoding
python3 -c "import html; html.escape('<script>alert(1)</script>')"
echo -n "text" | xxd -p                      # hex encode
grep -E "[A-Za-z0-9+/]{40,}={0,2}" logfile  # find Base64 in logs
echo "suspiciousBase64==" | base64 -d        # decode to reveal payload
```