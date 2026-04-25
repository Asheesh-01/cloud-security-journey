# Day 46 — Labs: User Space vs Kernel Space

**Environment:** Kali Linux VM

---

## Lab 1 — Process Memory Map
```bash
cat /proc/$$/maps | head -20
```
- `/proc/$$` — /proc directory for current shell ($$=current PID)
- `maps` — virtual memory layout of this process
- Columns: address range, permissions (rwxp), offset, device, inode, name
- All addresses shown are user space — kernel space is invisible to processes

---

## Lab 2 — ASLR Status
```bash
cat /proc/sys/kernel/randomize_va_space
```
- 0 = disabled — memory at predictable addresses, exploits easier
- 1 = partial randomization
- 2 = full randomization — default, should always be 2
- Randomizes stack, libraries, heap on every execution

---

## Lab 3 — Address Column Only
```bash
cat /proc/$$/maps | awk '{print $1}' | head -10
```
- `awk '{print $1}'` — print only the address range column
- User space addresses begin with 0000 on 64-bit Linux
- Kernel space (ffff...) never appears — unmapped from process view post-KPTI

---

## Lab 4 — Kernel Address Restriction
```bash
cat /proc/sys/kernel/kptr_restrict
```
- Controls visibility of kernel addresses in /proc and other interfaces
- 0 = visible to all (dangerous — aids exploit development)
- 1 = hidden from non-root users (minimum hardening)
- 2 = hidden from all including root (maximum hardening)

---

## Lab 5 — dmesg Restriction
```bash
cat /proc/sys/kernel/dmesg_restrict
```
- 0 = any user reads kernel messages (kernel messages may contain addresses)
- 1 = root only — correct setting for hardened systems

---

## Lab 6 — Memory Statistics
```bash
cat /proc/meminfo | head -20
```
- Kernel-exposed memory statistics
- MemTotal, MemFree, Cached, SwapTotal — physical memory breakdown
- Kernel itself uses RAM for structures not directly shown here

---

## Lab 7 — Page Size
```bash
getconf PAGE_SIZE
```
- Basic unit of MMU memory management
- Typically 4096 bytes (4KB) on x86_64
- All memory permissions are applied at page granularity

---

## Lab 8 — Process Isolation Demo
```bash
echo "Current shell PID: $$"
cat /proc/$$/maps | wc -l
bash -c 'echo "Child shell PID: $$"; cat /proc/$$/maps | wc -l'
```
- Two shell processes have separate address spaces
- Same virtual addresses in each process map to different physical memory
- This is MMU-enforced process isolation managed by the kernel