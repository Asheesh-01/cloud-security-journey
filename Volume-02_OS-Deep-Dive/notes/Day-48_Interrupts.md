# Day 48 — Interrupts

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 1 — OS Architecture
**Subtopic:** 4 — Interrupts

---

## What It Is

Signal to CPU that an event requires immediate attention.
CPU pauses current execution, saves state, runs interrupt handler, resumes.
How the OS responds to real-world events in real time.

## Key Terms

**ISR (Interrupt Service Routine)** — kernel function handling an interrupt.
Runs in Ring 0. Must complete quickly.

**IRQ** — hardware signal line. Traditional: 16 lines (IRQ 0-15).
IRQ 0 = system timer. IRQ 1 = keyboard.
Modern: APIC supports many more lines.

**IDT (Interrupt Descriptor Table)** — kernel table mapping IRQ numbers to handlers.
Set up at boot. Lives in kernel memory.
Rootkit attack: modify IDT to redirect interrupt handling to malicious code.
Defense: IDT mapped as read-only in kernel page tables.

**Hardware interrupt** — from physical devices. Asynchronous. Unpredictable timing.
**Software interrupt** — from software (int instruction). Historically int 0x80 for syscalls.
**Exception** — from CPU itself. Division by zero, page fault, invalid instruction. Synchronous.
**NMI** — Non-Maskable Interrupt. Cannot be disabled. Critical failures, watchdogs.

**Interrupt masking** — CPU temporarily disables interrupts during critical sections.
Security: masking interrupts for too long introduces latency.

**Top half** — immediate handler. Minimum work, schedules bottom half.
**Bottom half** — deferred processing. Runs when interrupts re-enabled.
Design keeps interrupt latency low.

**CONFIG_HZ** — timer interrupt frequency. Common: 100, 250, 1000.
HZ=250 = 250 timer interrupts/second = scheduler runs 250x/second.

## Hardware Interrupt Flow

Device raises IRQ → APIC signals CPU → CPU finishes current instruction
→ saves process state → looks up IDT entry → switches to Ring 0
→ runs interrupt handler (top half) → restores process state
→ resumes interrupted process → bottom half runs when scheduled.

## Security Relevance

**IDT attacks** — rootkits hook system call interrupt → intercept/falsify all syscalls.
Modern mitigation: IDT in read-only kernel page tables.

**Timer and scheduling** — scheduler runs every timer interrupt.
Malware can manipulate CPU usage pattern to reduce detection tool execution.

**APIC attacks** — compromise APIC → reroute or suppress interrupts.
Hardware-level denial of service or monitoring evasion.

**Side-channel timing** — interrupt handler timing leaks information.
Meltdown: read kernel memory via timing of speculative execution during interrupt.

## Commands

```bash
cat /proc/interrupts                    # IRQ stats per CPU core
watch -n 1 -d cat /proc/interrupts     # live interrupt monitoring
cat /proc/softirqs                      # bottom-half processing stats
cat /proc/cpuinfo | grep -E "apicid|flags" | head -10
dmesg | grep -i -E "interrupt|irq|exception|fault" | head -15
dmesg | grep -i "APIC\|irq" | head -10
ls /proc/irq/                           # IRQ directories
cat /proc/irq/0/smp_affinity 2>/dev/null  # timer IRQ CPU affinity
```