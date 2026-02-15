# Sticky Encoding — Writeup

**Category:** Cryptography  
**Difficulty:** Very Easy (50 points)  
**Flag:** `CQCTF{base64_is_n•••••••••••••}`

---

## Challenge Description

> I found this weird text on a sticky note. Can you decode it?
>
> `Q1FDVEZ7YmFzZTY0X2lzX25vdF9lbmNyeXB0aW9ufQ==`

---

## Solution

### Step 1: Recognize the Encoding

The string ends with `==` and contains only alphanumeric characters plus `+` and `/` — this is clearly **Base64** encoded data.

### Step 2: Decode

Using any Base64 decoder:

**Python:**
```python
import base64
encoded = "Q1FDVEZ7YmFzZTY0X2lzX25vdF9lbmNyeXB0aW9ufQ=="
print(base64.b64decode(encoded).decode())
```

**CyberChef:**  
Input → From Base64 → Output

**Command line:**
```bash
echo "Q1FDVEZ7YmFzZTY0X2lzX25vdF9lbmNyeXB0aW9ufQ==" | base64 -d
```

### Step 3: Read the Flag

```
CQCTF{base64_is_n•••••••••••••}
```

---

## Key Concepts

- **Base64 is encoding, not encryption**: Base64 transforms binary data into ASCII text for safe transmission. It provides **zero security** — anyone can decode it without a key.
- **Recognizing Base64**: Look for alphanumeric strings that end with `=` or `==` padding. The charset is `A-Z`, `a-z`, `0-9`, `+`, `/`.

---

## Tools Used

- Python / CyberChef / command line `base64` tool
