# Reading & Writing Files — Question Bank

Answer on paper, using the worksheet's ideas: `fwrite` writes a value's **raw bytes** (binary), `fprintf` writes it as **readable characters** (text); an `int`'s bytes go to the file in **little-endian** order (lowest byte first); and you read a file the same way you wrote it.

Useful codes: `'0'`=0x30, `'A'`=0x41, `'a'`=0x61. Answers are **not** in this file.

---

# Part A — Multiple Choice

**A1.** `fwrite(&n, sizeof(n), 1, f)` writes:

- A) the number `n` as readable digits
- B) the raw bytes of `n`, in memory order
- C) only the first byte of `n`
- D) nothing until you call `fprintf`

**A2.** `fprintf(f, "%i", 65)` writes to the file:

- A) one byte, value 65
- B) the four raw bytes of the int 65
- C) the two characters `'6'` and `'5'`
- D) the character `'A'`

**A3.** On Linux/WSL, `fopen("f","wb")` compared with `fopen("f","w")`:

- A) behaves the same — the `b` does nothing here
- B) writes in reverse
- C) refuses to open the file
- D) doubles the file size

**A4.** An `int` written with `fwrite` reaches the file lowest byte first because ints are stored:

- A) big-endian
- B) little-endian
- C) as text
- D) in reverse alphabetical order

**A5.** `int n = 0x30313233;` written with `fwrite`, then `cat` shows:

- A) `0123`
- B) `3210`
- C) `0x30313233`
- D) `3` followed by gibberish

**A6.** To read raw bytes back into an int, you use:

- A) `fscanf`
- B) `fgets`
- C) `fread`
- D) `fprintf`

**A7.** Which is a **text** file?

- A) `sum` (the compiled program)
- B) `sum.c`
- C) `clang`
- D) `lldb`

**A8.** `cat sum` (the executable) prints gibberish because the file is:

- A) empty
- B) raw binary bytes, not readable characters
- C) encrypted
- D) too large

**A9.** The character `'0'` stored in a file is the byte:

- A) 0x00
- B) 0x30
- C) 0x0A
- D) 0

**A10.** You wrote numbers with `fprintf` (text). To read them back correctly, use:

- A) `fread`
- B) `fscanf`
- C) `xxd`
- D) `fwrite`

**A11.** A photo sent on WhatsApp is stored as:

- A) a text file
- B) a binary file
- C) a `.c` source file
- D) readable digits

**A12.** Which command guesses whether a file is text or binary?

- A) `cat`
- B) `file`
- C) `clang`
- D) `fopen`

**A13.** `int n = 5;` written with `fwrite`, then `cat` shows:

- A) `5`
- B) `0x05`
- C) mostly gibberish / nothing readable
- D) `50000`

**A14.** The earlier examples (`3210`, `dcba`, `ABCD…`) showed readable letters from `fwrite` only because:

- A) `fwrite` always writes text
- B) the numbers were hand-picked so their raw bytes are printable ASCII codes
- C) `cat` converts binary to text
- D) little-endian makes bytes readable

---

# Part B — Fill in the Blanks

**B1.** `fwrite` writes the ____ ____ of a value; `fprintf` writes it as ____ characters.

**B2.** An `int` reaches the file ____ byte first, because ints are stored ____-endian.

**B3.** `int n = 0x41424344;` written with `fwrite`, then `cat` prints `________`.

**B4.** You read raw bytes back with `____`, and readable text back with `____`.

**B5.** `sum.c` is a ____ file; the compiled `sum` is a ____ file.

**B6.** The character `'A'` is stored as the byte `0x____`.

**B7.** On Linux, the `"b"` in `fopen("f","wb")` makes ____ difference.

**B8.** `fprintf(f, "%i", 100)` writes ____ bytes to the file (the digits of 100).

**B9.** The shell command `________` shows a file's raw bytes in hex.

**B10.** A phone's Contacts are kept in a compact ____ database, but a `.vcf` export is ____.

---

# Part C — Scenario Questions

**C1.** `int n = 0x65646F63;` is written with `fwrite`. Write what `cat` prints, and explain the byte order. (`0x63`=`'c'`, `0x64`=`'d'`, `0x65`=`'e'`, `0x6F`=`'o'`.)

**C2.** A program does `fprintf(file, "%i\n", 7); fprintf(file, "%i\n", 42); fprintf(file, "%i\n", 100);`. Write exactly what `cat` shows.

**C3.** Sort these into **text files** and **binary files**: `main.c`, `photo.jpg`, `a.out`, `notes.txt`, `song.mp3`, `script.py`, `lldb`.

**C4.** `int n = 5;` is written with `fwrite` and you `cat` the file — you see almost nothing readable, maybe a beep. Explain why, even though `5` is a small friendly number.

**C5.** A classmate writes four numbers with `fprintf` (text), then tries to read them back with `fread` into an int array. It comes out wrong. What rule did they break, and what should they use instead?

**C6.** For each, choose text or binary and give a one-line reason: (a) an Instagram video, (b) a program's `config.txt` settings, (c) Free Fire's saved game data, (d) a chat exported as `chat.txt`.

**C7.** `int arr[] = {0x74736574, 0x21214b4f};` is written with `fwrite`. Work out the 8 bytes in order and write what `cat` prints. (`0x74`=`'t'`, `0x73`=`'s'`, `0x65`=`'e'`, `0x4b`=`'K'`, `0x4f`=`'O'`, `0x21`=`'!'`.)

---

When you finish, check the answer key — check your *reasoning* (raw bytes vs text, and little-endian order), not only the final strings.
