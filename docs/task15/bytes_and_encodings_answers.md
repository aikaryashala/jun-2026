# Bytes Become Meaning — Answers with Reasoning

Check the **reasoning**, not just the letter. The two ideas behind almost every answer: a byte is a number a *standard* gives meaning to, and single quotes make a **char literal** (a number) while double quotes make a **string literal** (bytes ending in `'\0'`).

---

# Part A — Multiple Choice

**A1. C) 255** — 8 bits, `1111 1111` = 255. The range is 0–255 (256 values), so the largest is 255.

**A2. C) 3 bytes (R, G, B)** — one byte per colour channel, `0xRRGGBB`.

**A3. B) alpha (opacity)** — RGBA adds a 4th byte saying how see-through the pixel is (255 solid, 0 transparent).

**A4. B) 7** — ASCII numbers 128 characters (0–127), which needs 7 bits.

**A5. C) 65** — `'A'` is `0x41` = 65. (97 is `'a'`.)

**A6. B) 48** — the *character* `'0'` is `0x30` = 48, not the number zero. This is why `'0'` and `0` are different.

**A7. C) 6** — `"hello"` is 5 letters plus the `'\0'` terminator = 6 bytes.

**A8. B) null terminator** — the `'\0'` (value 0) marks where the string ends; `printf("%s")` stops when it reaches it.

**A9. B) `"A"`** — double quotes make a string literal. `'A'` (single quotes) is a char literal; bare `A` is a name; `65` is a number.

**A10. B) `'A'` is one value (65); `"A"` is two bytes (`65` then `0`)** — the string literal always carries a hidden `'\0'`.

**A11. B) code point** — the character's Unicode number, e.g. `U+0C15`. The *encoding* is how that number becomes bytes; a *code page* is an old 8-bit set.

**A12. C) 3 bytes** — Telugu sits in the range UTF-8 stores in 3 bytes. (ASCII is 1 byte; the rare highest characters are 4.)

**A13. B) they are 8-bit (octet) clean and carry full bytes** — modern routers handle full bytes. The "7-bit" idea is history or SMS packing, not a router limit.

**A14. B) case differs by one bit (`0x20` = 32)** — `'a'` = `'A'` with bit 5 set, so `'a' - 'A' = 32`. Flipping that one bit switches case (Task 10).

---

# Part B — Fill in the Blanks

**B1.** `0` to `255` (`0x00` to `0xFF`).

**B2.** `3` bytes; `4` bytes (the 4th is alpha).

**B3.** `'A'` = `0x41`; `'a'` = `0x61`.

**B4.** **char** literal (single quotes); **string** literal (double quotes).

**B5.** `6` bytes, because of the hidden **null** (`'\0'`) character — 5 letters + 1 terminator.

**B6.** `0` — the null character's value is zero.

**B7.** its **code point**; the **encoding** (UTF-8/16/32).

**B8.** `65` and `0` — `'A'` then `'\0'`.

**B9.** UTF-**8**, UTF-**16**, UTF-**32**.

**B10.** so that **8**-bit data can travel through an old **7**-bit-only channel (e.g. early email) — that is why Base64 encodes bytes into plain ASCII.

---

# Part C — Scenario Questions

**C1.** (a) `"code"` is 4 letters + `'\0'` = **5 bytes**... but the array is exactly `char s[5]`. The `memory read` in (b) asks for 6 bytes, so it shows the 5 string bytes plus one *extra* neighbouring byte.
(b) The five string bytes are `63 6f 64 65 00` (`c o d e \0`); the 6th byte is whatever sits next to the array in memory (unknown — not part of the string). So `s` occupies **5 bytes**, and the first five of the dump are `63 6f 64 65 00`.

**C2.** `char c = "A";` is wrong because `"A"` is a **string literal** — two bytes (`'A'` and `'\0'`) — but `c` is a single `char`, which holds only one byte. The fixes:
- (a) just the character: `char c = 'A';` — a **char literal**, one byte, value 65.
- (b) the text: `char s[] = "A";` — a **string literal**, a 2-byte array (`65`, `0`).

**C3.** `0xC8 = 200` (R), `0x64 = 100` (G), `0x32 = 50` (B). So **R=200, G=100, B=50** — each hex byte read on its own.

**C4.**
- **char literals** (single quotes, one character): `'z'`, `'\0'`, `'7'`.
- **string literals** (double quotes, characters + `'\0'`): `"z"`, `"hi"`, `"7"`.

Note `'7'` (the char literal, value 55) and `"7"` (a 2-byte string) are different, just like `'A'` vs `"A"`.

**C5.** A single byte like `0xE9` has no fixed letter of its own — an **8-bit code page** decides what it means, and different computers used different code pages (one might map `0xE9` to `é`, another to a different symbol). Because the standards disagreed, the same byte showed different characters. **Unicode** fixes this by giving every character in every language one agreed number (a code point), so `0x…` no longer depends on which code page is loaded.

**C6.** For plain ASCII, one character = one byte, so counting characters and counting bytes gave the same answer. In **UTF-8**, a non-ASCII character (like Telugu) takes **more than one byte** — about 3 each. So 3 visible characters × 3 bytes = 9 bytes, and `strlen` counts **bytes**, not characters. Character count and byte count are only equal when every character is 1 byte (ASCII).

**C7.** The case bit is `0x20` (Task 10):
- `'a'` → `'A'`: clear the bit — `'a' & ~0x20` = `0x61 & ~0x20` = `0x41` = **`'A'`**.
- `'G'` → `'g'`: set the bit — `'G' | 0x20` = `0x47 | 0x20` = `0x67` = **`'g'`**.

---

**Footnote — a `sizeof` surprise (optional):** in C, `sizeof("A")` is **2** (the string's two bytes), but `sizeof('A')` is **4**, not 1 — because a char literal in C actually has type `int`. The *value* of `'A'` is still 65 and it stores into a `char` as one byte; only the literal's own type is `int`. Don't let it shake the main rule: `'A'` is one character's number, `"A"` is two bytes.

**Pattern to notice across your mistakes:** nearly every error is one of three — forgetting the `'\0'` when counting a string's bytes, mixing up `'x'` (char literal, a number) with `"x"` (string literal, bytes), or confusing a **code point** (the character's number) with an **encoding** (how it becomes bytes). Keep those three straight and the rest follows.
