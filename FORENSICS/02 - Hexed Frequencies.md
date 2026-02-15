# Hexed Frequencies — Writeup

**Category:** Forensics  
**Difficulty:** Easy–Medium  
**Flag:** `CQCTF{beep_b••••••••}`

---

## Challenge Description

> "We intercepted a rhythmic transmission. It sounds like standard comms, but the decoded message is just a string of nonsense numbers and letters. Can you make sense of the chaos?"

We are given a WAV file called `hexed_frequencies.wav`.

---

## Solution

### Step 1: Analyze the Audio

Playing the WAV file reveals **Morse code** — a series of beeps (dots and dashes).

### Step 2: Decode the Morse Code

Upload the audio file to a Morse Code audio decoder such as:
- [Morse Code World](https://morsecode.world/international/decoder/audio-decoder-adaptive.html)
- Any Morse-to-text tool

The decoded output is:

```
43514354467B6265••••••••••••••••••••••••••7D
```

### Step 3: Recognize the Encoding

At first glance this looks like gibberish. But notice that every character is in the range `0-9` or `A-F` — this is **hexadecimal**.

### Step 4: Convert Hex to ASCII

Using CyberChef, Python, or any hex-to-text converter:

```python
hex_string = "43514354467B6265...7D"  # Partially masked
flag = bytes.fromhex(hex_string).decode()
print(flag)
```

Breaking it down:
| Hex | ASCII |
|-----|-------|
| 43  | C     |
| 51  | Q     |
| 43  | C     |
| 54  | T     |
| 46  | F     |
| 7B  | {     |
| ... | ...   |
| 7D  | }     |

Result:

```
CQCTF{beep_b••••••••}
```

---

## Key Concepts

- **Morse code**: A classic encoding method using dots (short tones) and dashes (long tones) to represent characters.
- **Hexadecimal encoding**: A base-16 representation of data. Each pair of hex digits represents one ASCII character.
- **Layered encoding**: This challenge chains two encodings — Morse code wrapping hexadecimal — requiring solvers to recognize and decode each layer in order.

---

## Tools Used

- Morse code audio decoder (e.g., Morse Code World)
- CyberChef or Python for hex-to-ASCII conversion
