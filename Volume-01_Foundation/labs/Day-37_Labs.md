# Day 37 — Labs: Tokens

**Environment:** Kali Linux VM

---

## Lab 1 — Install JWT Library
```bash
pip3 install PyJWT --break-system-packages
pip3 install requests --break-system-packages
```
- `pip3 install PyJWT` — Python JWT library for creating and validating JWTs
- `--break-system-packages` — required on modern Kali to install outside venv
- `requests` — HTTP library for making API calls with bearer tokens

---

## Lab 2 — Create a JWT Token
```python
python3 -c "
import jwt, datetime
SECRET = 'my-super-secret-key-that-is-long-enough'
payload = {
    'sub': 'user123',
    'name': 'Asheesh Sharma',
    'role': 'analyst',
    'iat': datetime.datetime.utcnow(),
    'exp': datetime.datetime.utcnow() + datetime.timedelta(hours=1)
}
token = jwt.encode(payload, SECRET, algorithm='HS256')
print('JWT Token:'); print(token)
"
```
- `jwt.encode()` — signs payload with secret using HS256 algorithm
- Output is three Base64url parts joined by dots: header.payload.signature
- Copy this token for use in subsequent labs

---

## Lab 3 — Decode and Verify JWT
```python
python3 -c "
import jwt, datetime
SECRET = 'my-super-secret-key-that-is-long-enough'
payload = {'sub':'user123','role':'analyst','exp':datetime.datetime.utcnow()+datetime.timedelta(hours=1)}
token = jwt.encode(payload, SECRET, algorithm='HS256')
decoded = jwt.decode(token, SECRET, algorithms=['HS256'])
print('Decoded payload:')
for k,v in decoded.items(): print(f'  {k}: {v}')
"
```
- `jwt.decode()` — verifies signature AND decodes payload in one call
- Raises InvalidSignatureError if signature does not match
- Raises ExpiredSignatureError if token is past expiry time

---

## Lab 4 — Prove Payload Is Not Encrypted
```python
python3 -c "
import jwt, base64, datetime
SECRET = 'my-super-secret-key-that-is-long-enough'
payload = {'sub':'user123','role':'analyst','exp':datetime.datetime.utcnow()+datetime.timedelta(hours=1)}
token = jwt.encode(payload, SECRET, algorithm='HS256')
parts = token.split('.')
payload_decoded = base64.b64decode(parts[1] + '==').decode()
print('Payload readable WITHOUT secret key:')
print(payload_decoded)
print()
print('Base64 is encoding, not encryption. Never store sensitive data in JWT payload.')
"
```
- `token.split('.')` — splits JWT into header, payload, signature parts
- `base64.b64decode(parts[1])` — decodes payload with NO secret key
- Proves: token payload is fully readable to anyone who intercepts it

---

## Lab 5 — Demonstrate Signature Rejection
```python
python3 -c "
import jwt, datetime
SECRET = 'my-super-secret-key-that-is-long-enough'
WRONG = 'wrong-secret-key'
payload = {'sub':'user123','exp':datetime.datetime.utcnow()+datetime.timedelta(hours=1)}
token = jwt.encode(payload, SECRET, algorithm='HS256')
try:
    jwt.decode(token, WRONG, algorithms=['HS256'])
    print('ERROR: Should not succeed')
except jwt.InvalidSignatureError:
    print('Signature FAILED with wrong secret — correct behavior')
    print('Attacker cannot forge tokens without the secret key')
"
```
- Wrong secret causes `InvalidSignatureError`
- Confirms: signature prevents forging, even if attacker reads the payload

---

## Lab 6 — Audit Shell Files for Hardcoded Tokens
```bash
grep -r "api_key\|api-key\|apikey\|secret_key\|access_token" \
  ~/.bashrc ~/.bash_history ~/.profile 2>/dev/null | head -10 || echo "No API keys found"
```
- `grep -r` — recursive pattern search
- Searches common shell config files for credential variable names
- Finding credentials here = immediate finding in real security audit
- Production: use environment variables injected at runtime, not hardcoded

---

## Lab 7 — Simulate AWS STS Temporary Credentials
```python
python3 -c "
import secrets, datetime
temp_creds = {
    'AccessKeyId': 'ASIA' + secrets.token_hex(8).upper(),
    'SecretAccessKey': secrets.token_urlsafe(40),
    'SessionToken': secrets.token_urlsafe(100),
    'Expiration': (datetime.datetime.utcnow() + datetime.timedelta(hours=1)).isoformat()
}
print('Simulated AWS STS Temporary Credentials:')
for k,v in temp_creds.items():
    print(f'  {k}: {v[:30]}...' if len(str(v)) > 30 else f'  {k}: {v}')
print()
print('ASIA prefix = temporary STS credential (expires in 1 hour)')
print('AKIA prefix = long-term IAM user key (never expires)')
print('Always prefer temporary credentials.')
"
```
- `secrets.token_hex()` — cryptographically secure random hex
- ASIA prefix simulates real AWS temporary credential format
- Demonstrates expiry difference between temporary and permanent credentials