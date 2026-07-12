# Day 88 — Application Protocols (HTTP, HTTPS, FTP, SMTP, DNS, SSH)

**Volume:** 03 — Networking From Zero
**Topic:** 10 — Application Protocols
**Layer:** Layer 7 (Application)
**Focus:** Understanding what each protocol does and security implications.

---

## What is an Application Protocol

Set of rules defining how applications communicate. Sits on top of TCP/UDP.

Examples:
- **HTTP** — web browser to web server
- **SSH** — local terminal to remote server
- **SMTP** — email client to email server
- **DNS** — resolver to DNS server

Each protocol uses specific port, defines message format, handles errors.

---

## HTTP (HyperText Transfer Protocol)

**Port:** 80
**Transport:** TCP
**Encryption:** None (cleartext)
**Use:** Unencrypted web traffic (legacy)

### Request Format
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: text/html
Connection: close

- `GET` = request method (also POST, PUT, DELETE, etc.)
- `/index.html` = resource path
- `HTTP/1.1` = protocol version
- Headers provide metadata

### Response Format
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1234
Connection: close
<html>
<body>Hello World</body>
</html>
````

200 = status code (success)
Headers describe content
Body is actual data (HTML, JSON, etc.)

Common Status Codes
CodeMeaningExample200OK — successPage loaded301Moved PermanentlyRedirect to HTTPS404Not FoundPage doesn't exist403ForbiddenNo permission500Internal Server ErrorServer crashed
Security Risk
Cleartext: Attacker intercepts HTTP traffic, sees:

What website you're visiting
What you search for
What you type in forms
Responses from server
Cookies (used for session tracking)

Defense: Migrate to HTTPS.

HTTPS (HTTP Secure)
Port: 443
Transport: TCP
Encryption: TLS/SSL
Use: Encrypted web traffic (modern standard)
How It Works
1. Client connects to server (TCP connection)

2. TLS Handshake:
   - Server sends certificate (proves identity)
   - Client and server negotiate encryption
   - Shared secret established

3. All HTTP traffic encrypted:
   - GET request encrypted
   - HTML response encrypted
   - Cookies encrypted

4. Attacker sees only encrypted bytes
   - Cannot read content
   - Cannot modify pages
   - Cannot steal credentials
Browser Indication

URL starts with https://
Green lock icon (certificate valid)
Gray lock icon (certificate problem)
No icon or red warning (certificate invalid or expired)

Certificate
Proves website is legitimate. Issued by Certificate Authority (CA).
Contains:

Website domain name
Public key
Expiration date
CA signature (proves authenticity)


FTP (File Transfer Protocol)
Port: 21 (control) + 20 (data)
Transport: TCP (two separate channels)
Encryption: None (cleartext)
Use: Unencrypted file transfer (legacy)
How It Works
Control Channel (port 21):
- Client: "USER username"
- Server: "331 Password required"
- Client: "PASS password" ← In cleartext!
- Server: "230 Login successful"
- Client: "RETR file.txt" (retrieve file)

Data Channel (port 20):
- Server sends file contents ← In cleartext!
Security Risk
Cleartext: Attacker on network sniffs:

Username and password (plain text)
File contents being transferred
File listing commands

Defense: Use SFTP (SSH File Transfer Protocol) instead.

SFTP (SSH File Transfer Protocol)
Port: 22
Transport: TCP over SSH
Encryption: Yes (SSH encryption)
Use: Encrypted file transfer (modern)
Same functionality as FTP but all traffic encrypted via SSH tunnel.
Everything protected:

Credentials encrypted
File contents encrypted
Commands encrypted


SMTP (Simple Mail Transfer Protocol)
Port: 25 (plain) or 587 (submission with TLS)
Transport: TCP
Encryption: Optional TLS
Use: Sending email between servers
Message Format
MAIL FROM: sender@example.com
RCPT TO: recipient@example.com
DATA
From: Sender <sender@example.com>
To: Recipient <recipient@example.com>
Subject: Hello

This is the email body.
.
QUIT
Security Risk
Without TLS: Email and credentials sent cleartext.
With TLS: Encrypted, secure.

POP3 (Post Office Protocol v3)
Port: 110 (plain) or 995 (with TLS)
Transport: TCP
Encryption: Optional TLS
Use: Downloading email from server
How It Works
Client connects, authenticates, downloads emails.
Downloaded emails typically REMOVED from server.
Good for single device (phone or laptop).
Limitation
Email only on single device. Switch devices = emails not synced.

IMAP (Internet Message Access Protocol)
Port: 143 (plain) or 993 (with TLS)
Transport: TCP
Encryption: Optional TLS
Use: Downloading email from server (synced)
How It Works
Client connects, authenticates, downloads email headers.
Emails remain on server (not deleted).
Switch devices = emails synced everywhere.
Good for multiple devices (phone, laptop, tablet).
Advantage over POP3
Email synchronized across all devices accessing same mailbox.

DNS (Domain Name System)
Port: 53 (UDP for queries, TCP for zone transfers)
Transport: UDP (most) or TCP (large responses)
Encryption: None (cleartext queries and responses)
Use: Translating domain names to IP addresses
Query Process
1. Client asks local DNS: "What IP is google.com?"

2. Local DNS asks root nameserver: "Where is .com?"
   Root responds: "Ask these nameservers"

3. Local DNS asks TLD nameserver (.com): "Where is google.com?"
   TLD responds: "Ask google's nameservers"

4. Local DNS asks google's nameserver: "What IP is google.com?"
   Responds: "142.250.195.46"

5. Local DNS caches and responds to client:
   "google.com is 142.250.195.46"
Record Types
TypePurposeAMaps domain to IPv4 addressAAAAMaps domain to IPv6 addressMXMail exchange server (where to send email)CNAMECanonical name (alias to another domain)TXTText record (SPF, DKIM, DMARC for email security)NSNameserver (who manages this domain)PTRPointer record (reverse DNS, IP to domain)
Security Risk
Cleartext queries: Attacker sees:

What websites you're visiting (can log all DNS queries)

DNS Spoofing: Attacker responds to query with fake IP:

User asks for bank.com
Attacker responds first with attacker's IP
User connects to attacker's server
User sees fake login page, enters credentials
Credentials stolen

Defense: DNSSEC (cryptographic signatures validate responses).

SSH (Secure Shell)
Port: 22
Transport: TCP
Encryption: Yes (entire connection encrypted)
Use: Encrypted remote terminal access + file transfer
How It Works
1. SSH Handshake:
   - Server sends public key + certificate
   - Client and server negotiate encryption algorithm
   - Shared encryption key established

2. Encrypted connection (all traffic encrypted):
   - Client sends: ssh -u username@host
   - Server asks: Password?
   - Client sends password (encrypted)
   - Server authenticates

3. Terminal session (all encrypted):
   - Client types: "ls -la"
   - Server executes and responds: file listing
   - All encrypted end-to-end

4. File transfer (SFTP):
   - Client: "Get remote_file.txt"
   - Server sends file contents (encrypted)
Advantages

Entire session encrypted (terminal output, commands, files)
Public key authentication option (better than passwords)
Replaced unencrypted Telnet

Replaced Telnet
Telnet (port 23) was same idea but UNENCRYPTED.
Passwords sent in cleartext. Attacker on network could:

See admin password
Impersonate admin
Control server

SSH encrypts everything, making Telnet obsolete (except legacy systems).

Telnet (Historical, DO NOT USE)
Port: 23
Transport: TCP
Encryption: None (cleartext)
Use: Unencrypted remote terminal access (legacy)
Why Dangerous
Everything sent in plaintext:

Username: admin (visible to attacker)
Password: SecurePass123 (visible to attacker)
Commands: rm -rf / (visible to attacker)
Responses (full terminal output): visible to attacker

Network admin logs in via Telnet, attacker intercepts password, becomes admin.
Completely obsolete. Replaced by SSH.

Protocol Security Summary
Cleartext (Avoid):

HTTP (use HTTPS)
FTP (use SFTP)
Telnet (use SSH)
Unencrypted SMTP/POP3/IMAP (use TLS versions)
DNS (use DNSSEC if possible)

Encrypted (Use):

HTTPS (HTTP over TLS)
SFTP (FTP over SSH)
SSH (remote terminal)
SMTP+TLS, POP3+TLS, IMAP+TLS (email with encryption)


Real Incident — Yahoo Email Breach 2013-2014
What happened:

Vendor used FTP (cleartext) to upload files to Yahoo
Attacker sniffed FTP credentials
Accessed Yahoo's systems via unencrypted protocols
Saw email, personal messages, attachments (all cleartext transmission)

Impact: 1 billion user accounts compromised.
Fix: Yahoo moved to HTTPS-only, SMTP+TLS, disabled FTP, enforced SFTP, encrypted storage.

Interview Questions
Q1: What's the difference between HTTP and HTTPS?
A1: HTTP (port 80) sends web traffic in cleartext — anyone intercepting can see requests, responses, passwords, cookies. HTTPS (port 443) encrypts all traffic with TLS — attacker sees only encrypted bytes. Modern websites should always use HTTPS.
Q2: Why is FTP insecure and what should replace it?
A2: FTP sends credentials and file contents in cleartext. Attacker on same network sniffs password and files. SFTP (SSH File Transfer Protocol) encrypts everything, making it secure. SFTP should replace FTP.
Q3: Explain DNS spoofing attack.
A3: DNS queries are cleartext and responses not validated. Attacker intercepts query "What IP is bank.com?" and responds first with attacker's IP instead of real bank IP. User's browser connects to attacker's server (looks like real bank). User enters login credentials, attacker steals them. Defense: DNSSEC validates responses cryptographically.