# Day 76 — Labs: Text Processing + Piping + Redirection

**Environment:** Kali Linux VM inside VirtualBox inside Ubuntu 24.04
**Using:** Real systemd journal as data source

---

## Lab 1 — Extract Unique Values with awk

```bash
sudo journalctl | grep "session" | awk '{print $1}' | sort | uniq | wc -l
```
- Extract first field from each line
- Count unique values
- Shows number of unique entities in logs

---

## Lab 2 — Multi-Field Extraction

```bash
sudo journalctl | grep "Failed" | awk '{print $1, $3}' | head -10
```
- Print field 1 and field 3 together
- Shows multiple values per line

---

## Lab 3 — Conditional awk

```bash
sudo journalctl | awk '$3 == "Failed" {print $1}'
```
- If field 3 equals "Failed"
- Print only field 1 (IP if present)
- Conditional filtering before extraction

---

## Lab 4 — Complete Ranking Pipeline

```bash
sudo journalctl | grep "Failed" | awk '{print $1}' | sort | uniq -c | sort -rn | head -5
```
- Extract → Sort → Count → Rank → Show top 5
- Core SOC analytics pipeline

---

## Lab 5 — sed — Delete Lines

```bash
sudo journalctl | head -20 | sed '10d'
```
- `sed '10d'` deletes line 10
- Shows first 20 lines except line 10

---

## Lab 6 — sed — Search and Replace

```bash
echo "IP 192.168.1.100 connected" | sed 's/192.168.1.100/XXX.XXX.XXX.XXX/'
```
- `s/old/new/` substitutes first occurrence
- Add `/g` for all occurrences

---

## Lab 7 — Create Report with Redirection

```bash
echo "=== SECURITY REPORT ===" > /tmp/my_report.txt
echo "Generated: $(date)" >> /tmp/my_report.txt
echo "Total system events:" >> /tmp/my_report.txt
sudo journalctl | wc -l >> /tmp/my_report.txt
cat /tmp/my_report.txt
```
- `>` creates/overwrites file
- `>>` appends to file
- `$(date)` inserts command output
- `cat` displays final report

---

## Lab 8 — Real-Time Log Monitoring

```bash
sudo journalctl -f | grep "Failed"
```
- `-f` follow mode
- Real-time filtered log stream
- Ctrl+C to stop

---

## Lab 9 — Complex Pipeline

```bash
sudo journalctl | awk '{print $1}' | sort | uniq -c | sort -rn | head -10 > /tmp/top_sources.txt
cat /tmp/top_sources.txt
```
- Entire pipeline output saved to file
- Verify output by displaying file

---

## Lab 10 — Combine sort Flags

```bash
echo -e "apple 5\nbanana 2\napple 8\ncherry 1" | sort -k2 -rn
```
- `-k2` sort by field 2
- `-rn` numeric reverse
- Sorts by second column instead of first

---

## Lab 11 — Count Occurrences

```bash
echo -e "error\nerror\nwarning\nerror\nwarning" | sort | uniq -c
```
- Output shows: count then value
- error 3, warning 2
- Why sort is essential before uniq