# The Red Herring — Writeup

**Category:** Forensics  
**Difficulty:** Easy  
**Flag:** `CQCTF{metadata_•••••••••••••}`

---

## Challenge Description

> This document appears to be a guide on how mysteries are constructed — suspects, detectives, clues, and red herrings. The pages are filled with definitions, structure, and instructional content, but none of it seems to lead anywhere useful.
>
> Much like a poorly written mystery, the obvious clues may not matter at all.
>
> Look beyond the story being told and focus on **who created it**.

---

## Solution

### Step 1: Read the Document (The Red Herring)

The provided document is full of content about mystery writing, detective stories, and red herrings. Reading through it yields nothing useful — the content itself is a deliberate distraction.

### Step 2: Examine the Metadata

The key hint is: *"focus on who created it."*

Every document (PDF, DOCX, image, etc.) contains **metadata** — hidden information about the file such as author, creation date, software used, and sometimes custom fields.

Use a metadata viewer:

- **Online**: [ExifMeta](https://exifmeta.com/) or [Jeffrey's Exif Viewer](http://exif.regex.info/)
- **Command line**: `exiftool` (Linux/macOS/Windows)

```bash
exiftool document.pdf
```

### Step 3: Find the Flag

In the metadata fields (such as Author, Title, Subject, Keywords, or Comments), you'll find:

```
CQCTF{metadata_•••••••••••••}
```

---

## Key Concepts

- **Metadata**: Data about data. Files store hidden information like author name, creation date, GPS coordinates (for photos), software version, and custom fields. This is a common hiding spot in CTF forensics challenges.
- **Red herring**: A deliberate distraction. The document content is designed to waste your time — the real clue is in the file's properties.
- **EXIF / Document properties**: Standard metadata formats embedded in PDFs, images, Office documents, and more.

---

## Tools Used

- [ExifMeta](https://exifmeta.com/) (online metadata viewer)
- `exiftool` (command line metadata reader)
