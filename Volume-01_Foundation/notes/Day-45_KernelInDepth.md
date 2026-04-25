# Day 45 — Kernel in Depth: Monolithic vs Microkernel

**Volume:** 02 — Operating Systems Deep Dive
**Topic:** 1 — OS Architecture
**Subtopic:** 1 — Kernel in Depth

---

## What It Is

The kernel is the core of the OS. Direct control over all hardware.
Every privileged operation — file access, memory, networking, processes —
goes through the kernel.

## Key Terms

**Kernel space** — memory region for kernel only. Unrestricted hardware access.
Bug here = system crash. Malware here = invisible to user-space tools.

**User space** — memory region for applications. Restricted access.
Crash here = only that app dies. All user programs including AV run here.

**Ring 0** — CPU hardware privilege level. Kernel mode. Unrestricted.
**Ring 3** — CPU hardware privilege level. User mode. Restricted.
Hardware enforces this boundary — not software.

## Kernel Architectures

**Monolithic** — entire OS in one program in kernel space.
Linux and Windows use this. Fast — direct memory communication.
Risk: any driver bug or compromise = Ring 0 access.
Large attack surface.

**Microkernel** — only IPC, scheduling, memory mapping in kernel space.
Everything else (drivers, file systems) in user space.
More secure — driver crash = only that process dies.
Slower — message passing between components.

**Hybrid** — compromise. Some services in kernel space for speed.
Windows NT and macOS XNU are hybrid.

**LKMs (Loadable Kernel Modules)** — extend Linux kernel at runtime.
lsmod lists currently loaded modules.
Attack vector: rootkits load malicious modules after privilege escalation.
Defense: kernel module signing, Secure Boot.

## CPU Security Features

**SMEP** — Supervisor Mode Execution Prevention.
Prevents kernel from executing code in user-space memory.
Defeats specific exploit techniques.

**SMAP** — Supervisor Mode Access Prevention.
Prevents kernel from accessing user-space memory unintentionally.

## Security Relevance

**Kernel exploits** — Ring 3 code executing as Ring 0.
Dirty Cow CVE-2016-5195: any user could write to read-only files
including /etc/passwd → trivial root escalation on unpatched kernels.

**Rootkits** — malware in kernel space.
Intercept system calls → return falsified results to user-space tools.
AV and EDR see a clean system. Malware operates invisibly.
Detection: kernel-level tools or known-good baseline comparison.

**Stuxnet 2010** — four zero-days, kernel-mode drivers, Ring 0.
Falsified centrifuge sensor readings to operators.
Physically destroyed hardware while appearing normal.
Only possible from Ring 0.

## Commands

```bash
uname -r                                    # kernel version
uname -a                                    # full system info
lsmod                                       # loaded kernel modules (Ring 0 code)
modinfo bluetooth 2>/dev/null              # module details
dmesg | head -30                            # kernel ring buffer messages
dmesg | grep -i -E "error|fail|denied|blocked" | head -20
dmesg | grep -i "usb\|pci\|driver" | head -15
cat /proc/version                           # kernel build info
cat /proc/cpuinfo | grep flags | head -1 | tr ' ' '\n' | grep -E "vmx|svm|smep|smap"
```