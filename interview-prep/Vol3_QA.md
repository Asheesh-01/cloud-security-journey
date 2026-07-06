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