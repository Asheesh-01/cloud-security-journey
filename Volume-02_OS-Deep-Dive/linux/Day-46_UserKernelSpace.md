# Day 46 — User Space vs Kernel Space

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 1 — OS Architecture
**Subtopic:** 2 — User Space vs Kernel Space

---

## What It Is

Fundamental memory division: kernel space (Ring 0) and user space (Ring 3).
The most critical security boundary in any OS.
Every other security control depends on this boundary holding.

## Key Terms

**Virtual memory** — each process has its own private address space.
MMU maps virtual → physical. Process A cannot read Process B's memory.

**MMU** — Memory Management Unit. Hardware on CPU.
Translates virtual to physical addresses. Enforces page permissions.
The kernel programs the MMU. User processes cannot.

**Page** — 4KB unit of memory management. Has r/w/x permission flags.
**Privilege level** — CPU execution mode. Ring 0 = kernel. Ring 3 = user.
**Context switch** — kernel switches CPU between processes. Thousands/second.
**Kernel panic** — unrecoverable kernel error. System halts. (Linux = panic, Windows = BSOD)
**ASLR** — randomizes memory layout. Makes exploits harder.
  /proc/sys/kernel/randomize_va_space: 0=off, 1=partial, 2=full (default).

## What User Space Can and Cannot Do

Can: read/write own memory, execute own code, make system calls.
Cannot: access other process memory, touch hardware directly,
  execute privileged CPU instructions, modify kernel memory,
  modify MMU page tables, load kernel modules.

## How Kernel Exploits Break the Boundary

Null pointer dereference: kernel reads address 0 → attacker controls content.
Use-after-free: freed kernel memory reallocated and controlled by attacker.
Buffer overflow: overwrite kernel function pointers → redirect to attacker code.
Type confusion: treat data structure as wrong type → arbitrary kernel write.

Result: Ring 3 code executes in Ring 0 context.
From Ring 0: elevate any process to root, disable security features,
load rootkit, read all system memory.

## Hardening Checks

kptr_restrict: 0=addresses visible (bad), 1=hidden from non-root, 2=hidden all.
dmesg_restrict: 0=any user reads kernel messages (bad), 1=root only.
randomize_va_space: must be 2 (full ASLR).

## Real Breach — Meltdown CVE-2018-3640

Intel CPU speculative execution bug.
User-space process read kernel memory via CPU cache timing side channel.
No kernel exploit needed — hardware boundary failed.
Fix: KPTI patch — unmap kernel from user-space page tables.
Performance cost: 5-30%. Price of software-fixing a hardware failure.
Lesson: hardware + software + kernel all have attack surface.

## Commands

```bash
cat /proc/$$/maps | head -20          # current process memory map
cat /proc/sys/kernel/randomize_va_space  # ASLR status (should be 2)
cat /proc/sys/kernel/kptr_restrict    # kernel address exposure (should be 1 or 2)
cat /proc/sys/kernel/dmesg_restrict   # dmesg access control (should be 1)
cat /proc/meminfo | head -20          # memory statistics
getconf PAGE_SIZE                     # page size in bytes (usually 4096)
echo "Current shell PID: $$"
cat /proc/$$/maps | wc -l
```