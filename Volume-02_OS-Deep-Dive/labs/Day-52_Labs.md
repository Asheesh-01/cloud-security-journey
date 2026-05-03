# Day 52 — Labs: File Attributes and ADS

**Environment:** Windows 10 VM — Command Prompt as Administrator

---

## Lab 1 — View File Attributes
```cmd
attrib C:\Windows\System32\notepad.exe
attrib C:\Windows\
```
- `attrib` — shows attribute flags for file or directory
- Flags: A=archive, H=hidden, R=read-only, S=system, I=not indexed

---

## Lab 2 — Find Hidden Files
```cmd
dir /A:H C:\Windows\
dir /A:SH C:\
```
- `/A:H` — list only hidden files
- `/A:SH` — list files with both System and Hidden attributes
- H+S combination hides from basic `/A:H` — requires both flags

---

## Lab 3 — Create File and Add ADS
```cmd
echo "This is normal visible content" > C:\Users\Public\test.txt
echo "This is hidden in ADS" > C:\Users\Public\test.txt:hidden_stream
```
- First echo creates file with default stream content
- Second echo writes to named ADS `hidden_stream`
- Colon syntax: `filename:streamname`

---

## Lab 4 — Verify ADS Invisible to Normal Dir
```cmd
dir C:\Users\Public\test.txt
```
- File size reflects default stream only
- ADS not shown, not counted in size
- Completely invisible without /R flag

---

## Lab 5 — Read ADS Content
```cmd
more < C:\Users\Public\test.txt:hidden_stream
```
- `more <` — reads and displays named stream content
- Colon syntax accesses the specific stream
- Also works: `notepad C:\Users\Public\test.txt:hidden_stream`

---

## Lab 6 — Detect ADS with dir /R
```cmd
dir /R C:\Users\Public\test.txt
```
- `/R` — shows all data streams including ADS
- Output: `:$DATA` (default), `:hidden_stream:$DATA` (ADS detected)
- This is the key detection command for ADS

---

## Lab 7 — Zone.Identifier Demo
```cmd
echo [ZoneTransfer] > C:\Users\Public\test.txt:Zone.Identifier
echo ZoneId=3 >> C:\Users\Public\test.txt:Zone.Identifier
dir /R C:\Users\Public\test.txt
more < C:\Users\Public\test.txt:Zone.Identifier
```
- ZoneId=3 = Internet zone
- Windows adds this automatically to downloaded files
- SmartScreen reads this to determine file origin

---

## Lab 8 — Hide and Find File with Attributes
```cmd
echo "Hidden file simulation" > C:\Users\Public\innocent.txt
attrib +H +S C:\Users\Public\innocent.txt
dir C:\Users\Public\innocent.txt
dir /A:SH C:\Users\Public\innocent.txt
attrib -H -S C:\Users\Public\innocent.txt
del C:\Users\Public\innocent.txt
del C:\Users\Public\test.txt
```
- `attrib +H +S` — sets hidden and system attributes
- First `dir` — file not found (hidden)
- `dir /A:SH` — file found with correct flags
- `attrib -H -S` — removes attributes for cleanup