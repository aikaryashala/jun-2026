# Bitwise Operators — Working the Bits

**Goal.** An `int` is not one number — it is **32 switches** sitting side by side. The bitwise operators (`&` `|` `^` `~` `<<` `>>`) work those switches one column at a time. On this sheet you will *watch* them work: predict on paper, print in binary, and step through with LLDB — decimal, hexadecimal, and binary views of the same bits. Observe first; the rules will write themselves.

**You need:** your Linux VM (WSL) with `clang` and `lldb`, a notebook, and a pencil.

> **The golden rule of bits**
> Bitwise operators have no idea what number you "meant". They line the two values up in binary and decide **each bit position independently**, using only the 0/1 in that column. To predict any result: write both values in binary, one under the other, and go column by column.

---

## Iteration 1 — Binary on paper

**a. What we set up**

Place values, right to left, each double the last:

```
bit:      7    6    5    4    3    2    1    0
value:  128   64   32   16    8    4    2    1
```

**b. Task**

In your notebook, write `13` and `200` as 8-bit binary. Then group each into two halves of 4 bits (called **nibbles**) and write each nibble as one hex digit (`0`–`9`, `A`–`F`).

**c. Observation (what you should find)**

```
 13  =  0000 1101  =  0x0D        (8 + 4 + 1)
200  =  1100 1000  =  0xC8        (128 + 64 + 8)
```

One hex digit = exactly one nibble. That is why programmers write masks in hex: `0xFF` *is* `1111 1111`, no conversion needed in your head.

**Takeaway to say out loud:** "Binary is the truth; hex is binary in groups of four."

---

## Iteration 2 — A program that shows its bits

**a. What we set up**

C's `printf` has `%d` (decimal), `%u` (unsigned), `%x` (hex) — but no reliable binary. So we print binary ourselves. Create `bits_observe.c`:

```c
#include <stdio.h>

void print_bits(int value);

int main()
{
    int a;
    int b;

    a = 12;
    b = 10;

    printf("a      = %d\n", a);
    print_bits(a);
    printf("b      = %d\n", b);
    print_bits(b);

    printf("a & b  = %d\n", a & b);
    print_bits(a & b);
    printf("a | b  = %d\n", a | b);
    print_bits(a | b);
    printf("a ^ b  = %d\n", a ^ b);
    print_bits(a ^ b);
    printf("~a     = %d\n", ~a);
    print_bits(~a);
    printf("a << 2 = %d\n", a << 2);
    print_bits(a << 2);
    printf("a >> 2 = %d\n", a >> 2);
    print_bits(a >> 2);

    return 0;
}

void print_bits(int value)
{
    int i;

    i = 31;
    while (i >= 0) 
    {
        if (((value >> i) & 1) == 1)
        {
            putchar('1');
        }
        else
        {
            putchar('0');
        }
        if (i % 4 == 0 && i != 0)
        {
            putchar(' ');
        }
        i = i - 1
    }
    putchar('\n');
}
```

**b. Task**

Before compiling: write `12` and `10` in binary, one under the other, and **predict all six results** in your notebook — binary first, then decimal. Then:

```
clang -g bits_observe.c -o bits_observe
./bits_observe
```

**c. Observation (what you should find)**

Six predictions, six verdicts. Don't fix your wrong ones — circle them; the next iterations explain exactly those. (And look at `print_bits` itself: it is made of `>>` and `& 1` — the tools teaching themselves.)

**Takeaway to say out loud:** "Predict on paper first; the program is only the judge."

---

## Iteration 3 — `&` and `|`, column by column

**a. What we set up**

```
a = 12  =  1100
b = 10  =  1010
```

**b. Task**

For each column ask: `&` — are **both** bits 1? `|` — is **at least one** bit 1?

**c. Observation (what you should find)**

```
    1100          1100
  & 1010        | 1010
  ------        ------
    1000 = 8      1110 = 14
```

`&` can only *remove* 1s — the result is never bigger than either input. `|` can only *add* 1s — never smaller. That asymmetry is the whole trick behind masks: `&` keeps only what you allow, `|` switches on what you demand.

**Takeaway to say out loud:** "`&` needs both; `|` needs one."

---

## Iteration 4 — `^` the difference detector, `~` the flip-everything

**a. What we set up**

Same `a = 1100`, `b = 1010`. And one surprise from your run: `~a` printed `-13`.

**b. Task**

For `^` ask per column: are the two bits **different**? Then look at the `print_bits(~a)` line of your run and count the 1s.

**c. Observation (what you should find)**

```
    1100
  ^ 1010
  ------
    0110 = 6      (columns where a and b disagree)
```

And `~a` flipped **all 32 bits**, not just the four you wrote:

```
a  = 0000 0000 0000 0000 0000 0000 0000 1100
~a = 1111 1111 1111 1111 1111 1111 1111 0011
```

That pattern is how C stores `-13` — the top bit is the **sign bit**, and when it is 1 a signed `int` is negative. You don't need the full theory today; you need the warning: `~` touches every bit, so on signed numbers it flips the sign too. (Rule worth noting: `~x` is always `-x - 1`.)

Also run this in your head, then check with the XOR column rule: `(a ^ b) ^ b` = ? Doing `^` with the same value twice **undoes it** — every difference is detected twice, i.e. cancelled.

**Takeaway to say out loud:** "`^` marks the differences; doing it twice erases them. `~` flips all 32."

---

## Iteration 5 — Shifts: the bits march

**a. What we set up**

From your run:

```
a      = 0000 1100   (12)
a << 2 = 0011 0000   (48)
a >> 2 = 0000 0011   (3)
```

**b. Task**

Predict on paper, then verify with quick edits to the program (or with `lldb` later): `5 << 1`, `5 << 3`, `7 >> 1`, `40 >> 3`.

**c. Observation (what you should find)**

`10, 40, 3, 5`. Every `<< 1` doubles; every `>> 1` halves **and throws away the bit that falls off the right edge** — that's why `7 >> 1` is `3`, not `3.5`. You met this floor behaviour in Task 1 (`/` keeps the quotient); `>> n` is `/ 2ⁿ` with the same rounding-down.

Two cautions to write in your notebook:
1. Bits shifted past the left edge of the 32 are **gone** — `<<` can overflow silently.
2. Precedence trap: `1 << 2 + 1` is `1 << 3` = 8, because `+` binds **tighter** than `<<`. Parenthesize every mixed expression — always.

**Takeaway to say out loud:** "`<<` doubles, `>>` halves and drops the remainder — and brackets go around everything mixed."

---

## Iteration 6 — Watch the bits change, one statement at a time

**a. What we set up**

Create `bits_debug.c` — a chain of seven transformations on one variable:

```c
#include <stdio.h>

int main()
{
    unsigned int x;

    x = 0;
    x = x | 1 << 3;
    x = x | 1 << 5;
    x = x ^ 1 << 3;
    x = x & ~(1 << 5);
    x = x | 0xF0;
    x = x >> 4;
    x = x << 1;

    printf("x = %u\n", x);

    return 0;
}
```

```
clang -g bits_debug.c -o bits_debug
lldb bits_debug
(lldb) breakpoint set --name main
(lldb) run
```

**b. Task**

Step one statement at a time with `next`, and after **every** line record `x` in all three notations:

```
(lldb) p x        (decimal)
(lldb) p/x x      (hexadecimal)
(lldb) p/t x      (binary — all 32 bits)
```

Build this table in your notebook, and beside each row write *in your own words* what the statement did to the bits (which bit went on? off? flipped? where did the group move?):

| after line | decimal | hex | binary (low byte) | what happened |
|---|---|---|---|---|
| `x = 0` | 0 | 0x0 | `0000 0000` | start clean |
| `x = x \| 1 << 3` | ? | ? | ? | ? |
| … | | | | |

**c. Observation (what you should find)**

No table here — this one is yours. Check it later against the "Debug trace" section of the answer key, only after all seven rows are filled. Two hints if you get stuck: `1 << 3` builds the value 8 *before* the `|` happens, and line 4's `~(1 << 5)` is a mask with **one** zero in it.

**Takeaway to say out loud:** "One statement, one bit story — decimal, hex, and binary are three views of the same 32 switches."

---

## Iteration 7 — Masks in the wild

A **mask** is just a value you build so that `&`, `|`, `^` touch exactly the bits you choose. Almost no theory needed — read each row of these five real domains and infer the pattern: *integer, binary, mask, operation, result.*

**a. Images — one pixel, channels packed as `0xRRGGBB`**

```
color = 0xC86432            1100 1000 0110 0100 0011 0010

red   = (color >> 16) & 0xFF        = 0xC8 = 200
green = (color >> 8)  & 0xFF        = 0x64 = 100
blue  =  color        & 0xFF        = 0x32 =  50

remove green : color & 0xFF00FF     = 0xC80032
full alpha   : pixel | 0xFF000000   (top byte of a 0xAARRGGBB pixel)
```

**b. Networking — IPv4 subnet mask, last octet shown**

```
ip   192.168.1.130      host octet  1000 0010
mask 255.255.255.0      mask octet  0000 0000

network = ip & mask    →  192.168.1.0
host    = ip & ~mask   →  0.0.0.130
```

**c. Networking — TCP flag bits: FIN=0x01 SYN=0x02 RST=0x04 PSH=0x08 ACK=0x10 URG=0x20**

```
flags = 0x12                     0001 0010     (ACK + SYN — this is the famous SYN-ACK)

is SYN set?   flags & 0x02      → not 0 → yes
add FIN:      flags | 0x01      → 0x13
drop ACK:     flags & ~0x10     → 0x02
```

**d. Unix permissions — one `rwx` triple: r=4 w=2 x=1**

```
perm = 6                         110   (rw-)

can execute?  perm & 1          → 0 → no
make it run:  perm | 1          → 7   (rwx)
take write:   perm & ~2         → 4   (r--)
```

**e. Embedded — a GPIO register: one bit per pin**

```
reg = 0

LED on pin 3 ON :   reg = reg | (1 << 3)       0000 1000
LED on pin 3 OFF:   reg = reg & ~(1 << 3)      0000 0000
button on pin 5 :   pressed = (reg >> 5) & 1
```

**f. General flags — many booleans in one int**

```
HAS_KEY = 1   HAS_MAP = 2   DOOR_OPEN = 4   LAMP_ON = 8

state = HAS_KEY | LAMP_ON        1001  = 9
has map?   state & HAS_MAP      → 0 → no
```

**Takeaway to say out loud:** "Same four moves everywhere — build the mask, then `&` to keep or clear, `|` to set, `^` to flip."

---

## Iteration 8 — The mask toolbox

**a. What we set up**

The five idioms, for any bit number `n`:

| Job | Idiom |
|---|---|
| build the mask | `1 << n` |
| set bit n | `x \| (1 << n)` |
| clear bit n | `x & ~(1 << n)` |
| toggle bit n | `x ^ (1 << n)` |
| check bit n | `(x >> n) & 1` |
| extract low nibble | `x & 0xF` |
| combine two masks | `mask1 \| mask2` |

**b. Task**

Take `x = 0x24`. On paper (each part starts again from `0x24`): (1) set bit 1, (2) clear bit 5, (3) toggle bit 2, (4) check bit 5, (5) extract the low nibble. Verify any two of them in `lldb` with `p/x` and `p/t` (`expr` lets you evaluate: `p/x 0x24 | (1 << 1)`).

**c. Observation (what you should find)**

`x = 0x24 = 0010 0100`. Results: **(1)** `0x26`, **(2)** `0x04`, **(3)** `0x20`, **(4)** `1` (bit 5 is on), **(5)** `0x4`. If any differ, redo that row column by column — the mistake is always in the mask, not the operator.

**Takeaway to say out loud:** "Set with OR, clear with AND-NOT, flip with XOR, read with shift-and-one."

---

## One-page reference

| Operator | Name | Per-column rule | Example (4-bit) |
|---|---|---|---|
| `&` | AND | 1 only if **both** are 1 | `1100 & 1010 = 1000` |
| `\|` | OR | 1 if **at least one** is 1 | `1100 \| 1010 = 1110` |
| `^` | XOR | 1 if the bits **differ** | `1100 ^ 1010 = 0110` |
| `~` | NOT | flip every one of the 32 bits | `~12 = -13` |
| `<<` | left shift | march left, 0s enter right; ×2 each step | `0011 << 2 = 1100` |
| `>>` | right shift | march right, right bits fall off; ÷2 each step | `1100 >> 2 = 0011` |

**Rules to keep:** binary is the truth, hex is nibble-shorthand · `&` keeps/clears, `|` sets, `^` toggles · `^` twice = undone · `~x = -x - 1` · `>>` floors like `/` · parenthesize every mixed expression (`+` binds tighter than `<<`; `==` binds tighter than `&`) · in LLDB: `p` decimal, `p/x` hex, `p/t` binary.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| bit | బిట్ | 0 లేదా 1 నిల్వ చేసే అతి చిన్న గది |
| binary | ద్వియాంశం | 0, 1 లతో మాత్రమే సంఖ్యలు రాసే పద్ధతి |
| hexadecimal | షోడశాంశం | 16 గుర్తులతో (0–9, A–F) రాసే పద్ధతి; ఒక్క గుర్తు = 4 బిట్లు |
| nibble | నిబుల్ | 4 బిట్ల గుంపు — ఒక హెక్స్ గుర్తు |
| mask | ముసుగు | కావలసిన బిట్లను మాత్రమే తాకేలా తయారు చేసిన విలువ |
| set a bit | బిట్ ఎక్కించడం | ఆ బిట్‌ను 1 చేయడం (`\|`) |
| clear a bit | బిట్ దించడం | ఆ బిట్‌ను 0 చేయడం (`& ~`) |
| toggle a bit | బిట్ తిప్పడం | 1↔0 మార్చడం (`^`) |
| shift | జరుపుడు | బిట్లను ఎడమకో కుడికో నడిపించడం |
| sign bit | గుర్తు బిట్ | 32వ (పై) బిట్ — 1 అయితే సంఖ్య రుణం |
| flag | జెండా | ఒక పరిస్థితి ఆన్/ఆఫ్ చెప్పే ఒక్క బిట్ |
| overflow | పొర్లిపోవడం | బిట్లు ఎడమ అంచు దాటి పోయి కనబడకుండా పోవడం |
