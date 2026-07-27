# Reading & Writing Files — Raw Bytes vs Readable Text

**Goal.** A file is just a sequence of bytes on disk. Today you will write numbers into files two different ways — as **raw bytes** (`fwrite`) and as **readable text** (`fprintf`) — and `cat` each one to see how differently they turn out. The little-endian idea from Tasks 11 and 15 will explain a surprise in the very first program.

**You need:** your Linux VM (WSL) with `clang` and `lldb`, a notebook, and a pencil.

> **The golden rule of files**
> A file holds bytes, nothing more. `fwrite` puts the value's **raw bytes** straight onto disk; `fprintf` writes the value as **readable characters**. Same number, two completely different files.

*(One note before we start: on Linux/WSL, `fopen(..., "wb")` and `fopen(..., "w")` behave the same — the `"b"` letter does nothing here. What actually makes a file "binary" or "text" is **how you write** it: `fwrite` for raw bytes, `fprintf` for readable text. We still write `"wb"` when we mean raw bytes, to show our intent.)*

---

## Iteration 1 — Write an int's raw bytes, then `cat` it

**a. What we set up**

```c
/* write_num.c */
#include <stdio.h>

int main()
{
    int number = 0x30313233;
    FILE *file;

    file = fopen("num.txt", "wb");
    fwrite(&number, sizeof(number), 1, file);
    fclose(file);

    return 0;
}
```

`fwrite(&number, sizeof(number), 1, file)` means: take the 4 raw bytes *at the address of* `number`, and write them to the file exactly as they sit in memory.

**b. Task**

Predict what `cat num.txt` will print. Then:

```
clang -g write_num.c -o write_num
./write_num
cat num.txt
```

**c. Observation (what you should find)**

```
3210
```

Surprised? You wrote `0x30313233`, but the file reads `3210` — *backwards*. Two things are happening, and you already know both: `0x30` is the character `'0'`, `0x31` is `'1'`, `0x32` is `'2'`, `0x33` is `'3'` (Task 15); and an `int` is stored **little-endian** — lowest byte first (Tasks 11, 15). So the 4 bytes land on disk in the order `0x33 0x32 0x31 0x30` = `'3' '2' '1' '0'`.

**Takeaway to say out loud:** "`fwrite` copies the raw bytes in memory order — and little-endian puts the lowest byte first."

---

## Iteration 2 — Repeat with a new number, and prove the theory

**a. What we set up**

Change one line in `write_num.c`:

```c
    int number = 0x61626364;
```

And, as a hint, a second little program that prints character codes in hex:

```c
/* show_codes.c */
#include <stdio.h>

int main()
{
    char symbols[] = {'0', '1', '2', '3', '4', '5'};
    int i = 0;

    while (i < 6)
    {
        printf("'%c' = %#x\n", symbols[i], symbols[i]);
        i = i + 1;
    }

    return 0;
}
```

**b. Task**

Predict `cat num.txt` for the new number *before* running. Then run both programs. Finally, look at the int's bytes directly in the debugger:

```
lldb write_num
(lldb) breakpoint set --file write_num.c --line 8
(lldb) run
(lldb) memory read -f x -s 1 -c 4 &number
```

**c. Observation (what you should find)**

```
dcba
```

`0x61`=`'a'`, `0x62`=`'b'`, `0x63`=`'c'`, `0x64`=`'d'` — and little-endian writes the lowest byte (`0x64`=`'d'`) first, so the file reads `dcba`. `show_codes.c` confirms the codes:

```
'0' = 0x30
'1' = 0x31
'2' = 0x32
'3' = 0x33
'4' = 0x34
'5' = 0x35
```

And the debugger shows the int's 4 bytes in memory, lowest address first:

```
0x00007fffffffe4dc: 64 63 62 61
```

That is exactly the order they hit the file. (Notice a `char` array like `symbols` is stored *in order*, one byte each — only a multi-byte `int` gets reversed by little-endian.)

**Takeaway to say out loud:** "The file order is the memory order — and `memory read` shows me that order before I even write it."

---

## Iteration 3 — Write a whole int array

**a. What we set up**

```c
/* write_nums.c */
#include <stdio.h>

int main()
{
    int nums[] = {0x44434241, 0x48474645, 0x4c4b4a49, 0x504f4e4d};
    FILE *file;

    file = fopen("numbers.txt", "wb");
    fwrite(nums, sizeof(nums), 1, file);
    fclose(file);

    return 0;
}
```

**b. Task**

Predict `cat numbers.txt`. (Work out each int's 4 bytes, lowest first, then read across all four ints.) Then:

```
clang write_nums.c -o write_nums
./write_nums
cat numbers.txt
```

**c. Observation (what you should find)**

```
ABCDEFGHIJKLMNOP
```

Each int is reversed by little-endian — `0x44434241` → `0x41 0x42 0x43 0x44` = `ABCD` — but the four numbers were chosen so that, once reversed, they line up to read forward. `sizeof(nums)` is 16, so all 16 bytes go to the file in one `fwrite`.

**Takeaway to say out loud:** "An array writes element by element; each int still reverses its own 4 bytes."

---

## Iteration 4 — Text mode: write the numbers a person can read

**a. What we set up**

Now write numbers as **readable text** with `fprintf` instead of raw bytes with `fwrite`:

```c
/* write_user_numbers.c */
#include <stdio.h>

int main()
{
    int numbers[4];
    int i = 0;
    FILE *file;

    printf("Enter 4 numbers: ");
    while (i < 4)
    {
        scanf("%i", &numbers[i]);
        i = i + 1;
    }

    file = fopen("user_numbers.txt", "w");
    i = 0;
    while (i < 4)
    {
        fprintf(file, "%i\n", numbers[i]);
        i = i + 1;
    }
    fclose(file);

    return 0;
}
```

**b. Task**

```
clang write_user_numbers.c -o write_user_numbers
./write_user_numbers
```

Type four numbers, e.g. `65 66 67 68`, then:

```
cat user_numbers.txt
```

**c. Observation (what you should find)**

```
65
66
67
68
```

No reversing, no surprises — the file shows exactly the numbers you typed, one per line. Because `fprintf("%i", ...)` writes each number as its **digit characters** (`65` becomes the two bytes `'6'` `'5'`), not as the int's 4 raw bytes. This is the whole difference: `fwrite` = raw bytes (binary), `fprintf` = readable characters (text).

**Takeaway to say out loud:** "`fprintf` writes the number as digits you can read; `fwrite` writes the raw bytes you cannot."

---

## Iteration 5 — Reading a file back

**a. What we set up**

Whatever you write, you read back the same way. Raw bytes with `fread`:

```c
/* read_num.c */
#include <stdio.h>

int main()
{
    int number;
    FILE *file;

    file = fopen("num.txt", "rb");
    fread(&number, sizeof(number), 1, file);
    fclose(file);

    printf("Read back: %#x\n", number);
    return 0;
}
```

Readable text with `fscanf`:

```c
/* read_user_numbers.c */
#include <stdio.h>

int main()
{
    int numbers[4];
    int i = 0;
    FILE *file;

    file = fopen("user_numbers.txt", "r");
    while (i < 4)
    {
        fscanf(file, "%i", &numbers[i]);
        i = i + 1;
    }
    fclose(file);

    i = 0;
    while (i < 4)
    {
        printf("numbers[%i] = %i\n", i, numbers[i]);
        i = i + 1;
    }

    return 0;
}
```

**b. Task**

Build and run both. Predict what `read_num.c` prints (remember `num.txt` currently holds the bytes from Iteration 2).

**c. Observation (what you should find)**

`read_num` reads the 4 raw bytes straight back into an int:

```
Read back: 0x61626364
```

The bytes were reversed on disk, but `fread` puts them back into the int's memory in the same reversed order, so the int is whole again. And `read_user_numbers` parses the digit characters back into numbers:

```
numbers[0] = 65
numbers[1] = 66
numbers[2] = 67
numbers[3] = 68
```

`fread` matches `fwrite` (raw bytes); `fscanf`/`fprintf` match (text). Read a file the same way it was written.

**Takeaway to say out loud:** "Read the way you wrote: `fread` for raw bytes, `fscanf` for text."

---

## Iteration 6 — Text files vs binary files, out in the open

**a. What we set up**

You have used both kinds of file for weeks without noticing. A **text file** holds only readable characters; a **binary file** holds raw bytes that are not meant to be read as text. Take a source file and the program built from it:

```c
/* sum.c */
#include <stdio.h>

/* Assumption: only two-digit numbers (10 to 99) are given. */
int sum_of_digits(int number);

int main()
{
    int number = 45;
    int total;

    total = sum_of_digits(number);
    printf("Sum of digits of %i is %i.\n", number, total);
    return 0;
}

/* Assumption: only two-digit numbers (10 to 99) are given. */
int sum_of_digits(int number)
{
    int tens = number / 10;
    int units = number % 10;
    return tens + units;
}
```

**b. Task**

```
clang sum.c -o sum
file sum.c
file sum
cat sum.c
cat sum
xxd sum | head -1
```

**c. Observation (what you should find)**

```
sum.c: C source, ASCII text
sum:   ELF 64-bit LSB pie executable, x86-64, ...
```

`cat sum.c` prints your readable program. `cat sum` prints gibberish and may make the terminal beep — because it is **raw bytes**, not text. `xxd` shows those bytes, starting with the ELF signature:

```
00000000: 7f45 4c46 0201 0100 0000 0000 0000 0000  .ELF............
```

So: `sum.c` and `sum.py` are **text files** (source code — you edit them). The compiled `sum`, and the tools `clang` and `lldb` themselves, are **binary files** — raw machine bytes. Same `cat`, completely different result, because the *content* is a different kind of thing.

**Takeaway to say out loud:** "Source code is a text file; the executable, `clang`, and `lldb` are binary files."

---

## Iteration 7 — When to use which (and where your phone does)

**a. What we set up**

Neither kind is "better" — each fits a job. The trade-off:

- **Text** — readable by humans, easy to edit, easy to move between systems; but larger and slower to parse.
- **Binary** — compact and fast for the computer, and the only sensible way to store non-text things (images, sound); but not human-readable.

**b. Task**

For each app on your phone, decide whether it mainly stores **text** or **binary**, and why. Write your guess before reading the answer.

- Contacts
- WhatsApp (a photo you send; a chat backup)
- Instagram
- Free Fire

**c. Observation (what you should find)**

- **Contacts** — the list of names and numbers is small structured data, kept in a compact **binary** database (SQLite) so lookups are fast; an *export* (`.vcf`) is **text** so you can share and read it.
- **WhatsApp** — a photo or voice note is **binary** (there is no way to write an image as readable characters); a chat *export* (`.txt`) is **text**.
- **Instagram** — every photo and video is **binary**; the caption text inside is text, but the media dominates.
- **Free Fire** — game art, models, sounds and saved progress are **binary**, packed tight so the game loads fast and stays small.

The pattern: **readable/shareable/editable → text; media, or compact-and-fast → binary.**

**Takeaway to say out loud:** "Text when a human must read or edit it; binary for media, or when small and fast matters."

---

## One-page reference

| Call | What it does |
|---|---|
| `fopen("f", "wb")` / `"w"` | open a file for writing (on Linux the `b` changes nothing) |
| `fopen("f", "rb")` / `"r"` | open a file for reading |
| `fwrite(&x, sizeof(x), 1, f)` | write the **raw bytes** of `x` (binary) |
| `fprintf(f, "%i\n", x)` | write `x` as **readable digits** (text) |
| `fread(&x, sizeof(x), 1, f)` | read raw bytes back into `x` |
| `fscanf(f, "%i", &x)` | parse readable digits back into `x` |
| `fclose(f)` | close the file (always) |

**Shell:** `cat f` (show contents) · `file f` (guess text vs binary) · `xxd f` (show raw bytes).

**Rules to keep:**
- A file is just bytes; `fwrite` = raw bytes, `fprintf` = readable text.
- `fwrite` of an `int` follows memory order → little-endian puts the lowest byte first.
- Read the way you wrote: `fread`↔`fwrite`, `fscanf`↔`fprintf`.
- Source (`.c`, `.py`) = text; executables, `clang`, `lldb` = binary.
- Text for humans/sharing; binary for media or compact speed.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| file | ఫైల్ | డిస్క్‌పై నిల్వ ఉన్న బైట్ల వరుస |
| write / read | రాయడం / చదవడం | ఫైల్‌లోకి పెట్టడం / ఫైల్ నుండి తీసుకోవడం |
| raw bytes (binary) | ముడి బైట్లు | విలువను ఉన్నదున్నట్టు బైట్లుగా (`fwrite`) |
| readable text | చదవగలిగే వచనం | విలువను అక్షరాలు/అంకెలుగా (`fprintf`) |
| `fwrite` / `fread` | ఎఫ్‌రైట్ / ఎఫ్‌రీడ్ | ముడి బైట్లు రాయడం / చదవడం |
| `fprintf` / `fscanf` | ఎఫ్‌ప్రింట్ఎఫ్ / ఎఫ్‌స్కాన్ఎఫ్ | వచనంగా రాయడం / చదవడం |
| text file | వచన ఫైల్ | చదవగలిగే అక్షరాలు మాత్రమే (`sum.c`) |
| binary file | బైనరీ ఫైల్ | ముడి బైట్లు — నేరుగా చదవలేనివి (`sum`, `clang`) |
| executable | నిర్వహణ ఫైల్ | నడిచే ప్రోగ్రామ్ (బైనరీ) |
| little-endian | లిటిల్-ఎండియన్ | తక్కువ బైట్ ముందు నిల్వ చేసే క్రమం |
