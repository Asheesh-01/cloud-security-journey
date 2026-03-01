# Day 03 — Input, Output & Storage Devices
Volume 1 — Computer Fundamentals

## 🎯 Objective
Understand how computers receive data, process it, store it, and return results.
This builds the foundation for networking, cloud infrastructure, and security logging.

---

## 📘 Concepts Covered

### 1️⃣ Input Devices

Definition:
Devices that send data or signals into a computer system.

Examples:
- Keyboard
- Mouse
- Microphone
- Network packets (API requests, HTTP traffic)

Cloud mapping:
User login request → Input to server  
API call to AWS → Input to cloud system  

Security relevance:
Malicious input = attack surface  
(SQL injection, command injection, malicious payloads)

---

### 2️⃣ Output Devices

Definition:
Devices that display or send processed results.

Examples:
- Monitor
- Printer
- Speaker
- Server HTTP response

Cloud mapping:
Server response → Output  
Security alert → Output from SIEM  

Security relevance:
Incorrect output can leak sensitive data.
(Example: verbose error messages revealing system details)

---

### 3️⃣ Storage Devices

Definition:
Hardware that stores data permanently or temporarily.

Types:

• Primary Storage (RAM)
  - Temporary
  - Volatile
  - Fast

• Secondary Storage (SSD / HDD)
  - Permanent
  - Non-volatile
  - Slower than RAM

Cloud mapping:
- RAM → EC2 memory
- SSD → EBS volume
- Object storage → S3 bucket

Security relevance:
If storage is unencrypted → data breach risk.
If public storage bucket → exposure risk.

---

## 🔄 Data Flow Summary

Input → Processing (CPU + RAM) → Output → Storage

This is the fundamental computing cycle.

Every cloud service follows this same principle at scale.

---

## ☁ Cloud Security Connection

• Logs are stored in storage systems.
• API requests are inputs.
• Security alerts are outputs.
• Cloud misconfigurations often expose storage.

Understanding input/output helps understand:
- Attack vectors
- Data exfiltration
- Logging systems

---

## 🧠 Key Takeaways

• Every system has inputs and outputs.
• Storage security is critical in cloud.
• Input validation prevents attacks.
• Output control prevents data leakage.

---

## 🔜 Next Topic

How Computers Process Data  
(Binary • Bits • Bytes • Number Systems)