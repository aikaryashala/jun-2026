# Signed Bytes, Two's Complement, and Gates — Answer Key

Check the **reasoning**. On this sheet especially, an answer you got by recognising the number rather than working the bits will not survive the next question.

Everything here comes from five ideas: one byte is 256 patterns and the **type only chooses how to read them**; a negative is written by **inverting and adding one**; the patterns are a **circle**, so they wrap silently; `&` is bits and `&&` is true/false; and **NAND** alone builds every gate.

---

# Part A — Multiple Choice

**A1 — B) 1 and 1.**
Both are one byte. There is no extra room anywhere for a sign, which is the whole reason two's complement has to fit the sign *inside* the eight bits.

**A2 — C) 256.**
8 bits, each 0 or 1, so 2^8 = 256. Note 255 (option B) is the largest *unsigned value*, not the number of patterns — the count includes zero.

**A3 — B) one pattern is used for zero.**
The 256 patterns split as 128 negative + 1 zero + 127 positive. Zero occupies one of the patterns that would otherwise be positive, so the positive side ends one short. The sign bit is not "wasted" (option A) — every pattern is used.

**A4 — C) 251.**
`1111 1011` = 128+64+32+16+8+0+2+1 = 251.

**A5 — A) −5.**
The same byte, read the other way. Invert `1111 1011` → `0000 0100`, add 1 → `0000 0101` = 5, so the value is −5. A4 and A5 together are the point of the whole worksheet: **one byte, two readings.**

**A6 — B) invert every bit, then add one.**
Order matters. Option C — add one first, then invert — gives a different (and wrong) answer.

**A7 — C) −128.**
`1000 0000` → invert → `0111 1111` → add 1 → `1000 0000`, back where it started. It is the negative with no positive twin. (Zero is also technically unchanged by the procedure, but −128 is the one that matters and the one asked about.)

**A8 — D) −128.**
`0111 1111` + 1 = `1000 0000`, which as a signed byte is −128. The largest positive wraps straight to the most negative.

**A9 — C) 255.**
`0000 0000` − 1 borrows all the way and gives `1111 1111` = 255. Unsigned values wrap round the circle; they never become negative. Nothing crashes.

**A10 — B) subtraction becomes ordinary addition, so one adder circuit does both.**
`a − b` is computed as `a + (~b + 1)`. Sign-and-magnitude would need a separate subtractor plus sign-comparison logic, and it wastes a pattern on a second zero. Option A is true of sign-and-magnitude and is exactly why it *lost*.

**A11 — B) `memory read -f b -s 1 -c 3 &arr[0]`.**
`-f b` is binary. Option A is hex; option D asks for one 3-byte item, not three 1-byte items.

**A12 — B) show 4 items.**
`-c` is the **count** of items; `-s` is the size of each. Together they say "four items of one byte each".

**A13 — B) −56.**
200 is `1100 1000`. The top bit is set, so as signed it is negative: invert → `0011 0111`, add 1 → `0011 1000` = 56, giving −56. Same byte, two readings.

**A14 — B) 0 and 1.**
`4 & 2` is `100 & 010` = `000` = 0 — no bit is set in both. `4 && 2` is 1 because both operands are non-zero. **The same two numbers, opposite answers** — this is the trap from Iteration 6.

**A15 — C) 0.**
`!` is logical NOT: 5 is non-zero, therefore true, therefore `!5` is false, which is 0.

**A16 — A) −6.**
`~` flips every bit. For a 32-bit int, `~5` gives the pattern for −6. The shortcut worth knowing: `~n` is always `−n − 1`. Compare with A15 — `~5` and `!5` have nothing in common.

**A17 — B) `&&` and `||`.**
If the left side already decides the answer, the right side is never evaluated. This is what makes `if (n != 0 && 10 / n > 1)` safe.

**A18 — C) `1 1` only.**
NAND is "not both". Every other combination gives 1.

**A19 — C) `NAND(a, a)`.**
Feeding the same signal to both inputs: if `a` is 1, "not both" is 0; if `a` is 0, "not both" is 1. That is inversion.

**A20 — B) every other gate can be built from NAND alone.**
Which is why a chip factory can perfect one gate and repeat it billions of times, and why nand2tetris can start a whole computer from it.

**A21 — B) `image & mask`.**
AND keeps a bit only where the mask has 1 and zeroes everything else. Option D — `image & ~mask` — keeps the opposite part, which is what you use for the background half.

**A22 — B) a pixel's position is where it sits in the bitmap, so no coordinates are stored.**
The position is implied by the ordering, so nothing needs to be written down for it. A coordinate list pays 4 bytes per selected pixel; the bitmap pays 1 bit per pixel of the whole image, no matter how many are selected.

**A23 — C) 65,536.**
2 bytes = 16 bits = 2^16.

**A24 — C) 2^32.**
4 bytes = 32 bits, so 2^32 = 4,294,967,296 — which is also 256^4.

---

# Part B — Fill in the Blanks

**B1** — **8** bits; **256** patterns.

**B2** — unsigned **0** to **255**; signed **−128** to **+127**.

**B3** — **invert** every bit, then **add one**.

**B4** — the **one's** complement.

**B5** — the **sign** bit; 1 means **negative**.

**B6** — **−128**.

**B7** — `-f` the **format**, `-s` the **size** in bytes per item, `-c` the **count** of items.

**B8** — **`b`** binary, **`x`** hex, **`d`** signed decimal, **`u`** unsigned decimal.

**B9** — **bitwise**; **logical**.

**B10** — **0** or **1**.

**B11** — both inputs are **1**.

**B12** — **functionally complete**.

**B13** — `~mask`.

**B14** — unsigned 0 to **2^(8N) − 1**; signed **−2^(8N−1)** to **2^(8N−1) − 1**.

---

# Part C — Scenario Questions

### C1 — the same bits under two names

**(a)** Both print `0b11111011`. Identical.

**(b)** The type tells the **compiler** how to generate instructions and how `printf` should display the value. It does not change what gets written to memory. −5 and 251 are the same eight bits, so a byte declared either way holds exactly the same thing.

**(c)** No, they are wrong, and the evidence is in front of them: if the type were stored alongside the value, the two bytes would differ somehow — and they do not. A `signed char` and an `unsigned char` occupy one byte each (`sizeof` says so), with no room for anything else. The type exists only at compile time; by the time the program runs there is nothing left of it but the instructions the compiler chose. `memory read -f d` versus `-f u` proves the point from the other direction — *you* supply the interpretation when you look.

### C2 — building −20

**(a)** 20 = `0001 0100`.

**(b)**
```
  20        0001 0100
  invert    1110 1011
  add 1     1110 1100   = 0xEC   = −20
```

**(c)** Applying the two steps again: invert `1110 1100` → `0001 0011`, add 1 → `0001 0100` = 20. You get the magnitude back. **The procedure is its own inverse** — the same two steps both write a negative and read one — which is one of the practical reasons the scheme is convenient.

### C3 — the sign bit is not a minus sign

**(a)** `1111 1011` is **−5**.

**(b)** They treated the byte as "1 bit of sign + 7 bits of magnitude", reading `111 1011` as the number 123. But in two's complement the remaining seven bits are **not** the magnitude. The whole eight-bit pattern is one number, and the only way to read a negative one is to undo the transformation: invert and add one. The top bit does reliably *tell* you the sign — that part of their intuition is right — but it does not let you read off the rest.

**(c)** **Sign-and-magnitude** would have made them exactly right — that representation really is one sign bit plus a magnitude. It is not used because it needs a separate subtractor circuit, and because it has two zeros (`0000 0000` and `1000 0000`), wasting a pattern and forcing every comparison to special-case them.

### C4 — the counter that goes backwards

**(a)** 127.

**(b)** **−128.** `0111 1111` + 1 = `1000 0000`.

**(c)** Any code relying on `count` only increasing is now broken — a test like `if (count > threshold)` becomes false, a loop that waits for the count to reach a target may never finish, a stored maximum stops updating. **No error appears** because nothing invalid happened at the hardware level: the adder added, the ninth bit fell off, and a perfectly legal byte remains. The type system cannot help, because −128 is a completely valid `signed char`. This is the Task 31 lesson again — the failure that prints a plausible wrong answer is worse than a crash.

### C5 — three commands, one byte

**(a)** No. Nothing between the commands writes to memory; all three are read-only.

**(b)** 144 in binary is `1001 0000`. In hex, `1001 0000` is `0x90` ✓. Read as signed, the top bit is set so it is negative: invert `1001 0000` → `0110 1111`, add 1 → `0111 0000` = 112, giving **−112** ✓. All three descriptions are the same eight bits.

**(c)** **None of them, and all of them.** The byte holds a bit pattern; "the real value" depends entirely on the agreement you apply. If the variable was declared `signed char`, then −112 is the reading the program will act on — but that is a fact about the program's *type declaration*, not about the memory. The memory itself is just `1001 0000`.

### C6 — `& 4` versus `&& 4`

**(a)** `flags = 4`: `4 & 4` = 4, which is non-zero and therefore true. `4 && 4` = 1, also true. **Both conditions fire.**

**(b)** `flags = 2`: `2 & 4` is `010 & 100` = `000` = 0, so **false**. `2 && 4` = 1, since both are non-zero, so **true**. **Opposite behaviour.**

**(c)** The second student is right — the two are not the same. `flags & 4` asks "is bit 2 set?", a genuine question about the bits. `flags && 4` asks "is `flags` non-zero at all?", which is true for *any* flag being set and is almost never what a flag test means. Testing only with `flags = 4` is dangerous precisely because that is the one value where both agree; the bug hides until some other flag combination arrives. The general lesson: **choose test values where the two candidate behaviours differ.**

### C7 — `&&` versus `&` in a guard

**(a)** With `&&`, `count != 0` is false, so the right side is **never evaluated** and the condition is simply false — safe. With `&`, **both** sides are evaluated first, so `total / count` runs with `count` as 0 and the program divides by zero.

**(b)** **Short-circuit evaluation.** `&&` stops as soon as the answer is determined. It is not an optimisation you can take or leave — a great deal of real C depends on it for safety, and this guard-then-use pattern is the commonest example.

**(c)** When you genuinely want to combine **bits** rather than conditions: `permissions & REQUIRED_MASK` to check several flag bits at once, `colour & 0xFF` to extract a channel, `x & 1` to test oddness. The rule of thumb holds — inside `if (...)` you almost always want `&&`; when manipulating bits you want `&`.

### C8 — building gates from NAND

**(a)** `AND(a, b) = NOT(NAND(a, b))`, and `NOT` is itself `NAND(x, x)`, so it is `NAND(NAND(a,b), NAND(a,b))` — two NANDs. It needs two because NAND is *already* "not AND"; a single NAND gives you the inverse of what you want, and you must invert it back.

**(b)** `OR(a, b) = NAND(NOT(a), NOT(b))`. The law is **De Morgan's**: "not (not-a and not-b)" is the same as "a or b". Put another way, "at least one is true" is the same as "it is not the case that both are false".

**(c)** Because NAND is **functionally complete** — one gate type suffices for every circuit in the machine, so a factory can concentrate on manufacturing that one component extremely well, at enormous volume, with a single set of tooling and one set of timing and reliability characteristics. NAND is also cheap in transistors and fast in the common CMOS process. Uniformity is worth more than having a varied catalogue of gates.

### C9 — how the two methods scale

**(a)** Half of 1920 × 1080 is 1,036,800 pixels, at 4 bytes each = **4,147,200 bytes ≈ 3.96 MB**.

**(b)** 2,073,600 pixels ÷ 8 = **259,200 bytes ≈ 253 KB**. About **16 times** smaller.

**(c)** The coordinate list **doubles** to 8,294,400 bytes ≈ 7.9 MB, because it pays per selected pixel. The bitmap **does not change at all** — still 259,200 bytes — because it pays per pixel of the frame regardless of how many are selected. The ratio widens to **32×**. So the list's cost depends on the *content* while the bitmap's depends only on the *dimensions*: the bitmap has a fixed, predictable size, which for a program that must allocate memory in advance is often worth more than the raw saving. (The list only wins when very few pixels are selected — for a handful of points, storing coordinates is obviously cheaper.)

### C10 — compositing

**(a)** `result = (photo & mask) | (background & ~mask);`

**(b)** `& mask` keeps the photo **only where the subject is**, zeroing everything else — the cut-out. `~mask` **flips the selection**, turning "where the subject is" into "where the background is". `& ~mask` then keeps the new backdrop only there, leaving a subject-shaped hole. Finally `|` **joins** them: each image is zero exactly where the other has content, so OR merges them with no overlap and nothing overwritten.

**(c)** Using `mask` twice means both images are cut to the **same** region — the subject's shape — and the background's hole is never made. The output shows the subject's silhouette filled with the two images ORed together, a bright garbled mess inside the subject's outline, and **pure black everywhere else**, because neither term contributes anything outside the mask. The giveaway is that the entire background is empty.

### C11 — the 2038 problem

**(a)** 2^31 − 1 = **2,147,483,647**.

**(b)** It wraps to **−2,147,483,648**, exactly as `127 + 1` gives −128 in one byte. The clock does not stop or error — it silently reports a time in December 1901. Any comparison of "is this timestamp later than that one" now gives the wrong answer.

**(c)** Store the count in a **signed 64-bit** integer instead. Its maximum is 9,223,372,036,854,775,807 seconds, which is roughly **292 billion years** — comfortably longer than the age of the universe. Most modern systems have already moved; the remaining risk is in old embedded devices and file formats that fixed the field width.

### C12 — four billion addresses

**(a)** Because each number is **one byte**, and a byte holds 0 to 255. The dots are punctuation for humans; the address is four bytes in a row.

**(b)** 256^4 = **2^32 = 4,294,967,296** — about 4.3 billion.

**(c)** There are not enough addresses for one per person, let alone one per device — and many people own several connected devices. Consequences you should expect: large blocks are **reserved and cannot be used on the public internet** (private ranges, loopback, multicast), so the usable count is well below 4.3 billion; addresses must be **shared**, so many machines sit behind one public address and something must keep track of which reply belongs to which machine; and a **larger address format** becomes necessary — which is IPv6, with 128 bits. All of that is Task 37.

---

# Part D — Convert and Trace

**D1 —**

| | Binary | Hex |
|---|---|---|
| (a) −1 | `1111 1111` | `0xFF` |
| (b) −2 | `1111 1110` | `0xFE` |
| (c) −16 | `1111 0000` | `0xF0` |
| (d) −100 | `1001 1100` | `0x9C` |
| (e) −128 | `1000 0000` | `0x80` |

Working for (d): 100 = `0110 0100`; invert → `1001 1011`; add 1 → `1001 1100`.

**D2 —** (a) **10** — top bit clear, so read it directly. (b) **−127** (invert `1000 0001` → `0111 1110`, +1 → `0111 1111` = 127). (c) **−2**. (d) **127**. (e) **−64**.

**D3 —**

| Bits | `unsigned char` | `signed char` |
|---|---|---|
| `0000 0000` | 0 | 0 |
| `0111 1111` | 127 | 127 |
| `1000 0000` | 128 | **−128** |
| `1111 1111` | 255 | **−1** |
| `1010 1010` | 170 | **−86** |

The first two rows agree because the top bit is clear — for patterns below 128 the two readings are identical. They diverge the moment the sign bit is set.

**D4 —**
(a) 120 + 10 = **−126**. **Wraps** — 130 does not fit in a signed byte.
(b) −120 − 10 = **126**. **Wraps** — −130 is below −128.
(c) 250 + 10 = **4**. **Wraps** — 260 needs a ninth bit, which falls off.
(d) 5 − 6 = **255**. **Wraps** — unsigned values never go negative.

All four wrapped, and not one of them produced an error.

**D5 —**
```
  1111 0001
+ 0000 1111
-----------
1 0000 0000        nine bits
```
The stored byte is `0000 0000`, and as a `signed char` that is **0**.

`1111 0001` is −15 and `0000 1111` is +15, so **you have just computed −15 + 15 = 0** using nothing but plain binary addition, with no attention paid to signs. That is Iteration 4's whole argument in one sum.

**D6 —** `a & b` = **8** · `a | b` = **14** · `a ^ b` = **6** · `~a` = **−13** · `a << 1` = **24** · `a >> 1` = **6** · `a && b` = **1** · `a || b` = **1** · `!a` = **0**.

Note the last three are all 0 or 1, while the first six are ordinary numbers.

**D7 —**
(a) `1 & 1` = 1 and `1 && 1` = 1 — **same** (by coincidence).
(b) `2 & 1` = 0 and `2 && 1` = 1 — **differ**. `010 & 001` shares no bits, but both operands are non-zero.
(c) `0 | 0` = 0 and `0 || 0` = 0 — **same**.
(d) `~0` = −1 and `!0` = 1 — **differ**, completely. One flips 32 bits, the other answers a yes/no question.

Three of the four pairs agree, and only (b) and (d) expose the difference. That is exactly why the bug is hard to catch.

**D8 —**

| a | b | NAND | NOT a | AND | OR | XOR |
|---|---|---|---|---|---|---|
| 0 | 0 | 1 | 1 | 0 | 0 | 0 |
| 0 | 1 | 1 | 1 | 0 | 1 | 1 |
| 1 | 0 | 1 | 0 | 0 | 1 | 1 |
| 1 | 1 | 0 | 0 | 1 | 1 | 0 |

Only the last row of NAND is 0. `NOT a` ignores `b` entirely, so it is 1 whenever `a` is 0. XOR is 1 exactly where `a` and `b` differ.

**D9 —**
(a) `photo & mask` = `1010 1010 & 1111 0000` = **`1010 0000`**
(b) `~mask` = **`0000 1111`**
(c) `background & ~mask` = `0101 0101 & 0000 1111` = **`0000 0101`**
(d) result = `1010 0000 | 0000 0101` = **`1010 0101`**

Read the result: the top four bits came from the photo (where the mask was 1) and the bottom four from the background (where it was 0). Nothing overlaps.

**D10 —** The bytes are `0000 0001`, `0111 1111`, `1111 1111`.

```
(lldb) memory read -f d -s 1 -c 3 &arr[0]
0x7fffffffe4c8: 1
0x7fffffffe4c9: 127
0x7fffffffe4ca: -1

(lldb) memory read -f u -s 1 -c 3 &arr[0]
0x7fffffffe4c8: 1
0x7fffffffe4c9: 127
0x7fffffffe4ca: 255

(lldb) memory read -f x -s 1 -c 3 &arr[0]
0x7fffffffe4c8: 0x01 0x7f 0xff
```

The first two bytes read the same either way — their top bit is clear. Only the third differs: **−1 or 255**, the same all-ones byte.

**D11 —**

| Bytes | Bits | Unsigned max | Signed min | Signed max |
|---|---|---|---|---|
| 1 | 8 | 255 | −128 | 127 |
| 2 | 16 | 65,535 | −32,768 | 32,767 |
| 4 | 32 | 4,294,967,295 | −2,147,483,648 | 2,147,483,647 |

**D12 —**
(a) 800 × 600 = **480,000 pixels**.
(b) 480,000 bits ÷ 8 = **60,000 bytes** (about 58.6 KB).
(c) One byte per pixel is **480,000 bytes** — **8 times larger**, which is the obvious consequence of using 8 bits to store an answer that needs 1. Storing a yes/no in a whole byte is the commonest way to waste memory eight-fold, and it is exactly what a bitmap avoids.

---

## The pattern to notice

**The bits do not know their type.** Almost half this paper is one fact in different clothes: A4/A5, A13, C1, C5, D3, D10. A byte is a bit pattern, and `signed`/`unsigned`, `%d`/`%u`, `-f d`/`-f u` are all just *readings*. The type lives in your source code and in your head — never in the memory.

**Invert and add one, in both directions.** The same two steps write a negative and read one back (C2). Once you trust that, every conversion on this sheet is mechanical.

**The patterns are a circle and the wrap is silent.** A8, A9, C4, C11, D4 — five questions, one behaviour, and in none of them does anything crash. The 2038 problem is this exact bug at global scale.

**Two's complement was chosen for the hardware, not for us.** One adder instead of two circuits, one zero instead of two, every pattern used (A10, C3, D5). Sign-and-magnitude is easier for humans to read, and that was not enough.

**`&` is bits, `&&` is true/false — and they agree often enough to be dangerous.** A14, C6, C7, D7. Three of D7's four pairs give the same answer. Test with values where they *differ*.

**Simple parts, repeated, build everything.** One gate makes every gate (A18–A20, C8, D8); one bit per pixel makes a mask (A21, A22, C9, C10, D9, D12); one byte, repeated four times, makes an address (A24, C12).

And that last one is where you are going next. Four bytes, 2^32 addresses, and the question of how a reply crosses the world and lands on **your** laptop rather than the one beside you — that is Task 37.
