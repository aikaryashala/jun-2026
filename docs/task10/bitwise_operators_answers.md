# Bitwise Operators — Answers with Reasoning

Check the **binary working**, not just the number. Every answer below shows the columns; if your final value matched but your columns didn't, redo it — masks punish lucky guesses.

---

# Worksheet check — observation predictions (Iteration 2)

With `a = 12 = 1100` and `b = 10 = 1010`:

```
a & b = 1000 =  8      a | b = 1110 = 14      a ^ b = 0110 = 6
~a    = 1111…0011 = -13 (all 32 flipped)
a << 2 = 11 0000 = 48   a >> 2 = 0011 = 3
```

# Worksheet check — debug trace (Iteration 6)

`bits_debug.c`, after each statement (low byte shown; the other 24 bits stay 0):

| after line | decimal | hex | binary | what happened |
|---|---|---|---|---|
| `x = 0` | 0 | 0x0 | `0000 0000` | start clean |
| `x = x \| 1 << 3` | 8 | 0x8 | `0000 1000` | bit 3 set (`1<<3` built 8 first) |
| `x = x \| 1 << 5` | 40 | 0x28 | `0010 1000` | bit 5 set, bit 3 kept |
| `x = x ^ 1 << 3` | 32 | 0x20 | `0010 0000` | bit 3 toggled off |
| `x = x & ~(1 << 5)` | 0 | 0x0 | `0000 0000` | mask `…1101 1111` cleared bit 5 |
| `x = x \| 0xF0` | 240 | 0xF0 | `1111 0000` | four bits set at once |
| `x = x >> 4` | 15 | 0xF | `0000 1111` | the group marched right 4 places |
| `x = x << 1` | 30 | 0x1E | `0001 1110` | doubled; a 0 entered on the right |

Program prints `x = 30`.

---

# Part A — Multiple Choice

**A1. A) 2** — `110 & 011 = 010`. Only the middle column has 1 in both.

**A2. B) 7** — `110 | 011 = 111`. Every column has at least one 1.

**A3. C) 5** — `110 ^ 011 = 101`. Top and bottom columns differ; the middle agrees, so it goes 0.

**A4. D) -1** — `~0` turns on all 32 bits, and all-ones is how a signed `int` writes `-1` (also: `~x = -x - 1` with `x = 0`).

**A5. C) 32** — a single 1 marching left five places lands on the 32s column. `1 << n` is 2ⁿ.

**A6. A) 5** — `40 = 10 1000`; three steps right → `101` = 5. Same as `40 / 8`.

**A7. B) `x * 2`** — every bit moves one column left, and every column is worth double.

**A8. C) 3** — `111 >> 1 = 11`; the rightmost 1 fell off the edge. `>>` floors, exactly like `/` in Task 1.

**A9. D) 8** — the trap: `+` binds tighter than `<<`, so this is `1 << (2 + 1) = 1 << 3`. Parenthesize mixed expressions, always.

**A10. B) odd** — bit 0 is the 1s column; it is 1 exactly for odd numbers. `x & 1` reads just that column.

**A11. B) 1** — second trap: `==` binds tighter than `&`, so this is `x & (4 == 4)` = `5 & 1` = 1. What you meant needs brackets: `(x & 4) == 4`.

**A12. A) `8 1`** — `&` works the columns (`1100 & 1010 = 1000 = 8`); `&&` only asks "both non-zero?" and answers 1. One symbol apart, different worlds.

**A13. A) -4** — on our machines a signed right shift copies the **sign bit** in from the left, so `-8 = …11111000` becomes `…11111100 = -4`. (Compare C9/C10.)

**A14. B) the lowest 8 bits** — `0xFF = 1111 1111` in the bottom byte and 0 everywhere above; `&` keeps only where the mask has 1s.

---

# Part B — Fill in the Blanks

**B1.** `0000 1101` — 8 + 4 + 1.

**B2.** `0xAC`, `172` — nibbles `1010 = A`, `1100 = C`; 128 + 32 + 8 + 4 = 172.

**B3.** `0100 0000`, `64` — one 1 on the 64s column.

**B4.** `|` — OR sets.

**B5.** `~` — `x & ~(1 << n)`: the mask is all 1s with a single 0 at position n.

**B6.** `^` — XOR toggles.

**B7.** `1` — bring bit n to the 1s column, then keep only that column.

**B8.** `0x0C` — `0000 1111 & 0011 1100 = 0000 1100` = 12.

**B9.** `8` — three doublings: 2³.

**B10.** `0x0F` — `1111 0000` marched four right: the top nibble became the bottom nibble.

---

# Part C — Predict: decimal, binary, hex

**C1.** `9 & 5` → **1, `0000 0001`, 0x01**

```
  1001
& 0101
------
  0001
```

**C2.** `9 | 5` → **13, `0000 1101`, 0x0D** — same columns, OR keeps every 1.

**C3.** `9 ^ 5` → **12, `0000 1100`, 0x0C** — bits 2 and 3 differ; bits 0 and… careful: bit 0 is 1 in both → 0. Only the disagreeing columns survive.

**C4.** `(9 ^ 5) ^ 5` → **9, `0000 1001`, 0x09** — XOR with the same value twice cancels: `12 ^ 5 = 9`. This is the undo property.

**C5.** `~5` → **-6, 0xFFFFFFFA** — `5 = …0000 0101`; flipping all 32 gives `1111 … 1111 1010`. Check with the rule: `~x = -x - 1 = -6`.

**C6.** `(3 << 4) | 3` → **51, `0011 0011`, 0x33** — the pair `11` copied into two nibbles: shift makes `0011 0000`, OR pastes `0011` below it.

**C7.** `(0xC8 >> 2) & 0x0F` → **2, `0000 0010`, 0x02** — `1100 1000 >> 2 = 0011 0010` (50); `& 0000 1111` keeps the low nibble `0010`.

**C8.** `5 & 3 | 4` → **5, `0000 0101`, 0x05** — `&` binds tighter than `|`: `(5 & 3) | 4 = 1 | 4 = 5`. (If you computed `5 & (3 | 4)` you got 5 too — but by luck; the grouping was still wrong. Columns, not luck.)

**C9.** `0xFFFFFFF0 >> 4` (unsigned) → **0x0FFFFFFF = 268435455** — unsigned shift pulls **0s** in from the left; the four low 0s fell off the right.

**C10.** `-16 >> 4` (signed) → **-1 = 0xFFFFFFFF** — `-16` is the same bit pattern `0xFFFFFFF0`, but a signed shift copies the **sign bit** in from the left: four fresh 1s. Same input bits as C9, different operator behaviour — the type decides what `>>` feeds in.

---

# Part E — Challenge Problems

(Part D's answers are in the **extra answer key**.)

**E1.**

```c
unsigned int swap_red_blue(unsigned int pixel)
{
    unsigned int red;
    unsigned int blue;

    red  = (pixel >> 16) & 0xFF;
    blue = pixel & 0xFF;

    pixel = pixel & 0xFF00FF00;              /* keep A and G, hole out R and B */
    pixel = pixel | (blue << 16) | red;

    return pixel;
}
```

Trace for `0x80FF6432`: A=`80` R=`FF` G=`64` B=`32` → keep `0x80006400`, OR in `0x320000` and `0xFF` → **`0x803264FF`**.

**E2.** The trick is computing the shift: owner's triple sits 6 bits up, group's 3, others' 0.

```c
int allow(int perm, int who, int what)
{
    return perm | (what << ((2 - who) * 3));
}

int deny(int perm, int who, int what)
{
    return perm & ~(what << ((2 - who) * 3));
}
```

`0644 = 110 100 100`. `allow(p,0,1)` → `111 100 100`; `allow(p,1,1)` → `111 101 100`; `allow(p,2,1)` → `111 101 101` = **0755**. (Octal digits are 3-bit triples — that is *why* chmod uses octal.)

**E3.**

```c
void print_flags(unsigned int flags)
{
    if (flags & 0x01) printf("FIN ");
    if (flags & 0x02) printf("SYN ");
    if (flags & 0x04) printf("RST ");
    if (flags & 0x08) printf("PSH ");
    if (flags & 0x10) printf("ACK ");
    if (flags & 0x20) printf("URG ");
    printf("\n");
}

unsigned int syn_ack_reply(unsigned int received)
{
    (void)received;               /* the reply acknowledges: SYN + ACK */
    return 0x02 | 0x10;           /* = 0x12 */
}
```

Each `if` is one mask test; the reply is the OR of two flag masks — `0x12`, the same byte the worksheet called the famous SYN-ACK.

**E4.**

```c
reg = reg | ((1u << 2) | (1u << 4) | (1u << 6));   /* 0101 0100 = 0x54 */
pressed = (reg >> 7) & 1;                          /* 0 — bit 7 is off  */
reg = reg ^ 1u;                                    /* 0101 0101 = 0x55 */
reg = reg & ~(1u << 4);                            /* 0100 0101 = 0x45 */
```

Combining three masks with `|` before applying is one write instead of three — on real hardware that difference is visible on the pins.

**E5.**

```c
unsigned int enable(unsigned int flags, int f)  { return flags | (1u << f); }
unsigned int disable(unsigned int flags, int f) { return flags & ~(1u << f); }
int is_on(unsigned int flags, int f)            { return (flags >> f) & 1; }
int count_enabled(unsigned int flags)           { return count_set_bits(flags); }
```

Features 1, 3, 5 in one statement: `flags = flags | (1u << 1) | (1u << 3) | (1u << 5);` — that mask is `0x2A` = `0010 1010`. (`count_set_bits` is D5 in the extra answer key — or write the loop inline.)

**E6.** Layout: `year(12) | month(4) | day(5)` — day in bits 0–4, month 5–8, year 9–20.

```c
unsigned int pack_date(unsigned int day, unsigned int month, unsigned int year)
{
    return (year << 9) | (month << 5) | day;
}

unsigned int get_day(unsigned int p)   { return p & 0x1F; }
unsigned int get_month(unsigned int p) { return (p >> 5) & 0x0F; }
unsigned int get_year(unsigned int p)  { return (p >> 9) & 0xFFF; }
```

22-7-2026: `(2026 << 9) = 1037312`, `(7 << 5) = 224`, `+ 22` → `1037558` = **0xFD4F6**. Unpack check: `0xFD4F6 & 0x1F = 22` ✓, `>> 5 & 0xF = 7` ✓, `>> 9 = 2026` ✓. A whole date in 21 bits — 11 to spare.

**E7.** The three mysteries, with the test values (12 = `1100`, 7 = `0111`, 40 = `10 1000`):

| expression | x=12 | x=7 | x=40 | what it does |
|---|---|---|---|---|
| `x & (x - 1)` | 8 | 6 | 32 | **clears the lowest set bit** |
| `x \| (x + 1)` | 13 | 15 | 41 | **sets the lowest zero bit** |
| `x & -x` | 4 | 1 | 8 | **keeps only the lowest set bit** |

Why: subtracting 1 borrows through the low zeros and knocks out the lowest 1 (`1100 - 1 = 1011`); adding 1 carries through the low ones and lands on the lowest 0 (`0111 + 1 = 1000`); and `-x = ~x + 1` agrees with `x` only at the lowest set bit. All three are the same insight seen from three sides: **arithmetic carries and borrows stop exactly at the first opposite bit** — which is also why the power-of-two test (extra bank, D7) works.

---

**Pattern to notice across your mistakes:** nearly every error is one of four habits — working in decimal instead of columns, forgetting `~` and shifts act on all 32 bits, trusting precedence instead of brackets, or building the right mask and applying the wrong operator. Fix the habit; the sheet's idioms (`| set`, `& ~ clear`, `^ toggle`, `>> & 1 check`) never change.
