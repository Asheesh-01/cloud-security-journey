# WIRESHARK INSTALLATION & SETUP

---

## Our Goal

We want to install Wireshark on Ubuntu 24.04 so we can capture and analyze network traffic on our home network. We need to properly configure permissions so we can capture packets without using sudo (which gives full root access).

---

## Step 1: Install Wireshark

```bash
sudo apt update
What it does: Updates the package list from Ubuntu repositories so we get the latest available versions.

bash
sudo apt install wireshark -y
What it does: Installs Wireshark. The -y flag automatically answers "yes" to installation prompts.

Step 2: Handle Permission Issue
Problem: By default, only root can capture network packets. We need to allow our user to do this.

bash
sudo usermod -a -G wireshark $USER
What it does: Adds our user ($USER is a variable that holds our username) to the 'wireshark' group. This group is created during installation and has permission to capture packets.

bash
sudo chgrp wireshark /usr/bin/dumpcap
What it does: Changes the group ownership of the dumpcap file (the program that actually captures packets) from 'root' to 'wireshark' group.

bash
sudo chmod 750 /usr/bin/dumpcap
What it does: Sets permissions on dumpcap so:

Owner (root) can read, write, execute (7)

Group (wireshark) can read and execute (5)

Others have no permissions (0)

bash
sudo setcap cap_net_raw,cap_net_admin=eip /usr/bin/dumpcap
What it does: This is the critical command. 'setcap' sets capabilities on a file.

cap_net_raw: Allows capturing raw network packets

cap_net_admin: Allows network administration tasks

=eip: Means effective, inheritable, and permitted

This gives dumpcap the specific permissions it needs WITHOUT giving it full root access.

Step 3: Verify Everything Works
bash
ls -l /usr/bin/dumpcap
What it does: Shows the permissions of dumpcap. Should show -rwxr-xr-- 1 root wireshark ... meaning root owns it, wireshark group has read/execute.

bash
getcap /usr/bin/dumpcap
What it does: Shows the capabilities set on dumpcap. Should show cap_net_admin,cap_net_raw=eip.

Step 4: Find Network Details
bash
ip addr show wlp85s0f0
What it does: Shows IP address details for our Wi-Fi interface. We found:

IP: 10.26.4.216/20 (our computer's address on the network)

MAC: e8:bf:b8:64:6b:df (hardware address of our Wi-Fi card)

bash
ip route | grep default
What it does: Shows the default gateway (router) IP. We found:

Router: 10.26.0.1 (the device that connects our network to the internet)

Why We Did This
We installed Wireshark to capture network packets

We fixed permissions so we can capture without full root access (security best practice - Principle of Least Privilege)

We found our network details so we know what network we're working on

This is the foundation for all our future experiments

Verification
After these steps, when we open Wireshark by clicking the icon, all network interfaces (wlp85s0f0, docker0, lo, etc.) appear correctly.

🔐 Security Angle
Principle of Least Privilege: Giving dumpcap only the permissions it needs (packet capture) without giving full root access. This is a core security principle.

Real Breach Reference: In 2020, the SolarWinds attack succeeded partly because attackers found systems running with excessive privileges. Limiting permissions like we're doing with dumpcap is exactly the kind of hardening that could have made that attack harder.

text

---

