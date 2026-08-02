# CAPTURING HTTP CREDENTIALS

---

## Our Goal

We want to see how attackers capture usernames and passwords on a network. We'll generate HTTP traffic (which is unencrypted) and find credentials in Wireshark.

---

## Method 1: Using curl (Most Reliable)

```bash
curl -v -X POST http://httpbin.org/post -d "username=asheesh_test" -d "password=hunter123" -d "email=asheesh@test.com"
Breakdown:

curl: Command-line tool for sending HTTP requests

-v: Verbose mode - shows everything happening in detail

-X POST: Specifies POST method (used for submitting forms)

http://httpbin.org/post: The URL (http:// ensures it's unencrypted)

-d: Data to send in the request body

"username=asheesh_test": The data we're sending (our test credentials)

What this does: Sends a POST request with test credentials to httpbin.org. The credentials are sent in plaintext because we used http:// not https://.

Method 2: Using Browser
Action: Visit http://httpbin.org/forms/post

What it does: Opens a test form page. Fill in fields and click submit.

Why this works: httpbin.org is a testing service that echoes back what you send. Using http:// ensures traffic is unencrypted.

Method 3: Using neverssl.com
Action: Visit http://neverssl.com

What it does: This site is designed specifically to be HTTP-only. It never uses HTTPS, perfect for testing.

Finding Credentials in Wireshark
Step 1: Start capture on Wi-Fi interface
What: Begin capturing all packets

Step 2: Run curl command or submit form
What: Generate the traffic we want to capture

Step 3: Stop capture
What: Stop capturing to analyze

Step 4: Apply filter:
text
http.request.method == "POST"
What this does: Shows only POST requests (form submissions). POST requests contain the data sent to the server.

Step 5: Click on the POST packet
What: Select the packet to examine its details

Step 6: Expand in packet details:
Hypertext Transfer Protocol

HTML Form URL Encoded: application/x-www-form-urlencoded

What this shows: The form data sent by the client. This is where credentials appear.

What We See
In the packet details, we see:

text
username=asheesh_test&password=hunter123&email=asheesh%40test.com
The %40 is URL encoding for the @ symbol.

Why This Matters
On an open Wi-Fi network (like a coffee shop), ANYONE on the same network can use Wireshark and see:

Which websites you're visiting

What you're typing in forms

Your usernames and passwords (if sent over HTTP)

How Attackers Use This
Attacker connects to public Wi-Fi

Opens Wireshark and starts capturing

Waits for users to visit HTTP websites

Collects credentials from POST requests

Uses these credentials to access accounts

Defense
Always use HTTPS. HTTPS encrypts the entire packet content. Even if an attacker captures the packet, they can't read the username and password.

Our Intention
We're learning this to understand the threat. This helps us:

Know why HTTPS is essential

Understand how public Wi-Fi is dangerous

See exactly what attackers see

Better protect our own networks

As future SOC analysts, understand what to look for

🔐 Security Angle
Real Breach Reference: In 2017, the Equifax breach exposed 147 million records partly because attackers captured unencrypted HTTP traffic between internal systems.

Defense: Always use HTTPS. Use browser extensions like HTTPS Everywhere. Never enter sensitive information on HTTP sites.

text

---

