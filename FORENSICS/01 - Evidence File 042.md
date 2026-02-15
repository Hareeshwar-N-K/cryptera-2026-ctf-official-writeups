# Evidence File #042 — Writeup

**Category:** Forensics  
**Difficulty:** Easy  
**Flag:** `CQCTF{magic_bytes_•••••••••}`

---

## Challenge Description

> "I tried to take a screenshot of the suspect's dashboard, but the file got corrupted during the upload. My photo viewer says 'Format Not Recognized,' but I know it's an image. Can you recover the image?"

We are given a file called `evidence.png` that no image viewer can open.

---

## Solution

### Step 1: Identify the Problem

Try opening the file — any image viewer will refuse it, saying the format is not recognized. This hints that the file's **magic bytes** (file signature) are corrupted.

Open the file in a hex editor (HxD, hexedit, or even `xxd` on Linux):

```
00000000: 00 00 00 00 00 00 00 00  ...
```

The first 8 bytes are zeroed out (or random garbage). A valid **PNG** file should start with:

```
89 50 4E 47 0D 0A 1A 0A
```

### Step 2: Restore the Magic Bytes

Using a hex editor:

1. Open `evidence.png`
2. Select the first 8 bytes
3. Replace them with: `89 50 4E 47 0D 0A 1A 0A`
4. Save the file

Alternatively, using Python:

```python
with open("evidence.png", "r+b") as f:
    f.seek(0)
    f.write(bytes([0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A]))
```

### Step 3: Open the Image

Open the repaired file in any image viewer. The image displays the flag:

```
CQCTF{magic_bytes_•••••••••}
```

---

## Key Concepts

- **Magic bytes / File signatures**: The first few bytes of a file that identify its format. For PNG, this is always `89 50 4E 47 0D 0A 1A 0A`.
- **Hex editors**: Tools like HxD (Windows), hexedit (Linux), or online hex editors allow you to directly view and modify raw file data.
- Corrupted headers are a common forensics scenario — always check the file signature first when a file won't open.

---

## Tools Used

- Hex editor (HxD / hexedit / xxd)
- Python (optional)
