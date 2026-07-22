# Bitwise Operators — Question Bank

Work everything **on paper first**: write the values in binary, one under the other, and go column by column. Where a question asks for binary, 8 bits is enough unless the value needs more. Remember the traps: `~` flips all 32 bits, `>>` drops what falls off, `+` binds tighter than `<<`, `==` binds tighter than `&`.

Part E holds challenge problems — write them in C, compile with `clang -g`, and test with your own `print_bits`. (The Part D programming drills live in the **extra question bank**; do them before Part E.) Answers are **not** in this file.

---

# Part A — Multiple Choice

**A1.** `6 & 3` = ?

- A) 2
- B) 3
- C) 7
- D) 18

**A2.** `6 | 3` = ?

- A) 5
- B) 7
- C) 9
- D) 63

**A3.** `6 ^ 3` = ?

- A) 3
- B) 2
- C) 5
- D) 7

**A4.** On a 32-bit `int`, `~0` = ?

- A) 0
- B) 1
- C) 255
- D) -1

**A5.** `1 << 5` = ?

- A) 5
- B) 10
- C) 32
- D) 64

**A6.** `40 >> 3` = ?

- A) 5
- B) 13
- C) 8
- D) 4

**A7.** For any small positive `x`, `x << 1` is the same as:

- A) `x + 1`
- B) `x * 2`
- C) `x / 2`
- D) `x * x`

**A8.** `7 >> 1` = ?

- A) 3.5
- B) 4
- C) 3
- D) 14

**A9.** `1 << 2 + 1` = ?

- A) 5
- B) 4
- C) 6
- D) 8

**A10.** `x & 1` tells you whether `x` is:

- A) positive
- B) odd
- C) a power of two
- D) zero

**A11.** If `x = 5`, what is the value of `x & 4 == 4`?

- A) 4
- B) 1
- C) 0
- D) 5

**A12.** What does `printf("%d %d", 12 & 10, 12 && 10);` print?

- A) `8 1`
- B) `8 8`
- C) `1 1`
- D) `14 1`

**A13.** On our machines, the signed value `-8 >> 1` = ?

- A) -4
- B) 2147483644
- C) -16
- D) 4

**A14.** The mask `0xFF` in an expression like `x & 0xFF` keeps:

- A) the highest 8 bits of `x`
- B) the lowest 8 bits of `x`
- C) only bit 8
- D) everything except 8 bits

---

# Part B — Fill in the Blanks

**B1.** `13` in 8-bit binary is `________ ________` (two nibbles).

**B2.** Binary `1010 1100` is hex `0x____` and decimal `____`.

**B3.** The mask for bit 6 is `1 << 6`, which is binary `________ ________`, decimal `____`.

**B4.** Set bit `n`:      `x ____ (1 << n)`.

**B5.** Clear bit `n`:    `x & ____(1 << n)`.

**B6.** Toggle bit `n`: the operator is `____`.

**B7.** Check bit `n`:  `(x >> n) & ____`.

**B8.** `0x0F & 0x3C` = `0x____`.

**B9.** `x << 3` multiplies `x` by `____`.

**B10.** `0xF0 >> 4` = `0x____`.

---

# Part C — Predict: decimal, binary, hex

For each expression write **all three**: decimal, binary (8 bits unless more needed), and hex. Show the column work.

**C1.** `9 & 5`

**C2.** `9 | 5`

**C3.** `9 ^ 5`

**C4.** `(9 ^ 5) ^ 5`

**C5.** `~5` (32-bit `int` — decimal and hex; binary may abbreviate the leading bits)

**C6.** `(3 << 4) | 3`

**C7.** `(0xC8 >> 2) & 0x0F`

**C8.** `5 & 3 | 4`

**C9.** `unsigned int u = 0xFFFFFFF0;  u >> 4` (hex and decimal)

**C10.** `int s = -16;  s >> 4` (on our machines — compare with C9 and explain the difference in one line)

---

# Part E — Challenge Problems

**E1. Pixel surgery.** A pixel is `0xAARRGGBB`. Write `unsigned int swap_red_blue(unsigned int pixel)` that exchanges the red and blue channels and leaves alpha and green untouched. Test with `0x80FF6432` — trace the bytes on paper first.

**E2. A permission system.** Store nine permission bits in one `int`: owner/group/others × read/write/execute (owner in the top triple, like `chmod`). Write `int allow(int perm, int who, int what)` and `int deny(int perm, int who, int what)` where `who` is 0/1/2 (owner/group/others) and `what` is 4/2/1. Start from `perm = 0644` (octal!) and produce `0755`.

**E3. TCP flag reader.** Using the flag values from the worksheet (`FIN=0x01 … URG=0x20`), write a function that prints the names of all set flags in a byte, and another that, given a received SYN packet's flags, builds the correct SYN-ACK reply flags.

**E4. GPIO register.** One `unsigned int reg` drives 32 pins. In one sequence of statements: switch LEDs on pins 2, 4 and 6 ON together (one statement), read the button on pin 7 into `pressed`, toggle pin 0 without touching anything else, then switch pin 4 OFF. Print the register in binary after every step.

**E5. Feature flags.** A program has features numbered 0–31. Write `enable(flags, f)`, `disable(flags, f)`, `is_on(flags, f)` and `count_enabled(flags)` — then, using only the operators from this sheet, enable features 1, 3 and 5 in one statement.

**E6. Bit packing a date.** A date needs day (1–31), month (1–12), year (0–4095). Pack them into one `unsigned int` as `year(12 bits) month(4 bits) day(5 bits)` — year highest. Write `pack_date` and the three unpackers, and verify with 22-7-2026: what is the packed value in hex?

**E7. Three mystery expressions.** For each expression, compute it for `x = 12`, `x = 7`, and `x = 40` (binary on paper, or with `print_bits`), then state in one sentence what the expression *does* for any `x`:

```
x & (x - 1)
x | (x + 1)
x & -x
```

---

When you finish, check the answer key — the binary working matters more than the final number. If your value is right but your columns were wrong, the next mask will betray you.
