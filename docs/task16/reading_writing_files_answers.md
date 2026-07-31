# Reading & Writing Files — Answers with Reasoning

Check the **reasoning**, not just the letter. Two ideas cover almost everything: `fwrite` writes **raw bytes** (in little-endian memory order) while `fprintf` writes **readable characters**; and you must read a file the same way it was written.

---

# Part A — Multiple Choice

**A1. B) the raw bytes of `n`, in memory order** — `fwrite` copies the bytes exactly as they sit at `&n`. No conversion to digits.

**A2. C) the two characters `'6'` and `'5'`** — `fprintf("%i", 65)` turns the number into its printed form, the digits `6` and `5` (bytes `0x36 0x35`). It does *not* write the raw int, and `65` is not `'A'` here.

**A3. A) behaves the same — the `b` does nothing here** — on Linux/WSL the `"b"` flag is a no-op; only Windows uses it (to translate line endings). The real binary-vs-text difference is `fwrite` vs `fprintf`.

**A4. B) little-endian** — the lowest byte is stored at the lowest address, so it is written first.

**A5. B) `3210`** — bytes are `0x30 0x31 0x32 0x33` = `'0' '1' '2' '3'`, written lowest-first → `0x33 0x32 0x31 0x30` = `3210`.

**A6. C) `fread`** — it reads raw bytes back into memory. `fscanf`/`fgets` are for text; `fprintf` writes.

**A7. B) `sum.c`** — source code is a text file. The compiled `sum`, `clang`, and `lldb` are binary.

**A8. B) raw binary bytes, not readable characters** — `cat` tries to show bytes as text; machine code bytes are not text, so you get gibberish (and some bytes make the terminal beep).

**A9. B) 0x30** — the character `'0'` has code 48 = `0x30`. The byte `0x00` is the number zero / null, a different thing.

**A10. B) `fscanf`** — text was written with `fprintf`, so parse it back with `fscanf`. Read the way you wrote.

**A11. B) a binary file** — an image is raw bytes; there is no sensible way to store a photo as readable characters.

**A12. B) `file`** — it inspects the contents and reports e.g. "ASCII text" or "ELF ... executable".

**A13. C) mostly gibberish / nothing readable** — `5` is `0x05 0x00 0x00 0x00`: one low control byte and three zero bytes, none of them printable. Small friendly number, unreadable bytes.

**A14. B) the numbers were hand-picked so their raw bytes are printable ASCII codes** — normally `fwrite` output is unreadable (see A13). The earlier examples were chosen so each byte happened to be a letter's code.

---

# Part B — Fill in the Blanks

**B1.** the **raw bytes**; **readable** characters.

**B2.** **lowest** byte first, because ints are **little**-endian.

**B3.** `DCBA` — bytes `0x44 0x43 0x42 0x41` = `D C B A`, written lowest-first (`0x44`='D' is the lowest byte).

**B4.** read raw bytes with **`fread`**; read text with **`fscanf`** (or `fgets`).

**B5.** `sum.c` is a **text** file; `sum` is a **binary** file.

**B6.** `0x41`.

**B7.** makes **no** difference (on Linux).

**B8.** **3** bytes — the characters `'1'`, `'0'`, `'0'`.

**B9.** `xxd` (also acceptable: `hexdump`/`od`).

**B10.** a compact **binary** database; a `.vcf` export is **text**.

---

# Part C — Scenario Questions

**C1.** `cat` prints **`code`**. The int `0x65646F63` sits in memory lowest byte first: `0x63 0x6F 0x64 0x65` = `'c' 'o' 'd' 'e'`. `fwrite` copies that order to the file, so it reads `code` — the value looked reversed in hex only because we *write* hex high-byte-first.

**C2.**
```
7
42
100
```
`fprintf("%i\n", …)` writes each number as its digit characters followed by a newline — readable, in the order written, no reversing.

**C3.**
- **Text files:** `main.c`, `notes.txt`, `script.py` — readable characters you can edit.
- **Binary files:** `photo.jpg`, `a.out`, `song.mp3`, `lldb` — raw bytes (image, executable, audio, tool).

**C4.** `fwrite` wrote the int's **raw bytes**, and `5` is `0x05 0x00 0x00 0x00`: a control byte (`0x05`) plus three null bytes. None are printable characters, so `cat` shows nothing readable (and `0x05`-type bytes can beep the terminal). The number being small does not help — what matters is whether its *bytes* are printable ASCII codes.

**C5.** They broke **"read the way you wrote."** Text written with `fprintf` is digit characters (e.g. `65` is the bytes `'6' '5'`), but `fread` copies raw bytes straight into the int — so it reads the character codes as if they were an int's bytes, giving nonsense. They should read with **`fscanf`** (matching the `fprintf`).

**C6.**
- (a) Instagram video — **binary** (media is raw bytes).
- (b) `config.txt` settings — **text** (a human edits it).
- (c) Free Fire saved game data — **binary** (compact and fast to load).
- (d) exported `chat.txt` — **text** (meant to be read).

**C7.** `cat` prints **`testOK!!`**. Take each int lowest byte first:
- `0x74736574` → `0x74 0x65 0x73 0x74` = `t e s t`
- `0x21214b4f` → `0x4f 0x4b 0x21 0x21` = `O K ! !`

Across the two ints: `test` then `OK!!` = `testOK!!`.

---

**Pattern to notice across your mistakes:** nearly every error is one of three — thinking `fwrite` writes readable digits (it writes raw bytes), forgetting little-endian puts the lowest byte first in the file, or reading a file a different way than it was written. Keep `fwrite`↔`fread` (raw bytes) and `fprintf`↔`fscanf` (text) paired, and the answers fall out.
