##  Topic 1:
    1. What is a network? Give one real-world analogy. 
    2. What is the difference between a router and a switch? 
    3. What is a protocol? Give two examples. 
    4. What is an IP address? What is a port? How are they different? 
    5. What is a gateway and why do you need one? 
Scenario Question for Topic 1:
    1. You open google.com in your browser. Describe exactly what happens at each network layer — from your application to the physical wires. Include: DNS lookup, ARP, routing, MAC addressing.


## Topic 2
Recall Questions:
    1. Name all 7 OSI layers from top to bottom. What does each layer do? 
    2. What is the difference between a segment, packet, and frame? Which layer creates each? 
    3. At which layer does encryption happen? At which layer does routing happen? 
    4. What is encapsulation? Give an example showing all 7 layers. 
    5. Draw the OSI model by hand. Label each layer with one protocol. 
Scenario Questions:
    1. A user cannot access gmail.com. Walk through each OSI layer to diagnose where the problem could be. Give one example failure at each layer and how you would test it. 
    2. During a security audit, you find that database traffic is unencrypted between web server and database. At which layer is this vulnerability? Which other layers should also be hardened?


## Topic 3
Recall Questions:
    1. Name the 4 TCP/IP layers. What does each do? 
    2. How does TCP/IP Model differ from OSI Model? When would you use each? 
    3. Which layer handles IP addresses? Which handles MAC addresses? 
    4. What is the difference between TCP and UDP? Give one use case for each. 
    5. At which TCP/IP layer does a firewall operate? A router? A switch? 
Scenario Questions:
    1. A developer says their application cannot connect to database. Walk through each TCP/IP layer to diagnose where the problem could be. 
    2. During incident response, you find someone accessed a service at 3 AM. Walk through which TCP/IP layer would show this in logs.


## Topic 4
Recall Questions:
    1. What is the difference between IPv4 and IPv6? Why does IPv6 exist? 
    2. What are the three private IP ranges? Which class do they belong to? 
    3. What is a subnet mask? What does /24 mean? 
    4. What is the difference between static and dynamic IP? When would you use each? 
    5. What is APIPA? When does a device use it? 
Scenario Questions:
    1. A user's computer shows IP 169.254.45.67. What problem occurred? How do you fix it? 
    2. Your company network is 192.168.1.0/24. How many usable host addresses? List the network, broadcast, and gateway IPs.

## Topic 5

Recall Questions:
    1. What is CIDR notation? What does /24 mean? 
    2. How do you calculate the number of usable hosts for a given subnet? 
    3. What is the network address and broadcast address for 192.168.1.0/24? 
    4. What is the subnet mask for /28? 
    5. Why is /30 used for point-to-point links? 
Scenario Questions:
    1. Your company network is 10.0.0.0/16. Divide it into 4 equal subnets. Calculate the network address, first usable host, last usable host, and broadcast address for each subnet. 
    2. A network engineer says they need a subnet with exactly 30 usable hosts. What prefix length (/XX) would you use and why?



## Topic 6

Recall Questions:
    1. What is a MAC address? How is it different from an IP address? 
    2. What is ARP? What problem does it solve? 
    3. What is ARP spoofing? How does it enable man-in-the-middle attacks? 
    4. What is the broadcast MAC address? When is it used? 
    5. What is the OUI in a MAC address? 
Scenario Questions:
    1. You monitor a network and notice two different IPs have the same MAC address. What is happening? How do you respond? 
    2. Walk through how ARP spoofing would work if you were attacking a user on your local network. What would you do step-by-step?

##  Topic 7

Recall Questions:
    1. What is the difference between a router and a switch? 
    2. What is the difference between a firewall, IDS, and IPS? 
    3. At which layers do each device operate? 
    4. What is a stateless firewall vs stateful firewall? 
    5. Why would a company use both IDS and IPS? 
Scenario Questions:
    1. Design network security for e-commerce company with DMZ. Where would you place firewall, IDS, IPS, router, switch? 
    2. Attacker compromises HVAC vendor network. Walk through how proper network segmentation would prevent lateral movement. 

## Topic 8

Recall Questions:
    1. List all 20 ports and their services from memory. 
    2. What is the difference between port 80 and port 443? 
    3. Why is Telnet (port 23) considered insecure? What replaced it? 
    4. What is the difference between POP3 and IMAP? 
    5. Why should database ports (3306, 5432, 27017, 6379) never be exposed to the internet? 
Scenario Questions:
    1. A port scan reveals port 6379 (Redis) open on a public-facing server with no authentication. Explain the risk and what you would recommend. 
    2. You find port 2375 (Docker unencrypted) open on a server. Explain why this is critical and what an attacker could do.

## Topic 9

Recall Questions:
    1. What is the difference between HTTP and HTTPS? 
    2. Why is FTP considered insecure? What should replace it? 
    3. What is the difference between SMTP, POP3, and IMAP? 
    4. How does DNS work? What are DNS records? 
    5. Why is SSH secure while Telnet is not? 
Scenario Questions:
    1. Explain what happens when you type a URL in browser, including which protocols are involved at each step (DNS, TLS, HTTP). 
    2. An attacker on your network intercepts your FTP login. What could they do? How would using SFTP prevent this?

## Topic 10

Recall Questions:
    1. What is the difference between HTTP and HTTPS? 
    2. Why is FTP considered insecure? What should replace it? 
    3. What is the difference between SMTP, POP3, and IMAP? 
    4. How does DNS work? What are DNS records? 
    5. Why is SSH secure while Telnet is not? 
Scenario Questions:
    1. Explain what happens when you type a URL in browser, including which protocols are involved at each step (DNS, TLS, HTTP). 
    2. An attacker on your network intercepts your FTP login. What could they do? How would using SFTP prevent this?



## Topic 11

Recall Questions:
    1. What is the DNS hierarchy? Name each level from root to subdomain. 
    2. What is the difference between A record, AAAA record, and CNAME record? 
    3. What is an MX record? Why does it matter for security? 
    4. What is DNS cache poisoning? How does it work? 
    5. What is DNSSEC and how does it defend against DNS poisoning? 
Scenario Questions:
    1. Walk through complete DNS resolution for mail.google.com from the moment you type it in your browser to when the browser gets the IP address. Include every server contacted. 
    2. An attacker poisoned your company's DNS resolver cache. Users visiting bank.com are landing on a phishing site. What steps do you take immediately and what long-term fixes do you recommend?
	


## Topics 12 and 13

Recall Questions:
    1. What is DHCP? Explain the DORA process step by step. 
    2. What DHCP options are sent alongside the IP address? 
    3. What is a routing table? How does a router decide where to send a packet? 
    4. What is the difference between static and dynamic routing? 
    5. What is the difference between OSPF and BGP? 
Scenario Questions:
    1. Users on your network suddenly cannot get IP addresses. Walk through the steps to diagnose whether this is a DHCP starvation attack or a DHCP server failure. 
    2. You notice all traffic from a specific subnet is being routed through an unexpected IP. What attack does this suggest and how would you respond?

##  Topics 14 and 15
Recall Questions:
    1. What is the difference between a stateless and stateful firewall? 
    2. What are the three main iptables chains and what does each handle? 
    3. What is the difference between a full tunnel VPN and split tunneling? 
    4. What is the difference between IPSec and SSL VPN? 
    5. What is the default deny policy and why is it more secure than default allow? 
Scenario Questions:
    1. A firewall blocks port 443 outbound. What service is affected, what legitimate reason would a security team have for this, and what would the business impact be? 
    2. Colonial Pipeline was breached via VPN with no MFA. What specific controls would you implement to prevent this?


## Topics 16 and 17
Recall Questions:
    1. What is the difference between WPA2 and WPA3? Why is WPA3 more secure? 
    2. Why is WPS considered a security vulnerability? What is the specific attack? 
    3. What is the difference between promiscuous mode and monitor mode? 
    4. What is the difference between tcpdump and Wireshark? When would you use each? 
    5. What is a Wireshark display filter? Give three examples with explanation. 
Scenario Questions:
    1. You capture network traffic and see regular outbound connections every 60 seconds to an unknown IP. What do you suspect and what do you do? 
    2. Users report WiFi is slow. During investigation you notice multiple devices with same SSID as your corporate network but different BSSID. What is happening and how do you respond?


## Topic 18

Topic 18
Recall Questions:
    1. What is the difference between a SYN scan and a connect scan? Why is SYN scan considered stealthier? 
    2. What do open, closed, and filtered port states mean in nmap output? 
    3. What is OS fingerprinting? How does nmap determine the OS of a remote host? 
    4. What is the Nmap Scripting Engine (NSE)? Give two use cases. 
    5. Why should you scan your own systems with nmap regularly? 
Scenario Questions:
    1. You are a security engineer. Your manager asks you to find all devices on the 10.0.0.0/24 network that have port 3306 (MySQL) open and exposed. Write the exact nmap command and explain every flag. 
    2. An nmap scan of a server returns port 22 open with OpenSSH 7.2p2. Why is this a security concern and what do you do? 