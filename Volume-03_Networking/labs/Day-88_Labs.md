
If SSH server not running locally, try public server (if you have one).

---

## Lab 7 — DNS Reverse Lookup (PTR Record)

```bash
dig -x 8.8.8.8
```

Asks: "What domain owns IP 8.8.8.8?"

Output: `dns.google. (Google's reverse DNS)`

Reverse DNS used to verify email servers (part of SMTP authentication).

---

## Lab 8 — SMTP Test (Simple)

```bash
nc -v smtp.gmail.com 587
```

Shows SMTP server response (cleartext greeting).

Type (or copy-paste):
EHLO client
QUIT

(Exits without authenticating)

This demonstrates SMTP cleartext protocol. Real email clients encrypt this.

---

## Lab 9 — Check SSL Certificate

```bash
curl -v https://google.com 2>&1 | grep "SSL"
```

Shows certificate info:
- Certificate subject (domain name)
- Issuer (Certificate Authority)
- Expiration date
- Valid or invalid status

Good certificates show "certificate verify ok".

---

## Lab 10 — View HTTP Headers Only

```bash
curl -I https://google.com
```

`-I` shows headers only (no body).

Output:
HTTP/2 200
content-type: text/html; charset=ISO-8859-1
server: gws

Shows protocol, content type, server info (headers alone).

---

## Lab 11 — POST Request (Sends Data)

```bash
curl -X POST https://httpbin.org/post \
  -d "username=testuser&password=testpass"
```

Sends data to server (encrypted via HTTPS).

Output shows received data (httpbin echoes it back).

This is how form submissions work.

---

## Lab 12 — FTP (If Available - Educational Only)

Not recommended in real use, but for understanding:

```bash
ftp ftp.example.com
```

You'd enter username/password (cleartext).

Commands show files, etc.

This demonstrates why FTP is dangerous (credentials visible).

---

## Lab 13 — DNS Query Timing

```bash
time dig google.com
```

Timing shows how long DNS query takes.

Typical: 10-100ms (DNS servers respond fast).

Used to understand network performance and detect DNS issues.

---

## Lab 14 — Find MX Records (Mail Servers)

```bash
dig google.com MX
```

Shows email servers for domain:
ANSWER SECTION:
google.com.	3600	IN	MX	10 smtp.google.com.
google.com.	3600	IN	MX	20 aspmx.l.google.com.

MX records used by other mail servers to send emails.

---

## Lab 15 — Interview Scenario: Capture HTTP vs HTTPS

In one terminal, start capture:

```bash
sudo tcpdump -i lo -A 'port 80 or port 443'
```

In another terminal, make requests:

```bash
curl http://example.com    # Cleartext - see data in tcpdump
curl https://example.com   # Encrypted - see only encrypted bytes in tcpdump
```

Demonstrates difference: HTTP data visible, HTTPS not.

---

## Lab 16 — SSH Public Key Authentication

Generate SSH key (if not present):

```bash
ssh-keygen -t rsa -b 4096
```

This creates:
- `~/.ssh/id_rsa` (private key, keep secret)
- `~/.ssh/id_rsa.pub` (public key, share with servers)

Copy public key to server:

```bash
ssh-copy-id user@remote_server
```

Now SSH works without password (key-based auth, more secure).

---

## Lab 17 — Speed Test: Connection Establishment

**HTTP (no handshake overhead):**

```bash
time curl -I http://example.com
```

**HTTPS (TLS handshake overhead):**

```bash
time curl -I https://example.com
```

HTTPS takes longer due to TLS handshake (key exchange, certificate validation).

Modern browsers mitigate with connection reuse and HTTP/2.

---

## Lab 18 — Protocol Analysis Summary

Create summary document:

**Protocol** | **Port** | **Encrypted** | **Use** | **Security Risk**
HTTP | 80 | No | Web (legacy) | Cleartext
HTTPS | 443 | Yes | Web (modern) | Low
SSH | 22 | Yes | Terminal | Low
FTP | 21 | No | File (legacy) | Credentials + files cleartext
SFTP | 22 | Yes | File (modern) | Low
SMTP | 25 | No | Email (legacy) | Cleartext
DNS | 53 | No | Queries | Spoofing, snooping

Understand tradeoffs (speed vs security).