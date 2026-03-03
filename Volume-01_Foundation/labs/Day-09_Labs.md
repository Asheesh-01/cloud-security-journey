# Day 09 — Labs
Volume 1 — Computer & Internet Foundations
Topics: Processes

---

## 🔬 Lab 1 — See All Running Processes

```bash
ps aux
```

`ps` → process status, snapshot of current processes
`a` → show processes from all users
`u` → user-oriented format showing username, CPU%, MEM%
`x` → include processes not attached to a terminal

Observation:
Every line = one process with PID, owner, CPU%, MEM%, command name.

---

## 🔬 Lab 2 — See Process Tree

```bash
pstree
```

`pstree` → displays all processes in tree structure showing parent-child relationships

```bash
pstree -p
```

`-p` → show PID number next to each process name

Observation:
systemd at the top = PID 1 = parent of everything.
Every branch = child process created by its parent above it.

---

## 🔬 Lab 3 — Find Your Current Shell PID

```bash
echo $$
```

`echo` → print to terminal
`$$` → special variable holding PID of current shell process

Result on this system: 2944

---

## 🔬 Lab 4 — See Process and Its Parent

```bash
ps -p $$ -o pid,ppid,cmd
```

`ps` → process status
`-p $$` → look at specific PID (current shell)
`-o` → output format, choose which columns to show
`pid` → process ID column
`ppid` → parent process ID column
`cmd` → command name column

Observation:
Shows your shell PID, who created it (PPID), and command name.

---

## 🔬 Lab 5 — Find PID 1

```bash
ps aux | grep systemd | head -5
```

`ps aux` → all processes
`|` → pipe, sends output of left command as input to right command
`grep systemd` → filter lines containing "systemd"
`head -5` → show only first 5 results

Observation:
PID 1 = systemd = root of entire process tree.

---

## 🔬 Lab 6 — Watch Processes Live in Tree View

```bash
htop
```

`htop` → interactive real-time process viewer

Inside htop press `t` → switches to tree view showing parent-child relationships live.
Press `q` to exit.

---

## 📝 Lab Observations

| Lab | Command | Result |
|-----|---------|--------|
| Lab 1 | ps aux | Total processes seen = |
| Lab 2 | pstree | Top of tree = systemd |
| Lab 3 | echo $$ | Shell PID = 2944 |
| Lab 4 | ps -p $$ -o pid,ppid,cmd | PPID of shell = |
| Lab 5 | grep systemd | PID 1 confirmed = |
| Lab 6 | htop tree view | Observed = |