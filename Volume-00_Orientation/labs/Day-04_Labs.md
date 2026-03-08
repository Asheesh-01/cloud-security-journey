# Day 04 — Labs

**Topic:** Environment Setup Verification
**Environment:** Kali VM + Ubuntu

---

## Lab 1 — Verify Kali VM

**Commands run in Kali VM:**
```bash
uname -a
cat /etc/os-release
whoami
free -h
df -h
```

**uname -a output:**
Linux kali 6.18.9+kali-amd64 #1 SMP PREEMPT_DYNAMIC
Kali 6.18.9-1kali1 (2026-02-10) x86_64 GNU/Linux

**What it means:**
Running Linux kernel 6.18.9 on 64-bit AMD architecture.
Latest Kali kernel. Confirmed working.

**cat /etc/os-release output:**
PRETTY_NAME="Kali GNU/Linux Rolling"
VERSION="2025.4"

**What it means:**
Kali GNU/Linux Rolling 2025.4. Latest version. Confirmed.

**whoami output:**
krishna

**What it means:**
Not running as root. Correct security practice.
Never do daily work as root even in VM.

**free -h output:**
Mem: 4.3Gi total, 978Mi used, 3.4Gi available
Swap: 3.1Gi total, 0B used

**What it means:**
4.3GB RAM allocated to Kali VM. More than enough for all
volumes including Wazuh in Volume 06 which needs minimum 4GB.
3.4GB available — healthy.

**df -h output:**
/dev/sda1 56G 29G 24G 55% /
VM_FOLDER 95G 63G 32G 67% /media/sf_VM_FOLDER

**What it means:**
Main partition has 24GB free. Comfortable for all tools.
VM_FOLDER is shared folder between Ubuntu and Kali.

**Screenshot:** saved in artifacts/Day-04/

---

## Lab 2 — Update Kali

**Command:**
```bash
sudo apt update && sudo apt upgrade -y
```

**What it does:**
apt update — downloads latest package list from repositories.
apt upgrade -y — installs all available updates.
&& — run second command only if first succeeds.

**Why:**
Outdated packages have known vulnerabilities.
A security engineer running outdated tools contradicts
their own purpose.

**Result:** All packages updated successfully.

**Screenshot:** saved in artifacts/Day-04/

---

## Lab 3 — Verify Git on Ubuntu

**Commands run in Ubuntu:**
```bash
git --version
git config user.name
git config user.email
cd ~/cloud-security-journey
git log --oneline -5 | cat
```

**git --version output:**
git version 2.53.0

**git config user.name output:**
Asheesh Sharma

**git config user.email output:**
asheeshsharma0001@gmail.com

**git log output:**
4c60c84 Day 03 - Career Roles and Goal Setting
2f41d66 Day 02 Revision - Cloud Ecosystem
77f3223 Revision - day 01 - Foundation - Volume 00
03c4b6c content improve and file rename
d6f322d Day 10 - Updated roadmap, progress tracker, README

**What it means:**
Git configured correctly with real identity.
Streak active. Last 5 commits visible and meaningful.
Email matches GitHub account — commits show as green squares.

**Screenshot:** saved in artifacts/Day-04/

---

## Lab 4 — Verify VS Code

**Commands run in Ubuntu:**
```bash
code --version
which code
code .
```

**code --version output:**
1.109.5

**which code output:**
/snap/bin/code

**What it means:**
VS Code installed via Snap. Latest stable version.
code . opens VS Code in current folder — use this daily.

**Screenshot:** saved in artifacts/Day-04/

---

## Lab 5 — Verify Python

**Commands run in Ubuntu:**
```bash
python3 --version
pip3 --version
```

**python3 --version output:**
Python 3.12.3

**pip3 --version output:**
pip 25.2 from /home/krishna/.local/lib/python3.13/

**What it means:**
Python 3.12.3 installed and working.
Two versions present (3.12 and 3.13) — not a problem.
Virtual environments will isolate projects in Volume 04.

**Screenshot:** saved in artifacts/Day-04/

---

## Lab 6 — Install and Verify AWS CLI

**Commands run in Ubuntu:**
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" \
  -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

**aws --version output:**
aws-cli/2.34.4 Python/3.13.11 Linux/6.17.0-14-generic
exe/x86_64.ubuntu.24

**What it means:**
AWS CLI version 2.34.4 installed successfully.
Ready for Volume 07 cloud security labs.

**Screenshot:** saved in artifacts/Day-04/

---

## Lab 7 — Configure AWS CLI

**Command:**
```bash
aws configure
```

**Values entered:**
- Region: ap-south-1 (Mumbai)
- Output format: json
- Access Key: entered from IAM user asheesh01
- Secret Key: entered from IAM user asheesh01

**Verification command:**
```bash
aws sts get-caller-identity
```

**Output:**
{
  "UserId": "AIDA3Z3ILOCUCPMLSCAUD",
  "Account": "811430801576",
  "Arn": "arn:aws:iam::811430801576:user/asheesh01"
}

**What it means:**
AWS CLI connected to account 811430801576.
Authenticated as IAM user asheesh01 — not root.
ARN confirms correct user and account.

**Screenshot:** saved in artifacts/Day-04/

---

## Lab 8 — Create Goals File

**Commands:**
```bash
echo "Phase 1 - SOC Intern - 6 months" > ~/goals.txt
echo "Phase 2 - Cloud Security Engineer - 14 months" >> ~/goals.txt
echo "Phase 3 - Mid Level - 3 years" >> ~/goals.txt
cat ~/goals.txt
```

**What > does:** Creates file and writes first line.
**What >> does:** Appends to existing file without overwriting.
**What cat does:** Reads and prints the file.

**Screenshot:** saved in artifacts/Day-04/