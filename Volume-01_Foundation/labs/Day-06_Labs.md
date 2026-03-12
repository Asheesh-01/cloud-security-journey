# Day 06 — Labs: How Computers Process Data

---

## Binary Number Commands

### Command 1
```bash
python3 -c "print(bin(205))"
```
Output: `0b11001101`

What Python shows: the binary representation of 205 with a 0b prefix.
What 0b means: Python's way of telling you this is a binary number, not decimal.
What 11001101 means: the eight bit pattern that represents 205. Each 1 and 0 corresponds to a power of 2. Position 7=128, position 6=64, position 3=8, position 2=4, position 0=1. Add them: 128+64+8+4+1=205.
What RAM stores: this exact pattern of 8 bits — 11001101 — as electrical charges in memory cells. 1 = charge present, 0 = no charge.

### Command 2
```bash
python3 -c "print(int('11001101', 2))"
```
Output: `205`

What Python shows: the decimal number 205.
What the 2 means: tells Python the input string '11001101' is in base 2 (binary). Without this Python would not know what number system the string uses.
What this confirms: converting binary 11001101 back to decimal gives 205. The conversion works both ways.
What RAM stores: the number 205 in whatever register or memory location Python allocated for this calculation.

### Command 3
```bash
python3 -c "print(bin(255))"
```
Output: `0b11111111`

What Python shows: 255 in binary.
What 11111111 means: all 8 bits are 1. This is the maximum value one byte can hold. Every bit position is switched on. 128+64+32+16+8+4+2+1=255.
What RAM stores: 11111111 — all 8 bits set to 1 in one byte of memory.
Security relevance: 255 appears everywhere. Subnet mask 255.255.255.0 means the first three bytes are all 1s (network portion) and last byte is all 0s (host portion).

---

## Bits and Bytes Commands

### Command 4
```bash
python3 -c "print(8 * 1)"
```
Output: `8`

What Python shows: 8.
What it means: 8 bits in 1 byte. Simple confirmation of the fundamental unit relationship.
What RAM stores: the integer 8 in binary — 00001000.

### Command 5
```bash
python3 -c "print(1024 * 1024)"
```
Output: `1048576`

What Python shows: 1,048,576.
What it means: the number of bytes in one megabyte. 1024 bytes = 1 kilobyte. 1024 kilobytes = 1 megabyte. So 1024 x 1024 = 1,048,576 bytes in one megabyte. This is 2^20.
Why not 1,000,000: computers count in powers of 2 not powers of 10. 2^10=1024 is close enough to 1000 that engineers called it kilo. Every storage unit follows this pattern.
What RAM stores: 1048576 in binary — 100000000000000000000 (1 followed by 20 zeros).

### Command 6
```bash
python3 -c "print(2**8)"
```
Output: `256`

What Python shows: 256.
What it means: 2 to the power 8. One byte has 8 bit positions. Each position can be 0 or 1. Total combinations = 2x2x2x2x2x2x2x2 = 256. So one byte can represent 256 different values — from 0 (00000000) to 255 (11111111).
What RAM stores: 256 = 100000000 in binary — 9 bits, so stored in at least 2 bytes.

### Command 7
```bash
python3 -c "print(2**10)"
```
Output: `1024`

What Python shows: 1024.
What it means: 2 to the power 10. This is why 1 kilobyte = 1024 bytes. The computer world chose powers of 2 as unit boundaries. 2^10=1024 is the closest power of 2 to 1000.
What RAM stores: 1024 = 10000000000 in binary.

### Command 8
```bash
python3 -c "print(2**32)"
```
Output: `4294967296`

What Python shows: 4,294,967,296.
What it means: 2 to the power 32. An IPv4 address is 32 bits long. So 2^32 is the total number of possible IPv4 addresses — approximately 4.3 billion. This is why IPv4 ran out of addresses as the internet grew past 4.3 billion connected devices. IPv6 uses 128 bits giving 2^128 addresses — practically unlimited.
What RAM stores: this large number requires 5 bytes to store — it exceeds the 4-byte (32-bit) maximum of 4294967295.

### Command 9
```bash
python3 -c "print(len('hello'.encode()))"
```
Output: `5`

What Python shows: 5.
What .encode() does: converts the Python string 'hello' into a bytes object. Each character gets converted to its ASCII number. h=104, e=101, l=108, l=108, o=111.
What len() does: counts how many bytes are in the result.
What 5 means: the word hello takes 5 bytes of storage — one byte per character. In ASCII each English character is exactly 1 byte.
What RAM stores: five consecutive bytes — 01101000 01100101 01101100 01101100 01101111.

---

## Number Systems Commands

### Command 10
```bash
python3 -c "print(hex(205))"
```
Output: `0xcd`

What Python shows: 0xcd.
What 0x means: prefix that tells you this is a hexadecimal number. Just like 0b means binary.
What cd means: C in hex = 12 in decimal, D in hex = 13 in decimal. Together CD = (12 x 16) + 13 = 192 + 13 = 205. Same number as before, different representation.
What RAM stores: 11001101 — the binary is identical regardless of how we display it. Hex is just a human-friendly way to read the same binary data.

### Command 11
```bash
python3 -c "print(hex(255))"
```
Output: `0xff`

What Python shows: 0xff.
What FF means: F in hex = 15. FF = (15 x 16) + 15 = 240 + 15 = 255. Two hex digits representing one full byte with all bits set to 1.
Security relevance: 0xFF is the maximum value of one byte. You will see FF constantly in security work — in file headers, in shellcode padding, in memory analysis.

### Command 12
```bash
python3 -c "print(hex(256))"
```
Output: `0x100`

What Python shows: 0x100.
What it means: 256 in hex is 100. The 1 means one group of 256 (16^2), the two 0s mean zero remaining. This is important — 256 cannot fit in one byte. One byte maximum is 255 (0xFF). The moment you reach 256 you need a second byte. 0x100 has three hex digits = more than one byte. This is the boundary where buffer overflows exploit the overflow from one memory region into the next.

### Command 13
```bash
python3 -c "print(int('CD', 16))"
```
Output: `205`

What Python shows: 205.
What this command does: converts the hex string 'CD' back to decimal. The 16 tells Python that 'CD' is a base 16 (hexadecimal) number. Direction: hex input, decimal output.
What it confirms: CD in hex equals 205 in decimal. Same value seen from the previous commands — 11001101 binary = CD hex = 205 decimal. Three different names for the same pattern of bits.

### Command 14
```bash
python3 -c "print(int('FF', 16))"
```
Output: `255`

What Python shows: 255.
What it means: FF in hex = 255 in decimal = 11111111 in binary = maximum value of one byte. All three number systems confirming the same value.

### Command 15
```bash
python3 -c "print(hex(16))"
```
Output: `0x10`

What Python shows: 0x10.
What it tells you about hexadecimal counting: in decimal you run out of single digits at 9 — the next number needs two digits (10). In hexadecimal you have 16 single digits — 0 through F. So you do not need two digits until you reach sixteen. 0x10 means one complete group of sixteen (the 1) plus zero remaining units (the 0). This is why hex(10) = 0xa — ten is still a single digit in hex.

---

## Character Encoding Commands

### Command 16
```bash
python3 -c "print(ord('A'))"
```
Output: `65`

What Python shows: 65.
What ord() does: returns the ASCII number for a given character. ord stands for ordinal.
What 65 means: in the ASCII standard the letter A is assigned the number 65. Every computer agrees on this mapping.
What RAM stores: 01000001 — the binary representation of 65.

### Command 17
```bash
python3 -c "print(ord('a'))"
```
Output: `97`

What Python shows: 97.
What it means: lowercase a is ASCII number 97. The gap between A (65) and a (97) is exactly 32. This gap is the same for every letter pair. B=66 and b=98. Z=90 and z=122. Knowing this gap, simple case conversion in code is just adding or subtracting 32.
What RAM stores: 01100001.

### Command 18
```bash
python3 -c "print(ord('0'))"
```
Output: `48`

What Python shows: 48.
What it means: the character zero — the digit you type on a keyboard — has ASCII value 48. This is completely different from the number zero. The number 0 in binary is 00000000. The character '0' in ASCII is 00110000 (48 in binary). This distinction matters in security — a program reading user input gets characters, not numbers. It must convert explicitly.

### Command 19
```bash
python3 -c "print(chr(65))"
```
Output: `A`

What Python shows: the letter A.
What chr() does: reverse of ord(). Takes an ASCII number and returns the character it represents.
What this confirms: 65 maps to A. chr and ord are inverse functions.

### Command 20
```bash
python3 -c "import base64; print(base64.b64encode(b'hello'))"
```
Output: `b'aGVsbG8='`

What Python shows: b'aGVsbG8='
What b means at the start: this is a bytes object, not a regular string. Python shows bytes with a b prefix.
What aGVsbG8= means: the word hello encoded in Base64. Each group of 3 bytes becomes 4 Base64 characters. hello = 5 bytes, which does not divide evenly into groups of 3, so = padding is added at the end.
What the = means: padding character. Base64 encoding always produces output in multiples of 4 characters. The = fills the gap. One = means one byte of padding. Two == means two bytes of padding.
Security relevance: when you see a string ending in = in a log file, your first thought is Base64. Decode it immediately.

### Command 21
```bash
python3 -c "import base64; print(base64.b64decode('aGVsbG8='))"
```
Output: `b'hello'`

What Python shows: b'hello' — the original word.
What b64decode does: reverses the encoding. Takes Base64 string, produces original bytes.
What this means for security: anyone can decode Base64 in one command. It is not encryption. It is not protection. It is just a format change. Attackers use it to hide commands from simple text scanners but any security tool decodes it instantly.

### Command 22
```bash
python3 -c "import base64; print(base64.b64decode('cGFzc3dvcmQ='))"
```
Output: `b'password'`

What Python shows: b'password'
What this was: a Base64 encoded string found in a log file during an investigation.
What the finding means: the word password was transmitted or logged in a form that looks like random data. This is a credential exposure — someone or something logged a plaintext password. In a real SOC investigation this is escalated immediately. It could mean a misconfigured application is logging credentials, or an attacker planted it.

### Command 23
```bash
python3 -c "print('A'.encode())"
```
Output: `b'A'`

What Python shows: b'A' — Python shows you the character A with a b prefix because it is a bytes object.
What is actually stored in RAM: the number 65, which in binary is 01000001. Python displays it as A because it knows 65 maps back to the character A and that is more readable for you. The actual memory contains 01000001 — eight bits, one byte.
This is the key insight: Python (and every tool you use) always shows you a human-friendly representation. The actual storage is always binary. Always. No exceptions.

### Command 24
```bash
python3 -c "print('é'.encode('utf-8'))"
```
Output: `b'\xc3\xa9'`

What Python shows: two escaped bytes — \xc3 and \xa9.
What \x means: Python's way of showing a raw byte value in hexadecimal inside a bytes object. \xc3 means the byte with hex value C3. \xa9 means the byte with hex value A9.
What two bytes means: the French character é cannot fit in one byte of ASCII. UTF-8 uses two bytes to encode it — C3 A9 in hex.
What RAM stores: 11000011 10101001 — two consecutive bytes.

### Command 25
```bash
python3 -c "print('é'.encode('utf-8').hex())"
```
Output: `c3a9`

What Python shows: c3a9
What .hex() does: takes a bytes object and returns a string of hex characters. Strips away the \x notation and shows raw hex values side by side.
What c3a9 means: the two bytes that represent é in UTF-8. C3 is the first byte (195 in decimal, 11000011 in binary). A9 is the second byte (169 in decimal, 10101001 in binary).

### Command 26
```bash
python3 -c "print('अ'.encode('utf-8'))"
```
Output: `b'\xe0\xa4\x85'`

What Python shows: three escaped bytes — \xe0, \xa4, \x85.
What three bytes means: the Hindi Devanagari character अ requires three bytes in UTF-8. This is because Devanagari script has hundreds of characters — far more than can fit in one or two bytes. UTF-8 uses 3 bytes for characters in this Unicode range.
What RAM stores: 11100000 10100100 10000101 — three consecutive bytes.

### Command 27
```bash
python3 -c "print('अ'.encode('utf-8').hex())"
```
Output: `e0a485`

What Python shows: e0a485 — three bytes shown as six hex characters.
What it confirms: UTF-8 character width is variable. ASCII characters = 1 byte. Western accented characters = 2 bytes. Hindi and other complex scripts = 3 bytes. Chinese characters = 3 bytes. Some rare Unicode characters = 4 bytes.

---

## Instruction Cycle Commands

### Command 28 — Fetch Decode Execute Simulation
```bash
python3 -c "
code = [10, 20, 30]
pc = 0
while pc < len(code):
    instruction = code[pc]
    print(f'Fetch: got instruction {instruction} from address {pc}')
    print(f'Decode: this means add {instruction} to total')
    print(f'Execute: result stored')
    pc += 1
    print()
"
```
Output:
```
Fetch: got instruction 10 from address 0
Decode: this means add 10 to total
Execute: result stored

Fetch: got instruction 20 from address 1
Decode: this means add 20 to total
Execute: result stored

Fetch: got instruction 30 from address 2
Decode: this means add 30 to total
Execute: result stored
```

What code = [10, 20, 30] means: this list represents RAM containing three instructions at addresses 0, 1, and 2.
What pc = 0 means: pc is the Program Counter — the register that holds the address of the next instruction. It starts at 0, the first address.
What the while loop does: represents the CPU running — it keeps fetching instructions as long as there are more to process.
What instruction = code[pc] does: this is the Fetch step. The CPU reads whatever is at the current Program Counter address.
What pc += 1 does: after fetching, the Program Counter automatically advances to the next address. This happens after every fetch, every time, without exception.
What the three print lines show: Fetch=reading from RAM, Decode=understanding what the instruction means, Execute=carrying out the operation.

### Command 29 — Disassembler on add function
```bash
python3 -c "
import dis
def add(a, b):
    return a + b
dis.dis(add)
"
```
Output:
```
3           RESUME                   0
4           LOAD_FAST_LOAD_FAST      1 (a, b)
            BINARY_OP                0 (+)
            RETURN_VALUE
```

RESUME 0: marks function entry point. Every function starts with this. The 3 is the line number in the source code.
LOAD_FAST_LOAD_FAST 1 (a, b): loads both variables a and b onto the stack in one combined instruction. LOAD_FAST means get a local variable from fast local storage. Line 4 is return a + b.
BINARY_OP 0 (+): the ALU performs addition on the two values loaded. The 0 is the internal code for addition. (+) is Python showing you it means addition.
RETURN_VALUE: takes the result and returns it to whoever called the function. Places result in RAX register. Updates RIP to point back to the caller.

### Command 30 — Disassembler on demonstrate_rip function
```bash
python3 -c "
def demonstrate_rip():
    x = 10
    y = 20
    result = x + y
    return result
import dis
dis.dis(demonstrate_rip)
"
```
Output:
```
2           RESUME                   0
3           LOAD_CONST               1 (10)
            STORE_FAST               0 (x)
4           LOAD_CONST               2 (20)
            STORE_FAST               1 (y)
5           LOAD_FAST_LOAD_FAST      1 (x, y)
            BINARY_OP                0 (+)
            STORE_FAST               2 (result)
6           LOAD_FAST                2 (result)
            RETURN_VALUE
```

LOAD_CONST 1 (10): fetches the constant value 10 from the constants table. This is the Fetch step — reading a value that will be used.
STORE_FAST 0 (x): stores 10 into the local variable slot for x. In hardware this moves the value into a register.
LOAD_CONST 2 (20): fetches 20 for y.
STORE_FAST 1 (y): stores 20 into y's slot.
LOAD_FAST_LOAD_FAST 1 (x, y): loads both x and y onto the stack — both values now in working registers ready for the ALU.
BINARY_OP 0 (+): ALU adds x and y. This is the Execute step.
STORE_FAST 2 (result): stores the addition result into the result variable slot.
LOAD_FAST 2 (result): loads result onto the stack one final time to prepare for return.
RETURN_VALUE: places result into RAX (the return value register) and updates RIP to return to the caller.

---

## CPU Registers Commands

### Command 31
```bash
python3 -c "
import ctypes
print(ctypes.sizeof(ctypes.c_long))
"
```
Output: `8`

What ctypes is: a Python library that lets you interact with C data types and low-level memory directly.
What ctypes.sizeof() does: returns the size in bytes of a given C data type.
What ctypes.c_long is: represents a C long integer — the same size as a general purpose register on this machine.
What 8 means: a C long integer is 8 bytes on this machine. 8 bytes x 8 bits = 64 bits. This confirms the machine is 64-bit. All general purpose registers (RAX, RBX, RCX, RDX, RSI, RDI, RSP, RBP, RIP) are each 64 bits = 8 bytes wide.

### Command 32
```bash
python3 -c "
import struct
value = 205
packed = struct.pack('Q', value)
print(f'Value: {value}')
print(f'As bytes in register: {packed.hex()}')
print(f'Size: {len(packed)} bytes = {len(packed)*8} bits')
"
```
Output:
```
Value: 205
As bytes in register: cd00000000000000
Size: 8 bytes = 64 bits
```

What struct is: a Python module for packing values into raw bytes exactly as they are stored in CPU registers or memory.
What struct.pack('Q', value) does: packs the integer 205 into bytes using format Q. Q means unsigned 64-bit integer — exactly the size of a general purpose register like RAX on a 64-bit CPU.
What cd00000000000000 means: the 8 bytes that represent 205 in a 64-bit register. cd is hex for 205 — you already know this. The remaining 000000000000 are the upper 7 bytes, all empty because 205 is small enough to fit in 1 byte. The CPU still uses the full 8 bytes for the register slot.
Why cd comes first: this is little-endian byte order. Your CPU stores the least significant byte first. Network protocols use big-endian (most significant byte first). This difference requires byte-order conversion in network programming and packet analysis.
What packed.hex() does: converts the bytes object to a plain hex string for easy reading.

---

## Cache Memory Commands

### Command 33 — Cache timing demonstration
Output:
```
Small list access time: 0.2938 ms
Large list access time: 0.3367 ms
```

What the two lists represent:
- small_list of 100 numbers: small enough to fit entirely in L1 or L2 cache after first few accesses. Each subsequent access is a cache hit — CPU finds the data in cache without going to RAM.
- large_list of 10 million numbers: too large for cache. The last element at position 9,999,999 is never going to be in cache. Every access forces the CPU to go all the way to RAM — a cache miss.

What time.perf_counter() does: returns current time with the highest precision your system provides. Used to measure very short intervals.
Why 10000 repetitions: one access is too fast and inconsistent to measure accurately. Running 10000 times gives a reliable average.
What the timing difference shows: cache hit is faster than cache miss. In Python the difference is small because Python adds its own overhead. In C or assembly the difference would be 10-50 times larger.
Why Spectre uses this: the timing difference between cache hit (4 cycles) and cache miss (200+ cycles) is measurable. Spectre exploits this by causing the CPU to speculatively load secret data into cache, then measuring access times to determine what was loaded — reading protected memory without ever accessing it directly.

### Command 34 — Real cache sizes from kernel
```bash
cat /sys/devices/system/cpu/cpu0/cache/index0/size
cat /sys/devices/system/cpu/cpu0/cache/index1/size
cat /sys/devices/system/cpu/cpu0/cache/index2/size
cat /sys/devices/system/cpu/cpu0/cache/index3/size
```
Output:
```
48K
64K
2048K
18432K
```

What cat does: reads a file and prints its contents. Short for concatenate.
What /sys/devices/system/cpu/ is: the sysfs filesystem. Not a real directory on disk. The Linux kernel generates it in memory to expose hardware information as readable files. You are reading directly from the kernel.
What cpu0 means: the first CPU core. This machine has 18 cores (cpu0 through cpu17).
What each index means:
- index0 = L1 data cache = 48K = 48 kilobytes. Stores data values. Private to cpu0.
- index1 = L1 instruction cache = 64K = 64 kilobytes. Stores upcoming CPU instructions separately. Private to cpu0.
- index2 = L2 cache = 2048K = 2 megabytes. Backup when L1 misses. Private to cpu0.
- index3 = L3 cache = 18432K = 18 megabytes. Shared across all 18 cores of the Intel Core Ultra 5 125H.

These are real hardware specifications of this exact machine read directly from the Linux kernel.