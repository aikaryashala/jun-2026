# Signed Bytes, Two's Complement, and Gates

**Goal.** Task 15 told you a byte holds 0 to 255. Today you will find out that the *same* byte can hold −128 to +127 instead, that nothing in memory changes when it does, and that the rule for writing a negative number is one you can do on paper in five seconds. You will see the actual bits in LLDB, meet the logical operators alongside the bitwise ones you already know, build every gate in a computer out of one gate, and finish knowing exactly how many addresses the internet has.

**You need:** your Linux VM (WSL), a terminal, `clang`, `lldb`, a notebook, and a pencil.

```
clang --version
lldb --version
```

Any `clang 14` or newer is fine. Make a folder for today:

```
mkdir -p ~/task36
cd ~/task36
```

**Built on:** Task 10 (bits, `&` `|` `^` `~` `<<` `>>`, masks), Task 15 (a byte is 0–255), and Tasks 6 and 9 (LLDB).

**Reference:** [**Task 35 — From a Bit to the Internet**](../task35/bits-to-internet.html) tells this story and the next one as one continuous read — bitwise operators, NAND, bitmaps, bytes, and then addresses and routing. Use it alongside this sheet; this one is the hands-on version.

> **The golden rule of today**
> A byte is eight bits and nothing else. The bits `1111 1011` are **251** if you agreed to call that byte `unsigned char`, and **−5** if you agreed to call it `signed char`. **The memory is identical.** Only the agreement differs. Two's complement is that agreement, and it was chosen for one reason: so the CPU can subtract using the very same circuit it uses to add.

---

## Iteration 1 — One byte, two agreements

**a. What we set up**

```c
/* two_readings.c */
#include <stdio.h>

int main(void)
{
    signed char   s = -5;
    unsigned char u = 251;

    printf("s = %d\n", s);
    printf("u = %u\n", u);
    printf("sizeof(signed char)   = %zu\n", sizeof(signed char));
    printf("sizeof(unsigned char) = %zu\n", sizeof(unsigned char));
    printf("s read as unsigned    = %u\n", (unsigned char)s);

    return 0;
}
```

**b. Task**

Predict all five lines. The last one is the question that matters: if `s` holds −5, what number appears when you read that same byte as unsigned?

```
clang -Wall -g -O0 two_readings.c -o two_readings
./two_readings
```

**c. Observation (what you should find)**

```
s = -5
u = 251
sizeof(signed char)   = 1
sizeof(unsigned char) = 1
s read as unsigned    = 251
```

Two things, and the second is the whole worksheet.

**Both types are one byte.** `sizeof` says 1 for each. There is no extra space anywhere for a sign — the sign has to live *inside* those same eight bits.

**`s` read as unsigned is 251 — the same number `u` was given.** Nothing was converted and nothing moved. The byte holding −5 and the byte holding 251 contain **the identical eight bits**. `%d` and `%u` are two different ways of reading one thing.

So one byte has 256 possible bit patterns, and the type decides how they are shared out:

| Type | Range | How the 256 patterns are used |
|---|---|---|
| `unsigned char` | **0 … 255** | all 256 for positive numbers and zero |
| `signed char` | **−128 … +127** | 128 for negatives, 1 for zero, 127 for positives |

Count the signed row: 128 + 1 + 127 = 256. Exactly the same 256 patterns, cut differently. **You never get more values by choosing a type — you only choose where to put them.**

*(A note for later: plain `char`, with no `signed` or `unsigned` written, is signed on your x86 VM but unsigned on some other machines such as ARM. That is why this worksheet always writes the word out. When the sign matters, say which one you mean.)*

**Takeaway to say out loud:** "One byte, 256 patterns. The type does not change the bits — it changes how I read them."

---

## Iteration 2 — Writing a negative number by hand

The rule has a name — **two's complement** — and it is two steps:

> **Invert every bit, then add one.**
>
> (Inverting every bit on its own is called the **one's complement**. Two's complement is one's complement **+ 1**.)

**a. What we set up**

Nothing but paper. Work out −5, −15 and −128 as eight-bit patterns before you touch the machine.

**b. Task**

For each number: write the positive value in eight bits, invert every bit, add one. Then also write down what that final pattern would be as an *unsigned* number.

**c. Observation (what you should find)**

**−5**

```
  5           0000 0101
  invert      1111 1010        (one's complement)
  add 1       1111 1011   ← −5
```

**−15**

```
  15          0000 1111
  invert      1111 0000
  add 1       1111 0001   ← −15
```

**−128** — this one is strange, and worth doing slowly:

```
  128         1000 0000        (you cannot store +128 in a signed char, but you can still write the pattern)
  invert      0111 1111
  add 1       1000 0000   ← −128
```

**−128 comes back to exactly where it started.** It is its own two's complement. That is not a mistake — it is the direct consequence of the range being lopsided: there are 128 negatives and only 127 positives, so −128 is the one negative with no positive twin. Remember it; it is a favourite exam question and a real source of bugs.

The three results, with all three readings:

| Decimal | Bits | Hex | Same byte as unsigned |
|---|---|---|---|
| −5 | `1111 1011` | `0xFB` | 251 |
| −15 | `1111 0001` | `0xF1` | 241 |
| −128 | `1000 0000` | `0x80` | 128 |

**One shortcut worth noticing.** Every negative number above starts with a `1`; every positive one starts with a `0`. That top bit is called the **sign bit**, and for a `signed char` you can tell the sign at a glance. But be careful — the sign bit is *not* a minus sign glued to the front. `1111 1011` is not "minus 123". The other seven bits are not the magnitude; you have to undo the two's complement to read it.

**To read a negative pattern back**, run the same two steps again — invert and add one — and you get the magnitude:

```
  1111 1011
  invert      0000 0100
  add 1       0000 0101   = 5   →  so the pattern was −5
```

The same procedure both ways. That is one of the reasons this scheme won.

**Takeaway to say out loud:** "Invert every bit and add one — and the same two steps read it back again."

---

## Iteration 3 — Seeing the bits in LLDB

Paper is fine, but you should never take my word for what is in memory. Look.

**a. What we set up**

```c
/* bytes.c */
#include <stdio.h>

int main(void)
{
    signed char nums[3] = {-5, -15, -128};
    unsigned char same[3] = {251, 241, 128};

    printf("%d %d %d\n", nums[0], nums[1], nums[2]);
    printf("%u %u %u\n", same[0], same[1], same[2]);

    return 0;
}
```

Two arrays. One says it holds negatives, the other says it holds large positives. **Predict, before running anything: will their bytes look the same in memory, or different?**

```
clang -Wall -g -O0 bytes.c -o bytes
lldb ./bytes
```

**b. Task**

In LLDB, stop just before the program ends and read the bytes:

```
(lldb) breakpoint set --file bytes.c --line 11
(lldb) run
(lldb) memory read -f b -s 1 -c 3 &nums[0]
(lldb) memory read -f b -s 1 -c 3 &same[0]
```

The three flags are the whole trick, and they are worth learning properly:

| Flag | Means | Values you will use today |
|---|---|---|
| `-f` | **format** — how to display it | `b` binary · `x` hex · `d` signed decimal · `u` unsigned decimal · `c` character |
| `-s` | **size** — how many bytes per item | `1` for a char |
| `-c` | **count** — how many items to show | `3` for our three |

**c. Observation (what you should find)**

```
Process 4192 launched: '/home/student/task36/bytes' (x86_64)
-5 -15 -128
251 241 128
Process 4192 stopped
* thread #1, name = 'bytes', stop reason = breakpoint 1.1
    frame #0: 0x0000555555555191 bytes`main at bytes.c:11:5
   8   	    printf("%d %d %d\n", nums[0], nums[1], nums[2]);
   9   	    printf("%u %u %u\n", same[0], same[1], same[2]);
   10  	
-> 11  	    return 0;
   12  	}
```

Now the two reads:

```
(lldb) memory read -f b -s 1 -c 3 &nums[0]
0x7fffffffe4c8: 0b11111011
0x7fffffffe4c9: 0b11110001
0x7fffffffe4ca: 0b10000000

(lldb) memory read -f b -s 1 -c 3 &same[0]
0x7fffffffe4c4: 0b11111011
0x7fffffffe4c5: 0b11110001
0x7fffffffe4c6: 0b10000000
```

**Identical.** Different addresses, and byte for byte the same contents. `{-5, -15, -128}` and `{251, 241, 128}` are *the same three bytes*. And they are exactly the patterns you worked out on paper in Iteration 2.

*(Your addresses will not be `0x7fffffffe4c8` — the stack sits wherever the operating system puts it that run. The **bits** will match exactly.)*

Now read one array four different ways and watch the number change while the memory does not:

```
(lldb) memory read -f x -s 1 -c 3 &nums[0]
0x7fffffffe4c8: 0xfb 0xf1 0x80

(lldb) memory read -f d -s 1 -c 3 &nums[0]
0x7fffffffe4c8: -5
0x7fffffffe4c9: -15
0x7fffffffe4ca: -128

(lldb) memory read -f u -s 1 -c 3 &nums[0]
0x7fffffffe4c8: 251
0x7fffffffe4c9: 241
0x7fffffffe4ca: 128
```

Three commands, one unchanged piece of memory, three different answers. **`-f d` and `-f u` are the two agreements from Iteration 1, and you can switch between them at will.**

And to prove the point completely, read the *unsigned* array as signed:

```
(lldb) memory read -f d -s 1 -c 3 &same[0]
0x7fffffffe4c4: -5
0x7fffffffe4c5: -15
0x7fffffffe4c6: -128
```

The array declared `unsigned char same[3] = {251, 241, 128}` reads back as −5, −15, −128 the moment you ask for signed decimal. The type in your source code is an instruction to the *compiler*. It is not written down anywhere in the memory itself.

**Two more ways to look, without `memory read`:**

```
(lldb) frame variable
(signed char[3]) nums = "\xfb\xf1\x80"
(unsigned char[3]) same = "\xfb\xf1\x80"
```

`frame variable` on a char array shows the bytes as escaped characters, because C thinks anything called `char` might be text. Not useful for numbers — which is exactly why `memory read` exists.

For a **single** variable, `frame variable -f b` does work:

```
(lldb) frame variable -f b nums[0]
(signed char) nums[0] = 0b11111011
(lldb) expression -f b -- nums[1]
(signed char) $0 = 0b11110001
```

**Takeaway to say out loud:** "`memory read -f b -s 1 -c 3 &arr[0]` shows me the actual bits — and `-f d` and `-f u` are just two ways of reading them."

---

## Iteration 4 — Why *this* rule and not something simpler

There is an obvious way to store a negative number: use the top bit as a minus sign and the other seven for the magnitude. It is called **sign-and-magnitude**, humans invented it first, and computers do not use it. This iteration shows why.

**a. What we set up**

Add −5 and +5 using ordinary binary addition, with no rule for negatives at all — just add the columns as if both were plain unsigned numbers.

```c
/* why.c */
#include <stdio.h>

void bits8(unsigned char v)
{
    for (int i = 7; i >= 0; i--) printf("%d", (v >> i) & 1);
}

int main(void)
{
    unsigned char minus5 = 0xFB;   /* the -5 pattern from Iteration 2 */
    unsigned char plus5  = 5;

    printf("  -5 = "); bits8(minus5); printf("\n");
    printf("   5 = "); bits8(plus5);  printf("\n");
    printf("  sum= "); bits8((unsigned char)(minus5 + plus5)); printf("\n");

    return 0;
}
```

**b. Task**

Add `1111 1011` and `0000 0101` on paper first, column by column, carrying as normal. You will get a **nine-bit** answer. Write down all nine bits, then predict what the program prints — which holds only eight.

**c. Observation (what you should find)**

```
  -5 = 11111011
   5 = 00000101
  sum= 00000000
```

On paper the answer is `1 0000 0000` — nine bits. The byte keeps only the low eight, and the ninth simply **falls off the end**. What is left is `0000 0000`, which is zero, which is the correct answer to −5 + 5.

**Nobody checked any signs.** The CPU added two bit patterns the way it adds any two bit patterns, threw away the overflow, and the right answer appeared. That is the entire reason two's complement exists:

| | Sign-and-magnitude | Two's complement |
|---|---|---|
| Circuits needed | an adder **and** a separate subtractor, plus sign-comparison logic | **one adder** |
| How many zeros | **two** — `0000 0000` (+0) and `1000 0000` (−0) | **one** |
| `a − b` is done by | special-case logic | `a + (two's complement of b)` — still just addition |
| Range in a byte | −127 … +127 (254 usable patterns, 2 wasted on zero) | **−128 … +127** (all 256 used) |

Two wasted patterns and a whole extra circuit, in exchange for being easier for humans to read on paper. Every machine you will ever use made the other choice.

And notice what this gives you for free: **subtraction disappears as a separate operation.** `a - b` is computed as `a + (~b + 1)`. The `~` you learnt in Task 10 is doing the first half of that, inside the CPU, on every subtraction you have ever written.

**Takeaway to say out loud:** "Two's complement means the CPU never needs to subtract — it flips the bits, adds one, and adds."

---

## Iteration 5 — The edges: wrapping around

**a. What we set up**

```c
/* edges.c */
#include <stdio.h>

int main(void)
{
    signed char   a = 127;
    signed char   b = -128;
    unsigned char u = 255;

    printf("signed   127 + 1 = %d\n", (signed char)(a + 1));
    printf("signed  -128 - 1 = %d\n", (signed char)(b - 1));
    printf("unsigned 255 + 1 = %u\n", (unsigned char)(u + 1));
    printf("unsigned   0 - 1 = %u\n", (unsigned char)(0 - 1));

    return 0;
}
```

**b. Task**

Predict all four. Do not reason about what "should" happen — work out the bit patterns and let them tell you.

**c. Observation (what you should find)**

```
signed   127 + 1 = -128
signed  -128 - 1 = 127
unsigned 255 + 1 = 0
unsigned   0 - 1 = 255
```

Every one of them **wrapped around**. Follow the bits and there is no mystery:

```
127        0111 1111
+ 1
-128       1000 0000     ← adding one to the largest positive lands on the sign bit
```

```
255        1111 1111
+ 1
0          0000 0000     ← the ninth bit falls off, as in Iteration 4
```

Picture the 256 patterns arranged in a **circle** rather than a line. Counting up from 255 brings you back to 0; counting up from 127 brings you to −128. There is no edge to fall off, only a point where the numbering restarts.

This is worth taking seriously, because it is a real and famous source of bugs. A counter stored in a `signed char` that reaches 127 does not stop or complain — it silently becomes −128, and any code that assumed it only ever grew is now wrong. Nothing crashes. **Same lesson as Task 31: the wrong answer that does not crash is the dangerous one.**

*(One honest note: unsigned wrap-around is defined by the C standard and always behaves exactly like this. Signed overflow is officially "undefined behaviour", which means the standard permits a compiler to assume it never happens and optimise accordingly. In practice, at `-O0` on your machine, you will see the wrap shown above — and you should still never write code that relies on it.)*

**Takeaway to say out loud:** "The 256 patterns are a circle, not a line — 127 + 1 is −128 and 255 + 1 is 0."

---

## Iteration 6 — Bitwise and logical: two families, easily confused

Task 10 taught you the bitwise operators. Here they are as a **recap**, followed by the family you have not met — and then the mistake that costs beginners the most time.

**a. What we set up**

**The six bitwise operators — recap from Task 10.** They work on **every bit** of the value, independently.

| Operator | Name | What it does | Example | Example |
|---|---|---|---|---|
| `&` | AND | 1 only where **both** bits are 1 | `12 & 10` = **8** | `5 & 3` = **1** |
| `\|` | OR | 1 where **either** bit is 1 | `12 \| 10` = **14** | `5 \| 3` = **7** |
| `^` | XOR | 1 where the bits **differ** | `12 ^ 10` = **6** | `5 ^ 3` = **6** |
| `~` | NOT | flips **every** bit | `~12` = **−13** | `~0` = **−1** |
| `<<` | left shift | bits march left; ×2 each place | `12 << 2` = **48** | `1 << 5` = **32** |
| `>>` | right shift | bits march right; ÷2 each place | `12 >> 2` = **3** | `40 >> 3` = **5** |

Check one by hand: `12` is `1100`, `10` is `1010`. AND them column by column → `1000` = 8. ✓

**The three logical operators — new today.** They do not look at individual bits at all. They treat the *whole value* as a yes/no: **zero means false, anything else means true**, and the answer is always exactly `0` or `1`.

| Operator | Name | What it does | Example | Example |
|---|---|---|---|---|
| `&&` | AND | 1 if **both** sides are non-zero | `12 && 10` = **1** | `0 && 5` = **0** |
| `\|\|` | OR | 1 if **either** side is non-zero | `12 \|\| 10` = **1** | `0 \|\| 0` = **0** |
| `!` | NOT | 1 if the value is zero, else 0 | `!12` = **0** | `!0` = **1** |

**b. Task**

Predict these four lines, then run them:

```c
/* trap.c */
#include <stdio.h>

int main(void)
{
    int c = 5, d = 3, e = 4, f = 2;

    printf("%d &  %d = %d      %d && %d = %d\n", c, d, c & d, c, d, c && d);
    printf("%d &  %d = %d      %d && %d = %d\n", e, f, e & f, e, f, e && f);
    printf("~1 = %d       !1 = %d\n", ~1, !1);

    return 0;
}
```

**c. Observation (what you should find)**

```
5 &  3 = 1      5 && 3 = 1
4 &  2 = 0      4 && 2 = 1
~1 = -2       !1 = 0
```

Look carefully at those first two lines, because this is the trap.

**`5 & 3` and `5 && 3` both gave 1** — and for completely unrelated reasons. `5 & 3` is `101 & 011` = `001` = 1, an accident of these particular bits. `5 && 3` is 1 because both operands are non-zero. **They agree by luck.**

**`4 & 2` and `4 && 2` disagree.** `100 & 010` = `000` = 0, so the bitwise answer is 0 — false. But both 4 and 2 are non-zero, so the logical answer is 1 — true. **The same two numbers, opposite answers.**

This is why the bug is so hard to find. Write `&` where you meant `&&` and your program will often work, on the values you happened to test, right up until it doesn't.

| | `&` `\|` `~` | `&&` `\|\|` `!` |
|---|---|---|
| Works on | every bit separately | the whole value, as true/false |
| Result | a full number, any value | only `0` or `1` |
| Use it for | masks, flags, packing bits | conditions in `if` and `while` |
| `~1` / `!1` | `~1` = −2 | `!1` = 0 |

**A rule of thumb:** if it is going inside `if (...)` or `while (...)`, you almost certainly want the doubled one, `&&` or `||`. If you are manipulating bits, you want the single one.

**One more difference, and it matters.** The logical operators **short-circuit**: in `a && b`, if `a` is false, `b` is *never evaluated at all*, because the answer is already known. Same for `a || b` when `a` is true. The bitwise operators always evaluate both sides. This is why `if (n != 0 && 100 / n > 2)` is safe and swapping in `&` would divide by zero.

*(A friendly warning: your compiler is watching. Write `&&` between two constants and clang says `warning: use of logical '&&' with constant operand ... use '&' for a bitwise operation`. When `-Wall` tells you something, read it — Task 13's habit applies here too.)*

**Takeaway to say out loud:** "`&` works on bits and gives a number; `&&` works on true/false and gives 0 or 1. `4 & 2` is 0 but `4 && 2` is 1."

---

## Iteration 7 — One gate builds them all

Underneath every operator in Iteration 6 there is a circuit, and underneath those circuits there is essentially **one** kind of gate.

**a. What we set up**

A **NAND** gate is AND followed by NOT — "not both". Its truth table:

| a | b | a AND b | **a NAND b** |
|---|---|---|---|
| 0 | 0 | 0 | **1** |
| 0 | 1 | 0 | **1** |
| 1 | 0 | 0 | **1** |
| 1 | 1 | 1 | **0** |

Only `1 1` gives 0. Everything else gives 1.

NAND has a remarkable property: it is **functionally complete**. Every other logic gate can be built from NAND gates alone — and therefore so can every circuit in a computer.

```c
/* gates.c */
#include <stdio.h>

int NAND(int a, int b) { return !(a && b); }

int NOT(int a)         { return NAND(a, a); }
int AND(int a, int b)  { return NOT(NAND(a, b)); }
int OR(int a, int b)   { return NAND(NOT(a), NOT(b)); }
int XOR(int a, int b)  { return AND(OR(a, b), NAND(a, b)); }

int main(void)
{
    printf("a b | NAND NOTa AND OR XOR\n");
    for (int a = 0; a <= 1; a++)
        for (int b = 0; b <= 1; b++)
            printf("%d %d |  %d    %d    %d   %d   %d\n",
                   a, b, NAND(a, b), NOT(a), AND(a, b), OR(a, b), XOR(a, b));
    return 0;
}
```

**b. Task**

Before running: work out `NOT` on paper. `NOT(a)` is `NAND(a, a)` — check both cases, `a = 0` and `a = 1`, against the truth table above. Then predict the whole output grid.

**c. Observation (what you should find)**

```
a b | NAND NOTa AND OR XOR
0 0 |  1    1    0   0   0
0 1 |  1    1    0   1   1
1 0 |  1    0    0   1   1
1 1 |  0    0    1   1   0
```

Every column is correct, and **only `NAND` contains real logic** — the other four functions call nothing but NAND.

Read how each is built:

- **`NOT(a) = NAND(a, a)`** — feed the same signal to both inputs. If `a` is 1, "not both" is 0. If `a` is 0, "not both" is 1. That is inversion.
- **`AND(a, b) = NOT(NAND(a, b))`** — NAND is already "not AND", so invert it back.
- **`OR(a, b) = NAND(NOT(a), NOT(b))`** — "not (both are false)" is the same as "at least one is true". This is De Morgan's law, and you have just used it.
- **`XOR`** — true when they differ: true when at least one is set (`OR`) *and* not both are set (`NAND`).

**Why is this more than a curiosity?** Because a NAND gate is easy and cheap to make out of a handful of transistors, so a chip factory can build one thing very well and repeat it billions of times. The processor in your machine is, in a real sense, an enormous pile of NAND gates arranged carefully.

### nand2tetris

There is a well-known free course built entirely on this idea: **[nand2tetris.org](https://nand2tetris.org)** — *"From NAND to Tetris"*. It starts you with a single NAND gate and, over twelve projects, has you build:

1. the basic gates — NOT, AND, OR, XOR — from NAND, exactly as above;
2. an adder, then an **ALU** — the part that does arithmetic;
3. memory: a flip-flop, a register, then RAM;
4. a **CPU** wired from those parts, and a working computer;
5. an assembler, a virtual machine, a compiler, and a small operating system;
6. finally, a game running on the machine you built.

Nothing is hidden and nothing is taken on trust — at every stage you have personally built everything underneath. If today's iteration was interesting, that course is the natural place to follow it, and it is free.

**Takeaway to say out loud:** "NAND alone builds NOT, AND, OR and XOR — and a whole computer is made of little else."

---

## Iteration 8 — A bitmap mask: the same operators, on a photograph

Task 10 showed masks on flags and permissions. Here is the same idea at image scale, and it is how background replacement actually works.

**a. What we set up**

You have a photo with a person in it, and you want to keep the person and replace the background. First you need to record **which pixels are the person**. There are two ways.

**Way 1 — a list of coordinates.** Store the `(i, j)` of every pixel belonging to the person. For a 1920 × 1080 photo, with two 2-byte numbers per pixel:

```
image             1920 × 1080  = 2,073,600 pixels
person occupies   about a third =   691,200 pixels
storage           691,200 × 4 bytes = 2,764,800 bytes ≈ 2.64 MB
```

**Way 2 — a bitmap.** Store **one bit per pixel** for the whole image: `1` = person, `0` = background. No coordinates at all — a pixel's position *is* its position in the bitmap.

```
2,073,600 bits ÷ 8 = 259,200 bytes ≈ 0.25 MB
```

**Roughly ten times smaller** — and the gap widens as the subject gets bigger, because the bitmap's size never changes. If the person filled the entire frame, the list would need 8.3 MB and the bitmap would still be 259,200 bytes: **32 times smaller**.

There is a second advantage, and it matters more. A coordinate list must be *searched* to answer "is pixel (700, 400) part of the person?". A bitmap answers it with one shift and one `&`.

**b. Task**

Here is one row of eight pixels, each stored as one bit, so the whole row is a single byte. Work out on paper what `(photo & mask) | (beach & ~mask)` gives, before running it.

```c
/* mask.c */
#include <stdio.h>

void bits8(const char *label, unsigned char v)
{
    printf("%-22s ", label);
    for (int i = 7; i >= 0; i--) printf("%c", ((v >> i) & 1) ? '#' : '.');
    printf("   0x%02X\n", v);
}

int main(void)
{
    unsigned char photo = 0xF0;   /* one row of the original photo  */
    unsigned char beach = 0x99;   /* the same row of a new backdrop */
    unsigned char mask  = 0x3C;   /* 1 = person is here             */

    bits8("photo", photo);
    bits8("beach", beach);
    bits8("mask (1 = person)", mask);
    bits8("~mask (1 = bg)", (unsigned char)~mask);

    printf("\n");
    bits8("photo & mask", photo & mask);
    bits8("beach & ~mask", beach & (unsigned char)~mask);
    bits8("RESULT", (photo & mask) | (beach & (unsigned char)~mask));

    return 0;
}
```

**c. Observation (what you should find)**

```
photo                  ####....   0xF0
beach                  #..##..#   0x99
mask (1 = person)      ..####..   0x3C
~mask (1 = bg)         ##....##   0xC3

photo & mask           ..##....   0x30
beach & ~mask          #......#   0x81
RESULT                 #.##...#   0xB1
```

Read it as a picture. `#` is a lit pixel and `.` is dark.

**`photo & mask`** keeps the photo only where the mask is 1 — the person is cut out, and everywhere else goes to zero. This is the **cut-out**.

**`beach & ~mask`** does the opposite. Inverting the mask turns "where the person is" into "where the background is", and ANDing keeps the new backdrop only there — with a person-shaped hole punched in it.

**`|` joins them.** One image has content only where the other is empty, so OR drops them together without either overwriting the other. The result is the person from the original photo standing on the new background.

The whole operation is one line:

```c
result = (photo & mask) | (beach & ~mask);
```

Three operators from Task 10, and that is background replacement. A real photograph does the identical thing with 8 bits per colour channel instead of 1 bit per pixel, over millions of pixels — but the expression is exactly the one above, and on a GPU it runs on thousands of pixels at once.

Everything you can do with a mask follows from these three:

| Goal | Expression |
|---|---|
| keep only the masked part | `image & mask` |
| keep only the unmasked part | `image & ~mask` |
| combine two masked halves | `a \| b` |
| invert which part is selected | `~mask` |
| is pixel *n* of this row set? | `(row >> n) & 1` |

**Takeaway to say out loud:** "A mask is one bit per pixel — `(photo & mask) | (background & ~mask)` swaps the backdrop in one line."

---

## Iteration 9 — From one byte to N

**a. What we set up**

Everything so far used one byte. The rule generalises with no changes at all.

For **N bytes**, that is `8 × N` bits, so `2^(8N)` different patterns:

- **unsigned:** `0` … `2^(8N) − 1`
- **signed:** `−2^(8N−1)` … `+2^(8N−1) − 1`

Check the second line against what you already know. For one byte, N = 1, so `8N − 1` = 7: from `−2^7` to `+2^7 − 1`, which is **−128 to +127**. Correct.

**b. Task**

Fill this in for 2, 4 and 8 bytes before running anything.

| Bytes | Bits | Unsigned max | Signed min | Signed max |
|---|---|---|---|---|
| 1 | 8 | 255 | −128 | 127 |
| 2 | 16 | ? | ? | ? |
| 4 | 32 | ? | ? | ? |
| 8 | 64 | ? | ? | ? |

```c
/* ranges.c */
#include <stdio.h>
#include <limits.h>
#include <stdint.h>

int main(void)
{
    printf("1 byte : %u  %d  %d\n", (unsigned)UCHAR_MAX, SCHAR_MIN, SCHAR_MAX);
    printf("2 bytes: %u  %d  %d\n", (unsigned)USHRT_MAX, SHRT_MIN, SHRT_MAX);
    printf("4 bytes: %u  %d  %d\n", UINT_MAX, INT_MIN, INT_MAX);
    printf("8 bytes: %llu  %lld  %lld\n",
           (unsigned long long)UINT64_MAX, (long long)INT64_MIN, (long long)INT64_MAX);
    printf("\n256^4 = %llu\n", (unsigned long long)256 * 256 * 256 * 256);
    return 0;
}
```

**c. Observation (what you should find)**

```
1 byte : 255  -128  127
2 bytes: 65535  -32768  32767
4 bytes: 4294967295  -2147483648  2147483647
8 bytes: 18446744073709551615  -9223372036854775808  9223372036854775807

256^4 = 4294967296
```

| Bytes | Bits | Unsigned max | Signed min | Signed max | Where you meet it |
|---|---|---|---|---|---|
| 1 | 8 | 255 | −128 | 127 | `char`, a colour channel, one pixel of a mask |
| 2 | 16 | 65,535 | −32,768 | 32,767 | `short`, a TCP port number |
| 4 | 32 | 4,294,967,295 | −2,147,483,648 | 2,147,483,647 | `int`, **an IPv4 address** |
| 8 | 64 | 18,446,744,073,709,551,615 | −9,223,372,036,854,775,808 | 9,223,372,036,854,775,807 | `long`, a memory address |

Two of those are worth stopping on.

**The 4-byte signed maximum, 2,147,483,647**, is a number you will meet outside programming. It is why some older systems break in the year 2038 — they count seconds since 1970 in a signed 32-bit integer, and that counter reaches its maximum on 19 January 2038, then wraps to −2,147,483,648 exactly as Iteration 5 showed. The fix is to count in 64 bits, which lasts about 292 billion years.

**And the last line.** An **IPv4 address** is four bytes — you have seen them written as four numbers separated by dots, each in the range 0 to 255, because each one *is* a byte:

```
192 . 168 .   1 .  17
```

So the total number of possible IPv4 addresses is:

```
256 × 256 × 256 × 256 = 256^4 = 2^32 = 4,294,967,296
```

About four billion — for a planet with more than eight billion people and many more devices than people. Not all four billion are even usable: large blocks are reserved for private networks, for loopback, for multicast and for other purposes.

**How those four billion are divided up, why your laptop's address starts with `192.168` and is not on the internet at all, and how a reply finds its way back to *your* machine — that is Task 37.**

**Takeaway to say out loud:** "N bytes hold 2^(8N) patterns — and an IPv4 address is just four bytes, so there are 2^32 of them."

---

## Practice — Predict the answer

Write every answer in your notebook **before** checking. Work the bits, do not guess.

**P1.** Write −1 as an eight-bit two's complement pattern. What is that byte as an unsigned number?

**P2.** Write −64 as an eight-bit pattern, in binary and in hex.

**P3.** The byte `1111 0001` is in memory. What is it as `unsigned char`? As `signed char`?

**P4.** The byte `0x7F` is in memory. As `signed char`, what is it — and what happens if you add 1?

**P5.** Why is there no `+128` in a `signed char`, when there *is* a −128?

**P6.** `signed char x = 100; x = x + 100;` What does `printf("%d", x)` show?

**P7.** `unsigned char u = 3; u = u - 10;` What does `printf("%u", u)` show?

**P8.** Which LLDB command shows three bytes starting at `&arr[0]` in binary?

**P9.** You run `memory read -f d -s 1 -c 1 &b` and see `-16`. What would `-f u` show for the same byte? What would `-f x` show?

**P10.** `6 & 3` and `6 && 3` — give both answers and explain why they differ.

**P11.** `0 & 5`, `0 && 5`, `~0`, `!0` — give all four.

**P12.** `int n = 0;` Why is `if (n != 0 && 10 / n > 1)` safe, but the same line with `&` is not?

**P13.** Using only NAND, write `NOT(a)`. Then check it for `a = 0` and `a = 1`.

**P14.** Fill in the NAND truth table from memory. Which single row gives 0?

**P15.** `photo = 1111 0000`, `mask = 1100 1100`. Work out `photo & mask`, `photo & ~mask`, and `(photo & mask) | (0000 1111 & ~mask)`.

**P16.** A 640 × 480 image. How many bytes does a one-bit-per-pixel mask need?

**P17.** How many different values fit in 2 bytes? What is the signed range?

**P18.** An IPv4 address is 4 bytes. How many addresses are possible, and what power of 2 is that?

---

### Self-check

**Cover this until every answer is written down.**

**P1** — `1111 1111`. As unsigned, **255**. (Check: invert `0000 0001` → `1111 1110`, add 1 → `1111 1111`.) Worth memorising: all-ones is −1 for any signed size.

**P2** — 64 is `0100 0000`; invert → `1011 1111`; add 1 → `1100 0000` = **`0xC0`**.

**P3** — Unsigned **241**. Signed **−15** — invert `1111 0001` → `0000 1110`, add 1 → `0000 1111` = 15, so −15. This is `num2` from Iteration 3.

**P4** — `0x7F` is `0111 1111` = **127**, the largest `signed char`. Adding 1 gives `1000 0000` = **−128**. It wraps.

**P5** — Because the 256 patterns split as 128 negative, 1 zero, 127 positive. Zero uses up one of the "positive-looking" patterns, so the positive side is one short. −128 is the negative with no positive twin — and it is its own two's complement.

**P6** — **−56**. 200 does not fit: `200` is `1100 1000`, which as a signed byte is −56. (Check: invert → `0011 0111`, +1 → `0011 1000` = 56.)

**P7** — **249**. 3 − 10 = −7, and −7 as a byte is `1111 1001` = 249. Unsigned values wrap round the circle; they never go negative.

**P8** — `memory read -f b -s 1 -c 3 &arr[0]` — format binary, size 1 byte, count 3.

**P9** — `-f u` shows **240**, `-f x` shows **0xf0**. (−16: invert `0001 0000` → `1110 1111`, +1 → `1111 0000` = 240 = 0xF0.) One byte, three readings.

**P10** — `6 & 3` = **2**: `110 & 011` = `010`. `6 && 3` = **1**: both are non-zero, so true. `&` combines bits and yields a number; `&&` asks a yes/no question and yields 0 or 1.

**P11** — `0 & 5` = **0**. `0 && 5` = **0**. `~0` = **−1** (all bits flipped to ones). `!0` = **1** (zero is false, so "not false" is true). Note `~0` and `!0` differ completely.

**P12** — `&&` **short-circuits**: if `n != 0` is false, the right-hand side is never evaluated, so the division never happens. `&` always evaluates both sides, so `10 / n` runs and divides by zero.

**P13** — `NOT(a) = NAND(a, a)`. For `a = 0`: NAND(0,0) = 1 ✓. For `a = 1`: NAND(1,1) = 0 ✓.

**P14** — 1, 1, 1, 0 going down. Only the row **`a = 1, b = 1`** gives 0 — "not both".

**P15** — `photo & mask` = `1111 0000 & 1100 1100` = **`1100 0000`**. `~mask` = `0011 0011`, so `photo & ~mask` = **`0011 0000`**. And `0000 1111 & 0011 0011` = `0000 0011`, so the combination is `1100 0000 | 0000 0011` = **`1100 0011`**.

**P16** — 640 × 480 = 307,200 pixels = 307,200 bits ÷ 8 = **38,400 bytes** (about 37.5 KB).

**P17** — 2 bytes = 16 bits = **65,536** values. Unsigned 0…65,535; signed **−32,768 … +32,767**.

**P18** — 4 bytes = 32 bits, so `256^4` = **2^32** = **4,294,967,296**.

---

## One-page reference

**One byte, two agreements**

| Type | Bits | Range | All-ones byte means |
|---|---|---|---|
| `unsigned char` | 8 | 0 … 255 | 255 |
| `signed char` | 8 | −128 … +127 | −1 |

Same 256 patterns either way. The type is an instruction to the compiler; it is not stored in memory.

**Two's complement — the whole rule**

```
to write −n :  take n, invert every bit, add 1
to read it back:  invert every bit, add 1   (same two steps)
```

| Value | Bits | Hex | As unsigned |
|---|---|---|---|
| −1 | `1111 1111` | `0xFF` | 255 |
| −5 | `1111 1011` | `0xFB` | 251 |
| −15 | `1111 0001` | `0xF1` | 241 |
| −128 | `1000 0000` | `0x80` | 128 |

Top bit set = negative. −128 is its own two's complement. Wrapping: `127 + 1` → −128; `255 + 1` → 0; `0 − 1` → 255.

**LLDB — looking at bytes**

| Command | Shows |
|---|---|
| `memory read -f b -s 1 -c 3 &arr[0]` | 3 bytes, **binary** |
| `memory read -f x -s 1 -c 3 &arr[0]` | the same bytes in **hex** |
| `memory read -f d -s 1 -c 3 &arr[0]` | as **signed** decimal |
| `memory read -f u -s 1 -c 3 &arr[0]` | as **unsigned** decimal |
| `frame variable -f b x` | one variable in binary |
| `expression -f b -- x` | evaluate and show in binary |

`-f` format · `-s` size in bytes · `-c` count. Use `&arr[0]`, not `arr` — `memory read` wants an address.

**The two operator families**

| | Bitwise `& \| ^ ~ << >>` | Logical `&& \|\| !` |
|---|---|---|
| Operates on | every bit separately | the whole value as true/false |
| Result | any number | only `0` or `1` |
| Evaluates both sides? | always | **no** — short-circuits |
| Use in | masks, flags, packing | `if`, `while` conditions |

`4 & 2` = 0 but `4 && 2` = 1. `~1` = −2 but `!1` = 0.

**Gates from NAND**

| Built | From |
|---|---|
| `NOT(a)` | `NAND(a, a)` |
| `AND(a,b)` | `NOT(NAND(a, b))` |
| `OR(a,b)` | `NAND(NOT(a), NOT(b))` |
| `XOR(a,b)` | `AND(OR(a,b), NAND(a,b))` |

NAND gives 0 only for `1 1`. See **nand2tetris.org** to build a whole computer from it.

**Masks**

```c
result = (photo & mask) | (background & ~mask);
```

`image & mask` keeps the selected part · `image & ~mask` keeps the rest · `|` joins them · `(row >> n) & 1` tests one bit.

**N bytes**

| Bytes | Unsigned max | Signed range |
|---|---|---|
| 1 | 255 | −128 … 127 |
| 2 | 65,535 | −32,768 … 32,767 |
| 4 | 4,294,967,295 | −2,147,483,648 … 2,147,483,647 |
| 8 | 18,446,744,073,709,551,615 | ±9.22 × 10^18 |

Unsigned `0 … 2^(8N) − 1` · signed `−2^(8N−1) … 2^(8N−1) − 1`. An IPv4 address is 4 bytes → **2^32 = 4,294,967,296**.

**Rules to keep:**
- The bits do not know their type. `-f d` and `-f u` read one byte two ways.
- Invert and add one — both to write a negative and to read it back.
- −128 has no positive twin and is its own two's complement.
- The 256 patterns are a circle: they wrap, silently, without crashing.
- Two's complement exists so the CPU needs only one adder and has only one zero.
- `&` is bits, `&&` is true/false. In an `if`, you almost always want the doubled one.
- `&&` and `||` short-circuit; `&` and `|` do not.
- NAND alone builds every other gate.
- A one-bit-per-pixel mask is far smaller than a list of coordinates, and answers "is this pixel set?" instantly.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| signed | సంజ్ఞ ఉన్న | ఋణ సంఖ్యలను కూడా నిల్వ చేయగలది (−128…127) |
| unsigned | సంజ్ఞ లేని | ధన సంఖ్యలు మాత్రమే (0…255) |
| magnitude | పరిమాణం | సంజ్ఞ లేకుండా సంఖ్య విలువ |
| sign bit | సంజ్ఞ బిట్ | ఎడమవైపు చివరి బిట్ — 1 అంటే ఋణం |
| one's complement | ఒకటి పూరకం | ప్రతి బిట్‌ను తిప్పడం |
| two's complement | రెండు పూరకం | బిట్‌లు తిప్పి, ఒకటి కలపడం |
| wrap around | చుట్టూ తిరగడం | గరిష్ఠం దాటితే మొదటికి రావడం (127 + 1 = −128) |
| overflow | పొర్లిపోవడం | విలువ ఆ బైట్‌లో పట్టకపోవడం |
| bitwise | బిట్‌ల వారీగా | ప్రతి బిట్‌పై విడిగా పనిచేసేది (`&`, `\|`) |
| logical | తార్కికం | నిజం/అబద్ధంపై పనిచేసేది (`&&`, `\|\|`) |
| short-circuit | ముందే ఆగిపోవడం | మొదటి భాగంతోనే జవాబు తేలితే రెండోది చూడకపోవడం |
| gate | గేట్ | బిట్‌లపై పనిచేసే చిన్న వలయం |
| NAND | నాండ్ | "రెండూ కాదు" — రెండూ 1 అయితేనే 0 |
| functionally complete | పూర్తి సామర్థ్యం | దీని ఒక్కదానితోనే మిగతా అన్నీ కట్టవచ్చు |
| truth table | సత్య పట్టిక | అన్ని ఇన్‌పుట్‌లకూ ఫలితాల పట్టిక |
| mask | మాస్క్ | ఏ బిట్‌లు కావాలో ఎంచుకునే విలువ |
| bitmap | బిట్‌మ్యాప్ | ప్రతి పిక్సెల్‌కు ఒక బిట్ |
| format (`-f`) | ఫార్మాట్ | LLDB విలువను ఎలా చూపాలో చెప్పేది |
