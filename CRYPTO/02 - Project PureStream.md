# Project PureStream — Writeup

**Category:** Cryptography  
**Difficulty:** Medium  
**Flag:** `CQCTF{steg64_••••••}`

---

## Challenge Description

> **[AUTOMATED SECURITY REPORT]** Action: Threat Mitigation Successful.  
> Details: Our active defense filter intercepted a data transmission containing non-standard logical artifacts. To ensure system stability and user safety, the file was automatically normalized.
>
> *User Ticket #9942:* "I keep telling IT that the 'cleaned' file is useless! It's just a generic placeholder message now. The raw transmission had some weird glitches in the text encoding that looked like static, but that's where the actual intel was supposed to be. Can you bypass the safety filter and get me the original 'glitchy' version?"

We are given `sanitized_transmission.txt` — a file containing one Base64 string per line.

---

## Solution

### Step 1: Examine the Data

The file contains lines like:

```
VE==
aD==
ZV==
IB==
cU==
...
```

Decoding each line from Base64 gives individual characters that spell out a decoy message: *"The quick brown fox jumps over the lazy dog to hide the secret flag inside this text."*

The decoy is a red herring — the flag is hidden **within** the Base64 encoding itself.

### Step 2: Understand Base64 Steganography

When Base64 encodes a **single byte** (1 character), the output is 4 characters: 2 meaningful characters + `==` padding.

However, a single byte only needs 8 bits of information, while 2 Base64 characters encode 12 bits (6 bits each). This leaves **4 bits of slack** in the second character.

The encoder exploits this by injecting flag bits into the unused lower bits of the second Base64 character using bitwise OR:

```python
# Original Base64 char's value has 0000 in the lower 4 bits
# Secret bits are OR'd in: original_val | secret_4bits
new_val = original_val | secret_val
```

### Step 3: Extract the Hidden Bits

For each line ending with `==` (single-byte encoding):

1. Take the **2nd character** of the Base64 string
2. Look up its index in the Base64 alphabet
3. Extract the **last 4 bits** — these are the hidden flag bits

```python
import base64

data = open("sanitized_transmission.txt").read().strip().split('\n')
b64_chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"

binary_string = ""

for line in data:
    line = line.strip()
    if not line:
        continue

    if line.endswith("=="):
        # 1-byte encoding: 4 bits of slack in 2nd character
        char = line[1]
        val = b64_chars.index(char)
        bits = format(val, '06b')[-4:]  # Last 4 bits
        binary_string += bits
    elif line.endswith("="):
        # 2-byte encoding: 2 bits of slack in 3rd character
        char = line[2]
        val = b64_chars.index(char)
        bits = format(val, '06b')[-2:]  # Last 2 bits
        binary_string += bits
```

### Step 4: Convert Binary to ASCII

Reassemble the extracted bits into 8-bit bytes:

```python
flag = ""
for i in range(0, len(binary_string), 8):
    byte = binary_string[i:i+8]
    if len(byte) == 8:
        flag += chr(int(byte, 2))

print(f"Recovered Flag: {flag}")
```

Output:

```
CQCTF{steg64_••••••}
```

---

## Full Solve Script

```python
import base64

data = open("sanitized_transmission.txt").read().strip().split('\n')
b64_chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"

binary_string = ""

for line in data:
    line = line.strip()
    if not line:
        continue

    if line.endswith("=="):
        char = line[1]
        val = b64_chars.index(char)
        bits = format(val, '06b')[-4:]
        binary_string += bits
    elif line.endswith("="):
        char = line[2]
        val = b64_chars.index(char)
        bits = format(val, '06b')[-2:]
        binary_string += bits

flag = ""
for i in range(0, len(binary_string), 8):
    byte = binary_string[i:i+8]
    if len(byte) == 8:
        flag += chr(int(byte, 2))

print(f"Recovered Flag: {flag}")
```

---

## Worked Example

Take the first few lines:

| Line  | 2nd Char | Index | Binary (6-bit) | Last 4 bits |
|-------|----------|-------|----------------|-------------|
| `VE==` | `E`     | 4     | `000100`       | `0100`      |
| `aD==` | `D`     | 3     | `000011`       | `0011`      |
| `ZV==` | `V`     | 21    | `010101`       | `0101`      |

First 12 bits: `0100 0011 0101...`  
First byte: `01000011` = 67 = `C` ✓

---

## Key Concepts

- **Base64 steganography**: Base64 padding creates unused bit positions that can carry hidden data without affecting the decoded plaintext.
- **Padding slack**: `==` padding (1-byte input) gives 4 slack bits; `=` padding (2-byte input) gives 2 slack bits.
- **Bitwise operations**: The flag bits are injected via OR into the lower bits of Base64 characters.

---

## Tools Used

- Python
- Understanding of Base64 encoding internals
