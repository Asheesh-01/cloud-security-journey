# Day 40 — Labs: Encoding

**Environment:** Kali Linux VM

---

## Lab 1 — Base64 Encode and Decode
```bash
echo -n "Hello, this is plaintext" | base64
echo -n "Hello, this is plaintext" | base64 | base64 -d
```
- `base64` — encodes input to Base64 string
- `base64 -d` — decodes Base64 back to original
- No key required at any step — proves encoding is not security

---

## Lab 2 — Prove Basic Auth Is Not Protected
```bash
echo -n "admin:supersecretpassword" | base64
echo "YWRtaW46c3VwZXJzZWNyZXRwYXNzd29yZA==" | base64 -d
echo
```
- Encodes credentials exactly as HTTP Basic Auth does
- Decodes them back with no key
- HTTP Basic Auth over plain HTTP exposes credentials to anyone on network path

---

## Lab 3 — JWT Payload Decode (No Key)
```bash
PAYLOAD='{"sub":"user123","role":"admin","exp":9999999999}'
echo -n "$PAYLOAD" | base64 | tr -d '='
echo -n "$PAYLOAD" | base64 | base64 -d
```
- `tr -d '='` — removes Base64 padding (Base64url style used in JWTs)
- Full payload readable with no secret key
- Confirms: JWT payload is encoded, not encrypted

---

## Lab 4 — URL Encoding
```python
python3 -c "
from urllib.parse import quote, unquote
original = 'search?q=hello world&category=security/tools'
encoded = quote(original, safe='?=&')
print('Original:', original)
print('Encoded:', encoded)
print('Decoded:', unquote(encoded))
"
```
- `quote()` — URL encodes special characters
- `unquote()` — reverses URL encoding
- Safe characters specified are not encoded

---

## Lab 5 — HTML Encoding (XSS Prevention)
```python
python3 -c "
import html
user_input = '<script>alert(\"XSS\")</script>'
print('Raw:', user_input)
print('HTML encoded:', html.escape(user_input))
print('Encoded version renders as text, not executable script')
"
```
- `html.escape()` — converts HTML special chars to entities
- Without encoding: browser executes injected JavaScript
- With encoding: browser displays it as literal text

---

## Lab 6 — Hex Encoding
```bash
echo -n "password" | xxd
echo -n "password" | xxd -p
```
- `xxd` — hex dump with address and ASCII columns
- `xxd -p` — plain hex, no extra columns
- Each character = two hex digits

---

## Lab 7 — Decode Malware PowerShell Command
```bash
ENCODED_CMD=$(echo -n "Get-Process | Where-Object {$_.CPU -gt 50}" | iconv -t UTF-16LE | base64 -w 0)
echo "Malware command: powershell -EncodedCommand $ENCODED_CMD"
echo "$ENCODED_CMD" | base64 -d | iconv -f UTF-16LE -t UTF-8 2>/dev/null
```
- PowerShell EncodedCommand uses UTF-16LE + Base64
- `iconv -t UTF-16LE` — converts to PowerShell's expected encoding
- Decoding reveals the actual command — core incident response skill

---

## Lab 8 — Find Base64 in Log Files
```bash
echo "Normal log entry user login success" > /tmp/suspicious_log.txt
echo "aW1wb3J0IG9zOyBvcy5zeXN0ZW0oJ3dob2FtaScpCg==" >> /tmp/suspicious_log.txt
grep -E "[A-Za-z0-9+/]{40,}={0,2}" /tmp/suspicious_log.txt
echo "aW1wb3J0IG9zOyBvcy5zeXN0ZW0oJ3dob2FtaScpCg==" | base64 -d
rm /tmp/suspicious_log.txt
```
- `grep -E "[A-Za-z0-9+/]{40,}={0,2}"` — finds Base64-looking strings
- 40+ character threshold reduces false positives
- Decoding the found string reveals: `import os; os.system('whoami')`
- This is how investigators find encoded payloads in compromised system logs