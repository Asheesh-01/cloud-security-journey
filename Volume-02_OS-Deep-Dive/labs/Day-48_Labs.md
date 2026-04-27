# Day 48 — Labs: Interrupts

**Environment:** Kali Linux VM

---

## Lab 1 — View System Interrupts
```bash
cat /proc/interrupts
```
- Kernel-exposed interrupt statistics
- Columns: IRQ number, count per CPU, controller type, device name
- High count = active device. New unexpected IRQ = investigate.

---

## Lab 2 — Watch Interrupts Live
```bash
watch -n 1 -d cat /proc/interrupts
```
- `watch -n 1` — refresh every 1 second
- `-d` — highlight changed values between updates
- Observe NIC IRQ increase with network activity
- Timer IRQ fires constantly at HZ rate
- Press Ctrl+C to stop

---

## Lab 3 — Soft IRQ Statistics
```bash
cat /proc/softirqs
```
- Bottom-half (deferred) interrupt processing statistics
- NET_RX = incoming network packet processing count
- TIMER = timer-based scheduled events
- Shows interrupt processing distribution across CPU cores

---

## Lab 4 — CPU APIC Information
```bash
cat /proc/cpuinfo | grep -E "^processor|apicid|flags" | head -20
```
- `apicid` — APIC ID of each CPU core (APIC routes interrupts)
- `flags` — CPU features including x2apic for extended APIC support

---

## Lab 5 — IRQ Directory Structure
```bash
ls /proc/irq/
cat /proc/irq/0/smp_affinity 2>/dev/null || echo "IRQ 0 not present"
```
- `/proc/irq/` — one subdirectory per active IRQ
- `smp_affinity` — hex bitmask of CPU cores handling this IRQ
- ff = all cores. 01 = only CPU 0.

---

## Lab 6 — Exception Events in Kernel Log
```bash
dmesg | grep -i -E "interrupt|irq|exception|fault" | head -15
```
- Finds interrupt and exception events logged by kernel
- Page faults, IRQ conflicts, exception handling messages
- Unexpected exception messages during operation = possible exploit attempt

---

## Lab 7 — APIC Boot Messages
```bash
dmesg | grep -i "APIC\|irq" | head -10
```
- APIC initialization and IRQ routing messages from boot
- Confirms interrupt controller type (xAPIC vs x2APIC)
- Baseline: what is normal at boot for this hardware