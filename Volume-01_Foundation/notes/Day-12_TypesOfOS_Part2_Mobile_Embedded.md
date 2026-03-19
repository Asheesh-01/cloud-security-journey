# Day 12 — Topic 4: Types of Operating Systems (Part 2)
# Mobile OS and Embedded OS

**Date:** Day 12 continued
**Volume:** 01 — Computer and Internet Foundations
**GitHub:** github.com/Asheesh-01

---

## Type 3 — Mobile OS

**What it is:** An OS designed for smartphones and tablets. Every design decision comes from four constraints — battery, touchscreen, cellular network, and pocket size.

**Examples:** Android, iOS, HarmonyOS (Huawei)

**Key terms:**
- **Battery constraint** — OS aggressively manages which apps use CPU, network, screen to save power
- **Sandbox** — isolated container where each app runs — cannot access other app's data
- **Permission model** — user must explicitly grant camera, microphone, location, contacts access
- **Suspended state** — app frozen in memory, zero CPU time, minimal battery drain
- **APK** — Android Package file, installation format for Android apps

**Core design choices:**

Aggressive power management — apps not in use are suspended (frozen in memory, no CPU). If RAM full, OS kills suspended apps silently. This is why apps sometimes restart fresh instead of resuming.

Sandboxed app model — each app has private isolated directory and its own user ID. Instagram cannot read WhatsApp messages. Banking app cannot be read by a game.

Permission system — camera, microphone, location, contacts each require explicit user approval. Enforced at kernel level.

Touch input driver — instead of hid_generic keyboard driver, touchscreen driver reads finger position, pressure, gesture — tap, swipe, pinch, rotate.

**Real example — you open banking app on Android:**

Step 1 — tap icon. Touchscreen driver detects touch. Sends tap event to launcher.
Step 2 — Android Activity Manager checks if banking app is suspended in memory.
Step 3 — if suspended, resume it. If killed, fork() and exec() create new process.
Step 4 — app needs network. Android checks INTERNET permission declared at install. Granted.
Step 5 — app needs fingerprint. Checks BIOMETRIC permission granted by user. Fingerprint driver called. Match confirmed.
Step 6 — balance displayed. You switch to Instagram.
Step 7 — Android suspends banking app. Freezes in memory. Instagram gets CPU.
Step 8 — RAM full after 15 apps. Banking app killed silently. Next open restarts fresh. Normal behavior.

**Android vs iOS security:**

Android — open, sideloading allowed (install apps outside Play Store). Flexibility = risk. Malicious APK files bypass Google scanning.

iOS — closed, App Store only. Apple reviews every app. Sideloading restricted. Harder to compromise but not impossible.

**Real breach:** Pegasus spyware by NSO Group. Zero-click exploit — no user interaction needed. Exploited iMessage and WhatsApp image processing vulnerabilities. Accessed messages, calls, camera, microphone, location. Bypassed entire sandbox by exploiting kernel vulnerabilities.

---

## Type 4 — Embedded OS

**What it is:** An OS built into a specific hardware device to do one job and only that job. Not general purpose. Runs permanently in a chip. Does its one purpose forever.

**Examples:** Firmware in home WiFi router, CCTV camera OS, smart TV software, car engine control unit, washing machine controller.

**Key terms:**
- **Embedded** — built into and permanently part of the hardware. Not removable.
- **Firmware** — software stored permanently in flash memory chip. Does not disappear when power cut.
- **Flash memory** — storage chip keeping data without power. Like a tiny SSD soldered onto circuit board.
- **General purpose** — able to run any software. Laptop = general purpose. Router = not.
- **Resource constrained** — very limited CPU, RAM, storage. Home router: 128MB RAM, 600MHz CPU. Your phone: 8GB RAM, 3GHz CPU.

**Core design choices:**

Stripped to absolute minimum — no GUI, no user accounts, no app store. Just code for the one job.

Boots in seconds or milliseconds — almost nothing to load.

Updates are rare or never — firmware written at manufacture. Many devices never updated. Ever.

**Real example — you connect to home WiFi router:**

Step 1 — power on. CPU immediately reads firmware from flash chip. No loading screen. Firmware executes directly.
Step 2 — embedded OS initializes WAN port (to ISP) and WiFi radio (to your devices).
Step 3 — loads routing table — rules saying which packets go where.
Step 4 — your phone connects. Sends DHCP request. Router assigns IP 192.168.1.5.
Step 5 — you open YouTube. Phone sends packet to YouTube IP. Arrives at router WiFi interface.
Step 6 — router checks routing table in microseconds. Decision: forward out WAN port to ISP.
Step 7 — YouTube response arrives on WAN port. Router sends back to your phone at 192.168.1.5.
Step 8 — this happens thousands of times per second for every device in your home. Nothing else. Only routing.
Step 9 — router runs for months. No updates. Same firmware from day of manufacture.

**Security angle — critical:**

Never updated — router firmware from 2019 has every vulnerability discovered since 2019 still present. Still exploitable right now.

Massive attack surface — router, smart TV, IP cameras, smart speaker, smart doorbell, washing machine with WiFi. Each is a computer with old firmware and no updates. Each is a potential entry point.

Default credentials — most ship with admin/admin or admin/password. Most users never change this. Attacker tries default credentials on every device. Succeeds on most.

**Real breach:** Mirai botnet 2016 — scanned entire internet for embedded devices with default credentials. Infected 600,000 cameras, routers, DVRs. Used all simultaneously to attack DNS provider Dyn. Took down Twitter, Netflix, Reddit, Spotify, CNN for hours. Attack came from cameras and routers in people's homes. Owners had no idea.

---

## Key Terms — Full List Topic 4 So Far

- **GUI** — Graphical User Interface
- **Daemon** — background process running continuously
- **Headless** — server without monitor/keyboard, managed via SSH
- **Sandbox** — isolated container where app runs without accessing others
- **Permission model** — explicit user approval for hardware access
- **Suspended state** — app frozen in memory, no CPU time
- **APK** — Android Package file
- **Sideloading** — installing apps outside official app store
- **Firmware** — software stored permanently in hardware chip
- **Flash memory** — storage chip retaining data without power
- **Resource constrained** — limited CPU, RAM, storage
- **DHCP** — Dynamic Host Configuration Protocol, assigns IP addresses — Volume 3
- **Routing table** — list of rules directing network packets to correct destination
- **IoT** — Internet of Things, internet-connected embedded devices — Volume 7
- **Shodan** — internet scanner finding exposed devices — Volume 3
- **Mirai** — malware targeting embedded devices with default credentials — Volume 5
- **Pegasus** — spyware using zero-click exploits on mobile OS — Volume 5
- **Zero-click exploit** — attack requiring no user interaction