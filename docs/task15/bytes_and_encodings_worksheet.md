# Bytes Become Meaning — Colours, Characters & Encodings

**Goal.** A byte is just a number from 0 to 255. What that number *means* — a shade of red, the letter `A`, part of a Telugu letter — is nothing more than an **agreement**: a standard everyone follows. Today you will watch the same bytes stand for colours and for characters, see how C stores a name as bytes ending in a special marker, and learn the names for the pieces of text you type in your programs.

**You need:** your Linux VM (WSL) with `clang` and `lldb`, a notebook, and a pencil.

> **The golden rule of bytes**
> A byte holds a number 0–255. It has no colour and no letter of its own — a **standard** decides what the number means. Change the standard and the same byte means something else.

---

## Iteration 1 — A byte is just a number 0–255

**a. What we set up**

From Task 10 you know a byte is 8 bits, so it can hold `0000 0000` (0) up to `1111 1111` (255) — 256 different values.

**b. Task**

In your notebook, write the smallest and largest number one byte can hold, in decimal and in hex.

**c. Observation (what you should find)**

`0` to `255`, or `0x00` to `0xFF`. That is the whole range. Everything on this sheet — a colour channel, a keyboard character — is built out of bytes holding numbers in exactly this range. Nothing else is stored; meaning is added by agreement.

**Takeaway to say out loud:** "A byte is a number from 0 to 255 — meaning comes from a standard, not from the byte."

---

## Iteration 2 — A pixel is 3 bytes (and sometimes 4)

**a. What we set up**

A single dot of colour on your screen — a **pixel** — is usually **3 bytes**: how much **R**ed, how much **G**reen, how much **B**lue, each 0–255. Written together it is `0xRRGGBB`.

```c
#include <stdio.h>

int main()
{
    unsigned char pixel[3];

    pixel[0] = 0xC8;   /* Red   = 200 */
    pixel[1] = 0x64;   /* Green = 100 */
    pixel[2] = 0x32;   /* Blue  =  50 */

    printf("R=%i G=%i B=%i\n", pixel[0], pixel[1], pixel[2]);

    return 0;
}
```

**b. Task**

Build with `clang -g`, run, then look at the raw bytes in the debugger:

```
(lldb) breakpoint set --file <file>.c --line 12
(lldb) run
(lldb) memory read -f x -s 1 -c 3 pixel
```

**c. Observation (what you should find)**

```
R=200 G=100 B=50
0x7fffffffe4c5: c8 64 32
```

Three bytes, three colour amounts — the exact channel idea you extracted with masks in Task 10. Some images add a **4th byte, the alpha channel**, for **opacity**: `0xRRGGBBAA`, where the 4th byte says how see-through the pixel is (255 = fully solid, 0 = fully transparent). Same bytes-hold-numbers rule, one extra number.

**Takeaway to say out loud:** "A pixel is 3 bytes — R, G, B — plus a 4th for opacity when an image needs transparency."

---

## Iteration 3 — Characters are numbers too: ASCII and char literals

**a. What we set up**

Every key on your keyboard is stored as a number, agreed by a standard called **ASCII**. ASCII uses only **7 bits**, so it numbers 128 characters (0–127). A single character written in **single quotes**, like `'A'`, is called a **char literal** — and its value is just that ASCII number.

```c
#include <stdio.h>

int main()
{
    char upper = 'A';
    char lower = 'a';
    char digit = '0';

    printf("'A' = %i\n", upper);
    printf("'a' = %i\n", lower);
    printf("'0' = %i\n", digit);
    printf("gap = %i\n", lower - upper);

    return 0;
}
```

**b. Task**

Predict the four numbers on paper, then compile and run.

**c. Observation (what you should find)**

```
'A' = 65
'a' = 97
'0' = 48
gap = 32
```

`'A'` is `65` (`0x41`), and the uppercase letters run `0x41`–`0x5A`. `'a'` is `97` (`0x61`), lowercase `0x61`–`0x7A`. The digit `'0'` is `48` (`0x30`) — *not* zero! A **char literal** (single quotes) is simply the number a character stands for. And the gap between a lowercase and its uppercase letter is exactly **32** = `0x20` — a single bit (bit 5). That is the same case bit from Task 10: `'A' | 0x20` gives `'a'`, and `'a' & ~0x20` gives `'A'`.

Escape sequences are char literals too: `'\0'` is `0`, `'\n'` is `10`, `'\t'` is `9`. You will meet `'\0'` in the next iteration.

**Takeaway to say out loud:** "A char literal in single quotes is just a number — `'A'` is 65, and case is one bit apart."

---

## Iteration 4 — Strings in C: string literals and the `'\0'` terminator

**a. What we set up**

Text in **double quotes**, like `"shiva"`, is called a **string literal**. C stores it as an array of characters — and quietly adds one extra byte at the end: the **null character** `'\0'` (value 0), also called the **string terminator**. It marks where the text stops.

```c
#include <stdio.h>

int main()
{
    char name[] = "shiva";

    printf("text  = %s\n", name);
    printf("bytes = %zu\n", sizeof(name));

    return 0;
}
```

**b. Task**

`"shiva"` has 5 letters — so how many bytes is `name`? Predict, then run, then read the raw bytes:

```
(lldb) breakpoint set --file <file>.c --line 8
(lldb) run
(lldb) memory read -f x -s 1 -c 6 &name
(lldb) memory read -f c -s 1 -c 6 &name
```

**c. Observation (what you should find)**

```
text  = shiva
bytes = 6
0x7fffffffe4da: 73 68 69 76 61 00
0x7fffffffe4da: s h i v a \0
```

Five letters, but **6 bytes** — because a **string literal** always ends with the **null terminator** `'\0'` (`0x00`). That is why `char name[] = "shiva"` reserves one *more* element than the number of letters. The terminator is how `printf("%s", ...)` knows where to stop: it prints bytes until it hits the `0`.

Now the key contrast — single quotes vs double quotes:

```
'A'   →  a char literal:    one value, the number 65
"A"   →  a string literal:   TWO bytes, 65 then 0  ('A' and '\0')
```

So `char c = 'A';` is one byte, but `char s[] = "A";` is two. Mixing up `'A'` and `"A"` is the most common beginner slip — single quotes make a **char literal** (a number), double quotes make a **string literal** (bytes ending in `'\0'`).

**Takeaway to say out loud:** "A string literal is characters plus a hidden `'\0'` — `\"shiva\"` is 6 bytes, and `'A'` is not `\"A\"`."

---

## Iteration 5 — Beyond 127: code pages, Unicode, and UTF (concept)

**a. What we set up**

ASCII only numbers 128 characters — enough for English, nowhere near enough for Telugu. Two ideas extended it:

- **Extended ASCII / code pages:** use the 8th bit to number another 128 characters (128–255). But there is no single set — many different **code pages** reused those slots for different languages, so the *same* byte meant different letters in different places.
- **Unicode:** one giant standard that gives *every* character in *every* language its own number, called a **code point**, written like `U+0C15` (that one is the Telugu letter క). Telugu lives in the range `U+0C00`–`U+0C7F`.

**b. Task**

A code point is just a number. But a number bigger than 255 does not fit in one byte — so how is it stored? That job belongs to an **encoding**. Read the three common ones and note what differs.

**c. Observation (what you should find)**

A **code point** (the character's number) and an **encoding** (how that number becomes bytes) are two separate things. The common encodings of Unicode:

- **UTF-8** — 1 byte for plain ASCII, but **2–4 bytes** for other characters. A Telugu letter takes **3 bytes** here. ASCII text is unchanged, which is why UTF-8 is used almost everywhere.
- **UTF-16** — 2 bytes for most characters (4 for rare ones).
- **UTF-32** — always 4 bytes, one fixed size for every character.

The lesson: in UTF-8, one Telugu letter on screen is **several bytes** in memory — so "number of bytes" and "number of characters you see" are no longer the same, the way they were for plain ASCII. (You do not need to compute the bytes by hand today — just hold the idea: code point = the number, encoding = the bytes.)

**Takeaway to say out loud:** "Unicode gives every character a number; UTF-8/16/32 are different ways to turn that number into bytes."

---

## Iteration 6 — Why encodings exist: the 7-bit story, set straight

**a. What we set up**

You may hear "the network only handles 7 bits, so the 8th is dropped." That is **not** true of modern routers — they move full 8-bit **bytes (octets)** perfectly well. The 7-bit story comes from two real but different places.

**b. Task**

Read both and note which is about *saving space* and which is about *surviving an old channel*.

**c. Observation (what you should find)**

- **Old 7-bit-only channels.** Early telegraph, some serial links, and early **email (SMTP)** were built for 7-bit ASCII and could mangle the 8th bit. To send 8-bit data (an image, a Telugu message) safely through them, the data is **encoded** into only ASCII characters — that is exactly why **Base64** exists.
- **Packing to save space.** **Phone SMS** uses a 7-bit alphabet and *packs* it so that 8 characters fit into 7 bytes — squeezing 160 characters into 140 bytes. This is done by the phone and the SMS centre, **not** by routers.

So: routers today are **8-bit clean**. The 7-bit tricks are either historical (encode 8-bit data to survive a 7-bit channel, e.g. Base64) or a space-saving packing (SMS) — never a limit of modern networking.

**Takeaway to say out loud:** "Modern routers carry full bytes — 7-bit tricks are old-channel encodings or SMS packing, not a network limit."

---

## One-page reference

| Thing | What it is |
|---|---|
| byte | a number 0–255 (`0x00`–`0xFF`) |
| pixel (RGB) | 3 bytes: Red, Green, Blue |
| pixel (RGBA) | 4 bytes: + Alpha (opacity) |
| ASCII | 7-bit standard, 128 characters (0–127) |
| char literal | one character in **single** quotes: `'A'` = the number 65 |
| string literal | text in **double** quotes: `"shiva"` = the characters **+ `'\0'`** |
| `'\0'` | the null character (value 0), the **string terminator** |
| code point | a character's Unicode number, e.g. `U+0C15` |
| encoding | how a code point becomes bytes: UTF-8, UTF-16, UTF-32 |

**Key ASCII codes:** `'A'`=65 (`0x41`) · `'a'`=97 (`0x61`) · `'0'`=48 (`0x30`) · space=32 (`0x20`) · `'\0'`=0. Case differs by 32 (`0x20`, one bit).

**Rules to keep:**
- A byte is a number; a standard gives it meaning.
- Single quotes → **char literal** (a number). Double quotes → **string literal** (bytes ending in `'\0'`).
- `char name[] = "shiva"` is **6** bytes — one more than the letters, for the terminator.
- `'A'` (1 value) is not `"A"` (2 bytes).
- Unicode gives the **number** (code point); UTF-8/16/32 give the **bytes** (encoding).
- Modern routers are 8-bit clean; 7-bit is history (Base64) or SMS packing.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| byte | బైట్ | 0–255 మధ్య ఒక సంఖ్యను నిల్వ చేసే 8 బిట్ల గది |
| pixel | పిక్సెల్ | తెరపై ఒక రంగు చుక్క (R, G, B బైట్లు) |
| alpha / opacity | ఆల్ఫా / అపారదర్శకత | పిక్సెల్ ఎంత దట్టంగా (పారదర్శకం కాదా) ఉందో చెప్పే బైట్ |
| ASCII | ఆస్కీ | అక్షరాలకు సంఖ్యలు ఇచ్చే 7-బిట్ ప్రమాణం |
| char literal | కార్ లిటరల్ | ఒంటి కోట్‌లో ఒక అక్షరం `'A'` — అది ఒక సంఖ్య (65) |
| string literal | స్ట్రింగ్ లిటరల్ | జంట కోట్‌లో అక్షరాల వరుస `"shiva"` — చివర `'\0'` తో |
| null terminator (`'\0'`) | నల్ ముగింపు గుర్తు | స్ట్రింగ్ ఎక్కడ ఆగుతుందో చెప్పే 0 విలువ గల బైట్ |
| Unicode | యూనికోడ్ | ప్రతి భాషలోని ప్రతి అక్షరానికి ఒక సంఖ్య ఇచ్చే ప్రమాణం |
| code point | కోడ్ పాయింట్ | ఒక అక్షరం యొక్క యూనికోడ్ సంఖ్య (`U+0C15`) |
| encoding (UTF-8/16/32) | ఎన్‌కోడింగ్ | కోడ్ పాయింట్‌ను బైట్లుగా మార్చే విధానం |
