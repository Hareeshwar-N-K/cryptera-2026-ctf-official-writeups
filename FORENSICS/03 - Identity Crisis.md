# Identity Crisis — Writeup

**Category:** Forensics  
**Difficulty:** Easy  
**Flag:** `CQCTF{file_extensi0ns_•••••••••••••}`

---

## Challenge Description

> This file claims to be something it is not. Appearances can be deceptive — especially in digital evidence.

We are given a file called `report.txt`.

---

## Solution

### Step 1: Examine the File

Opening `report.txt` in a text editor might show garbled content or nothing readable, because the file isn't actually a text file — it just has a `.txt` extension.

### Step 2: Check the True File Type

Use the `file` command (Linux/macOS) or a hex editor to inspect the actual file signature:

```bash
file report.txt
```

This will reveal the true type — the file is actually a **ZIP archive** (or PNG image), not a text file. The magic bytes in the header will tell you the real format:

- **ZIP**: Starts with `50 4B 03 04` (`PK..`)
- **PNG**: Starts with `89 50 4E 47`

### Step 3: Fix the Extension and Open

Rename the file to match its actual type:

```bash
# If it's a ZIP:
mv report.txt report.zip
unzip report.zip

# If it's a PNG:
mv report.txt report.png
```

### Step 4: Retrieve the Flag

After extracting/opening the correctly-renamed file, you'll find:

```
CQCTF{file_extensi0ns_•••••••••••••}
```

In this case, unzipping the archive reveals a `flag.txt` containing the flag.

---

## Key Concepts

- **File extensions are cosmetic**: The operating system uses extensions as hints, but the actual file format is determined by the file's **magic bytes** (header signature).
- **`file` command**: A standard Unix utility that identifies file types by examining their content, not their extension.
- **Mismatch analysis**: A fundamental forensics technique — always verify that a file's extension matches its actual content.

---

## Tools Used

- `file` command (Linux/macOS) or hex editor
- Archive extraction tool (unzip / 7-Zip)
