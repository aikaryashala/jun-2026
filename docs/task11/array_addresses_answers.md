# Adding to an Address — Answers with Reasoning

Check the **reasoning**, not just the letter. Almost every answer is one of two moves: *start-step-land* (which slot?) or *step × slot-size* (how many bytes?). If your answer was right but you counted bytes when the question wanted slots — or the reverse — treat it as wrong and redo it.

---

# Part A — Multiple Choice

**A1. C) `a[3]`** — `*(a + 3)` starts at the front (`a` = `&a[0]`), steps 3 slots, opens the box: `a[3]`. It is not "3 bytes after `a`" — a step counts slots.

**A2. A) `*(a + 2)`** — the core identity: `a[i]` is shorthand for `*(a + i)`. `&a[2]` is only the *address*; `a + 2` is also only the address (no `*`); `*a + 2` opens `a[0]` and adds 2 to its value.

**A3. C) 8** — `&a[2]` is 2 slots past `&a[0]`, and an int slot is 4 bytes: `2 × 4 = 8`.

**A4. B) 2** — same 2 slots, but a char slot is 1 byte: `2 × 1 = 2`.

**A5. A) `a[0]`** — start at `a[2]`, step **back** 2 slots: `a[2] → a[1] → a[0]`.

**A6. C) `0x1004`** — one step on an int array is 4 bytes: `0x1000 + 4`.

**A7. A) `0x1001`** — one step on a char array is 1 byte: `0x1000 + 1`.

**A8. A) `a[0]`** — `*(a + 0)` steps 0 slots from the front and opens the box: `a[0]`. (`a` and `&a[0]` are the address only — no box opened.)

**A9. A) `a[3]` in both** — `*(&a[1] + 2)` means "start at `a[1]`, step 2 slots" — that is `a[3]` regardless of what the slots hold. The byte-jumps differ (8 vs 2); the landing slot does not.

**A10. A) the int array (4 bytes vs 1)** — a step is one slot, and an int slot (4 bytes) is bigger than a char slot (1 byte). The stored value never affects the step.

**A11. C) 16** — `-c 16` asks for 16 one-byte items. It happens to be the whole array, because four 4-byte int slots *are* 16 bytes.

**A12. B) each int slot is 4 bytes wide** — `9` needs only one byte (`0x09`); the other three bytes of its 4-byte slot are `0`. (Little-endian puts the value's byte first, so `09 00 00 00`.)

**A13. A) `a[0]`** — start at `a[3]`, step back 3 slots: `a[3] → a[2] → a[1] → a[0]`.

**A14. B) `sizeof`** — `p sizeof(int)` answers 4, `p sizeof(char)` answers 1. `memory read` shows the bytes; `&a[0]` shows one address.

---

# Part B — Fill in the Blanks

**B1.** `a + i` — `a[i]` is `*(a + i)`.

**B2.** slot **0**, same as `&a[**0**]` — the bare array name is the address of the first slot.

**B3.** **4** bytes — one int slot.

**B4.** **1** byte — one char slot.

**B5.** `a[**1**]` — start at `a[3]`, step back 2: `a[3] → a[2] → a[1]`.

**B6.** `n × ` **slot size** (element size) — the language scales the step for you.

**B7.** `a[**2**]` — `*(a + 2)` lands on `a[2]`.

**B8.** **16** bytes; **4** bytes — four 4-byte slots vs four 1-byte slots.

**B9.** **last** — `*(&a[0] + 3)` is `a[3]`, the last of four slots.

**B10.** **slot size** (element size) — the spacing between consecutive values is exactly one slot.

---

# Part C — Scenario Questions

**C1.** Final line: **` 0,  0,  8,  9`**

```
start:          0, 0, 0, 0
*(a + 2) = 7     → a[2]         → 0, 0, 7, 0
*(&a[3] - 1) = 8 → a[3] back 1 = a[2] → 0, 0, 8, 0   (overwrites the 7)
*(&a[0] + 3) = 9 → a[0] fwd 3  = a[3] → 0, 0, 8, 9
```

Two different-looking expressions (`a + 2` and `&a[3] - 1`) reached the **same** slot `a[2]` — start-step-land is what matters, not how the expression is written.

**C2.** Int slots are 4 bytes apart:

```
&a[1]     = 0x7fffffffe504      (e500 + 4)
&a[2]     = 0x7fffffffe508      (e500 + 8)
&a[1] + 2 = 0x7fffffffe50c      (e504 + 8)   = &a[3]
&a[2] - 2 = 0x7fffffffe500      (e508 - 8)   = &a[0]
```

**C3.** `{5, 0, 0, 10}` as 16 raw bytes (`5` = `0x05`, `10` = `0x0a`, little-endian):

```
05 00 00 00  00 00 00 00  00 00 00 00  0a 00 00 00
```

Each value takes one byte, followed by three `00`s filling out its 4-byte slot.

**C4.** Final line: **`'H', '.', '.', 'Z'`**

```
start:            '.', '.', '.', '.'
*(&a[2] + 1) = 90 → a[2] fwd 1 = a[3], 90 = 'Z' → '.', '.', '.', 'Z'
*(a + 0) = 72     → a[0],        72 = 'H'        → 'H', '.', '.', 'Z'
```

**C5.** char: **3 bytes** (`3 slots × 1`); int: **12 bytes** (`3 slots × 4`). Both are 3 slots past `&a[0]`, but a char slot is 1 byte and an int slot is 4, so the same slot-distance is a different byte-distance.

**C6.** Because `+2` counts **slots, not bytes**. `*(&a[1] + 2)` says "start at `a[1]`, move 2 slots forward" — that is `a[3]` in any array. When C turns those 2 slots into an actual address, it multiplies by the slot size: `2 × 4 = 8` bytes for ints, `2 × 1 = 2` bytes for chars. Different byte-jumps, same slot, so the same landing index.

**C7.** `print_array` shows: **`'H', 'I', 'J', 'K'`** — each byte is one char slot, so `48 49 4a 4b` reads straight across as `H I J K` (no gaps, because char slots are 1 byte and pack tight).

---

**Pattern to notice across your mistakes:** nearly every error is one of three habits — counting a step as bytes instead of slots, forgetting the slot size differs by type (4 for int, 1 for char), or reading a memory dump as if every value were one byte. The fix is the same each time: decode the expression as start-step-land for the slot, then multiply by the slot size only when you want bytes.
