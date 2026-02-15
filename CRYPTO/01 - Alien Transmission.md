# Alien Transmission — Writeup

**Category:** Cryptography  
**Difficulty:** Easy  
**Flag:** `CQCTF{xor_logic_•••••••••••••••••}`

---

## Challenge Description

> We intercepted a strange script and a data file from a hacker group. The script file seems to be encrypted or shifted — it doesn't look like valid Python code right now.
>
> We know the underlying encryption key is **65**. Decode the script logic if you need to, then reverse the transmission!

We are given two files:
- `intercepted_script.py` — a garbled Python script
- `transmission.txt` — an array of integers

---

## Solution

### Step 1: Decode the Intercepted Script

Opening `intercepted_script.py` reveals garbled but structured text:

```
# Em qvbmzkmxbml bpqa xgbpwv akzqxb, jcb bpm ntio dizqijtm eia lmtmbml.
# Kiv gwc zmkwdmz bpm ntio nzwu bzivauqaaqwv.bfb?

SMG = 65
NTIO = "ZMLIKBML"
...
```

This looks like a **Caesar cipher / ROT shift** applied to the code. The variable names give it away — `SMG` is likely `KEY`, `NTIO` is likely `FLAG`.

Testing **ROT-18** (or equivalently ROT-8 in reverse, since it was encoded with ROT+8):

Applying ROT-18 (shift back by 8):
- `S` → `K`, `M` → `E`, `G` → `Y` → **KEY**
- `N` → `F`, `T` → `L`, `I` → `A`, `O` → `G` → **FLAG**

Decoding the full script reveals:

```python
# We intercepted this python script, but the flag variable was deleted.
# Can you recover the flag from transmission.txt?

KEY = 65
FLAG = "REDACTED"

encrypted_chars = []
for char in FLAG:
    encrypted_chars.append(ord(char) ^ KEY)

print(encrypted_chars)
```

### Step 2: Understand the Encryption

The script XORs each character of the flag with key `65` and outputs the integer results. XOR is **reversible** — applying XOR with the same key decrypts the data:

$$\text{char} \oplus 65 = \text{encrypted}$$
$$\text{encrypted} \oplus 65 = \text{char}$$

### Step 3: Read the Transmission

`transmission.txt` contains:

```
[2, 16, 2, 21, 7, 58, 57, 46, 51, 30, 45, 46, 38, 40, 34, 30, 40, 50, 30, 51, 36, 55, 36, 51, 50, 40, 35, 45, 36, 30, 112, 115, 114, 60]
```

### Step 4: Decrypt

XOR each value with key 65:

```python
import ast

KEY = 65

with open("transmission.txt") as f:
    data = ast.literal_eval(f.read())

flag = ''.join(chr(val ^ KEY) for val in data)
print(flag)
```

Result:

```
CQCTF{xor_logic_•••••••••••••••••}
```

### Verification (Manual)

| Encrypted | XOR 65 | ASCII | Char |
|-----------|--------|-------|------|
| 2         | 67     | 67    | C    |
| 16        | 81     | 81    | Q    |
| 2         | 67     | 67    | C    |
| 21        | 84     | 84    | T    |
| 7         | 70     | 70    | F    |
| 58        | 123    | 123   | {    |
| ...       | ...    | ...   | ...  |
| 60        | 125    | 125   | }    |

---

## Key Concepts

- **XOR cipher**: A symmetric cipher where the same operation (XOR) with the same key both encrypts and decrypts. It's the foundation of many modern ciphers.
- **ROT / Caesar cipher**: A letter substitution cipher that shifts characters by a fixed number of positions. Here ROT-8 was used to obfuscate the script.
- **Reversibility**: XOR is its own inverse: `A ^ K ^ K = A`. This makes it trivial to decrypt if the key is known.

---

## Tools Used

- Python
- Text editor (to analyze the ROT-encoded script)
