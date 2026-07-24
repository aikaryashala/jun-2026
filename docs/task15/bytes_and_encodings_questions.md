# Bytes Become Meaning — Question Bank

Answer on paper, using the worksheet's ideas: a byte is a number 0–255; a **char literal** in single quotes (`'A'`) is that character's number; a **string literal** in double quotes (`"..."`) is the characters **plus a `'\0'`**; and a code point (the character's number) is separate from an encoding (the bytes).

Useful codes: `'A'`=65 (`0x41`), `'a'`=97 (`0x61`), `'0'`=48 (`0x30`), space=32, `'\0'`=0. Answers are **not** in this file.

---

# Part A — Multiple Choice

**A1.** The largest number a single byte can hold is:

- A) 127
- B) 128
- C) 255
- D) 256

**A2.** A pixel with no transparency is usually stored in:

- A) 1 byte
- B) 2 bytes
- C) 3 bytes (R, G, B)
- D) 8 bytes

**A3.** The 4th byte in an RGBA pixel is the:

- A) brightness
- B) alpha (opacity)
- C) a second red
- D) checksum

**A4.** ASCII uses how many bits per character?

- A) 4
- B) 7
- C) 8
- D) 16

**A5.** The value of the char literal `'A'` is:

- A) 0
- B) 1
- C) 65
- D) 97

**A6.** The value of the char literal `'0'` is:

- A) 0
- B) 48
- C) 30
- D) 12

**A7.** How many bytes does `char word[] = "hello";` occupy?

- A) 4
- B) 5
- C) 6
- D) 7

**A8.** The `'\0'` at the end of a C string is called the:

- A) newline
- B) null terminator
- C) space
- D) alpha byte

**A9.** Which is a **string literal**?

- A) `'A'`
- B) `"A"`
- C) `A`
- D) `65`

**A10.** `'A'` and `"A"` differ because:

- A) they are the same thing
- B) `'A'` is one value (65); `"A"` is two bytes (`65` then `0`)
- C) `"A"` is one value; `'A'` is two bytes
- D) `'A'` is invalid C

**A11.** A character's number in Unicode (like `U+0C15`) is called its:

- A) encoding
- B) code point
- C) code page
- D) pixel

**A12.** In UTF-8, a Telugu letter typically takes:

- A) 1 byte
- B) exactly 2 bytes
- C) 3 bytes
- D) always 4 bytes

**A13.** Which statement about modern routers is correct?

- A) they only handle 7 bits, so the 8th is dropped
- B) they are 8-bit (octet) clean and carry full bytes
- C) they pack every 7 bits into a byte
- D) they only carry ASCII

**A14.** The gap between `'a'` and `'A'` is 32 because:

- A) it is random
- B) case differs by one bit (`0x20` = 32)
- C) there are 32 letters
- D) `'a'` comes 32 words later

---

# Part B — Fill in the Blanks

**B1.** A byte holds a number from ____ to ____.

**B2.** An RGB pixel is ____ bytes; an RGBA pixel is ____ bytes.

**B3.** `'A'` in hex is `0x____`, and `'a'` in hex is `0x____`.

**B4.** Text in single quotes is a ____ literal; text in double quotes is a ____ literal.

**B5.** `char name[] = "shiva";` occupies ____ bytes, because of the hidden ____ character.

**B6.** The value of `'\0'` is ____.

**B7.** In Unicode, a character's number is its ________; the way that number is turned into bytes is the ________.

**B8.** `"A"` is stored as the two bytes ____ and ____.

**B9.** The three common Unicode encodings are UTF-____, UTF-____, and UTF-____.

**B10.** Base64 exists so that ____-bit data can travel safely through an old ____-bit-only channel.

---

# Part C — Scenario Questions

**C1.** For `char s[] = "code";`, write (a) how many bytes `s` occupies, and (b) the bytes a `memory read -f x -s 1 -c 6 s` would show (use the ASCII codes: `'c'`=99, `'o'`=111, `'d'`=100, `'e'`=101).

**C2.** Your friend writes `char c = "A";` and it does not work as they expect. Explain the mistake, and give the two ways to correctly store (a) just the character `A`, and (b) the text `"A"`.

**C3.** A pixel is `0xC86432`. Give R, G, and B as decimal values. (You extracted channels like this in Task 10.)

**C4.** Sort these into two groups — **char literals** and **string literals**: `'z'`, `"z"`, `'\0'`, `"hi"`, `'7'`, `"7"`.

**C5.** Two computers show different letters for the same byte `0xE9`. Using the idea of **code pages**, explain how that can happen — and what standard fixes it so every character has one agreed number.

**C6.** A word shows as **3 characters** on screen but `strlen` reports **9** for its UTF-8 bytes. Explain how the character count and the byte count can differ (they were equal for plain ASCII).

**C7.** Convert with a single bitwise step (Task 10): turn `'a'` into `'A'`, and turn `'G'` into `'g'`. Give the operation and the resulting character for each.

---

When you finish, check the answer key — check your *reasoning* (byte counts, which is a char vs a string literal, code point vs encoding), not only the final answers.
