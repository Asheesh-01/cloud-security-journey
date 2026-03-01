# Day 06 — Labs
Volume 1 — Computer & Internet Foundations
Topics: Instruction Cycle, Registers, Cache Memory

---

## 🔬 Lab 1 — Observe the Instruction Cycle in Action

When you run any program, the instruction cycle is happening. Let's observe it.

```bash
# Run a simple program and time its execution
time ls -la

# Run a Python one-liner and observe execution
time python3 -c "print('hello')"
```

**What to observe:**
- `real` time = total time including all CPU fetch-decode-execute cycles
- Even a simple `print` triggers thousands of instruction cycles internally

```bash
# Watch CPU activity in real time (instruction cycles happening live)
htop
```

Press `q` to exit htop.

**What to observe in htop:**
- CPU% column = how many instruction cycles that process is consuming
- Each % point = millions of fetch-decode-execute cycles per second

---

## 🔬 Lab 2 — Observe Registers Indirectly via Running Processes

You cannot see registers directly without a debugger, but you can observe what processes are holding in memory (which feeds into registers).

```bash
# See all running processes and their memory usage
ps aux --sort=-%mem | head -10
```

```bash
# See detailed info about a specific process (use any PID from above)
cat /proc/1/status | head -20
```

**What to observe:**
- VmRSS = actual RAM this process is using right now
- This RAM feeds into CPU registers during execution

```bash
# See your CPU architecture (determines register size)
lscpu | grep -E "Architecture|CPU|Thread|Core"
```

**What to observe:**
- Architecture: x86_64 = 64-bit registers (most modern systems)
- This means general purpose registers like RAX, RBX hold 64 bits = 8 bytes each

---

## 🔬 Lab 3 — Observe Cache Memory on Your System

```bash
# See your CPU cache sizes
lscpu | grep -i cache
```

**What to observe:**
- L1d = L1 data cache size
- L1i = L1 instruction cache size
- L2 = L2 cache size
- L3 = L3 cache size

```bash
# See cache info in detail
cat /sys/devices/system/cpu/cpu0/cache/index0/size
cat /sys/devices/system/cpu/cpu0/cache/index1/size
cat /sys/devices/system/cpu/cpu0/cache/index2/size
cat /sys/devices/system/cpu/cpu0/cache/index3/size
```

Each index = one cache level. Read the size values.

```bash
# Alternative — full hardware info including cache
sudo dmidecode --type cache 2>/dev/null | head -40
```

---

## 🔬 Lab 4 — Observe Memory Hierarchy Live

```bash
# See full memory picture at once
free -h
```

**What to observe:**
- Total RAM available
- Used vs Free RAM
- This is the RAM that feeds data into CPU cache → registers

```bash
# See memory usage updating in real time
watch -n 1 free -h
```

Press `Ctrl+C` to stop.

```bash
# See which processes are using the most RAM (feeding into instruction cycle)
ps aux --sort=-%mem | head -5
```

---

## 🔬 Lab 5 — Instruction Cycle Security Demo (Buffer Overflow Concept)

```bash
# Create a simple Python script to simulate memory address concept
cat > memory_demo.py << 'EOF'
import ctypes

# Create a variable in memory
data = "hello"

# Get its memory address (this is where CPU fetches it from)
address = id(data)

print(f"Value: {data}")
print(f"Memory address (decimal): {address}")
print(f"Memory address (hex): {hex(address)}")
print()
print("This hex address is what the Program Counter (register) points to")
print("CPU fetches instruction FROM this address during the instruction cycle")
EOF

python3 memory_demo.py
```

**What to observe:**
- The hex address = real location in your RAM
- In a buffer overflow — an attacker overwrites this address value in the register
- That redirects CPU to fetch malicious instructions instead

---

## 📝 Lab Observations (fill after running)

| Lab | Command | What I Observed |
|-----|---------|-----------------|
| Lab 1 | htop | |
| Lab 2 | lscpu | |
| Lab 3 | lscpu \| grep cache | L1= , L2= , L3= |
| Lab 4 | free -h | Total RAM= |
| Lab 5 | memory_demo.py | Address hex= |