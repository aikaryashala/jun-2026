# Signed Bytes, Two's Complement, and Gates — Question Bank

Answer on paper, using the worksheet's ideas: one byte holds 256 patterns and the **type** decides how to read them; a negative is written by **inverting every bit and adding one**; the patterns form a **circle**, so they wrap; `&` works on bits while `&&` works on true/false; **NAND** alone builds every gate; and N bytes hold `2^(8N)` patterns.

Work the bits. Do not guess from the decimal.

Answers are **not** in this file.

---

# Part A — Multiple Choice

**A1.** `sizeof(signed char)` and `sizeof(unsigned char)` are:

- A) 1 and 2
- B) 1 and 1
- C) 2 and 1
- D) 4 and 4

**A2.** How many different bit patterns can one byte hold?

- A) 128
- B) 255
- C) 256
- D) 512

**A3.** The range of a `signed char` is −128 to +127. Why is the positive side one shorter?

- A) the sign bit is wasted
- B) one pattern is used for zero
- C) 127 is the largest prime below 128
- D) the compiler reserves a pattern for errors

**A4.** The byte `1111 1011` is read as `unsigned char`. What is it?

- A) −5
- B) 5
- C) 251
- D) 123

**A5.** The same byte `1111 1011` is read as `signed char`. What is it?

- A) −5
- B) 251
- C) −123
- D) −4

**A6.** Two's complement of a number is:

- A) invert every bit
- B) invert every bit, then add one
- C) add one, then invert every bit
- D) put a 1 in the top bit

**A7.** Which value is its own two's complement in one byte?

- A) 0 only
- B) −1
- C) −128
- D) 127

**A8.** `signed char a = 127; a = a + 1;` What does `a` hold?

- A) 128
- B) 0
- C) −1
- D) −128

**A9.** `unsigned char u = 0; u = u - 1;` What does `u` hold?

- A) −1
- B) 0
- C) 255
- D) undefined, always crashes

**A10.** The main engineering reason computers use two's complement rather than sign-and-magnitude is:

- A) it is easier for humans to read
- B) subtraction becomes ordinary addition, so one adder circuit does both
- C) it stores one extra bit
- D) it makes multiplication faster

**A11.** In LLDB, which command shows 3 bytes starting at `&arr[0]` in **binary**?

- A) `memory read -f x -s 1 -c 3 &arr[0]`
- B) `memory read -f b -s 1 -c 3 &arr[0]`
- C) `frame variable arr`
- D) `memory read -f d -s 3 -c 1 &arr[0]`

**A12.** In `memory read -f d -s 1 -c 4 &arr[0]`, what does the `-c 4` mean?

- A) 4 bytes per item
- B) show 4 items
- C) format number 4
- D) start 4 bytes in

**A13.** You run `memory read -f u -s 1 -c 1 &b` and see `200`. What does `-f d` show for the same byte?

- A) 200
- B) −56
- C) −200
- D) −128

**A14.** `4 & 2` and `4 && 2` give:

- A) 0 and 0
- B) 0 and 1
- C) 1 and 1
- D) 6 and 1

**A15.** What is the result of `!5`?

- A) −6
- B) 5
- C) 0
- D) 1

**A16.** What is the result of `~5`?

- A) −6
- B) 0
- C) 1
- D) −5

**A17.** Which pair of operators **short-circuits** — that is, may not evaluate its right-hand side?

- A) `&` and `|`
- B) `&&` and `||`
- C) `<<` and `>>`
- D) `~` and `!`

**A18.** A NAND gate outputs 0 for which inputs?

- A) `0 0` only
- B) `0 1` and `1 0`
- C) `1 1` only
- D) all four

**A19.** `NOT(a)` built from NAND alone is:

- A) `NAND(a, 0)`
- B) `NAND(a, 1)`
- C) `NAND(a, a)`
- D) `NAND(NAND(a, a), a)`

**A20.** "NAND is functionally complete" means:

- A) NAND is the fastest gate
- B) every other gate can be built from NAND alone
- C) NAND uses the fewest transistors
- D) NAND never produces 0

**A21.** To keep only the part of an image the mask selects, you write:

- A) `image | mask`
- B) `image & mask`
- C) `image ^ mask`
- D) `image & ~mask`

**A22.** Why does a one-bit-per-pixel mask usually take far less space than a list of `(i, j)` coordinates?

- A) bitmaps are compressed automatically
- B) a pixel's position is where it sits in the bitmap, so no coordinates are stored
- C) coordinates must be stored as text
- D) a bitmap only stores the person, not the background

**A23.** How many values fit in 2 bytes?

- A) 512
- B) 1,024
- C) 65,536
- D) 4,294,967,296

**A24.** An IPv4 address is 4 bytes. How many are possible?

- A) 2^16
- B) 2^24
- C) 2^32
- D) 2^64

---

# Part B — Fill in the Blanks

**B1.** One byte is ____________ bits and can hold ____________ different patterns.

**B2.** The range of an `unsigned char` is ____________ to ____________; the range of a `signed char` is ____________ to ____________.

**B3.** To write a negative number in two's complement you ____________ every bit and then ____________.

**B4.** Inverting every bit, without adding one, gives the ____________ complement.

**B5.** In a signed byte, the leftmost bit is called the ____________ bit, and a value of 1 there means the number is ____________.

**B6.** The one negative value in a byte with no matching positive value is ____________.

**B7.** In `memory read -f b -s 1 -c 3`, the `-f` sets the ____________, `-s` sets the ____________, and `-c` sets the ____________.

**B8.** The LLDB format letter for binary is ____________, for hexadecimal is ____________, for signed decimal is ____________ and for unsigned decimal is ____________.

**B9.** `&` and `|` are called ____________ operators; `&&` and `||` are called ____________ operators.

**B10.** A logical operator always produces either ____________ or ____________.

**B11.** A NAND gate produces 0 only when both inputs are ____________.

**B12.** Because every other gate can be built from it alone, NAND is said to be ____________ ____________.

**B13.** To swap a background you compute `(photo & mask) | (background & ____________)`.

**B14.** For N bytes the unsigned range is 0 to ____________, and the signed range is ____________ to ____________.

---

# Part C — Scenario Questions

**C1.** A student declares `signed char s = -5;` and `unsigned char u = 251;` and then inspects both in LLDB with `memory read -f b -s 1 -c 1`.
(a) What will each command print?
(b) Explain the result in terms of what the type does and does not control.
(c) The student concludes "the compiler must store the type next to the value in memory". Are they right? Justify your answer using what they just saw.

**C2.** Work out the byte for −20, showing every step.
(a) Write 20 in eight bits.
(b) Apply the two steps and give the final pattern in binary and hex.
(c) Now apply the same two steps to your answer. What do you get, and what does that tell you about the procedure?

**C3.** A student is convinced the sign bit works like a minus sign, so they read `1111 1011` as "minus 123" — 1 for the sign, `111 1011` = 123 for the magnitude.
(a) What is the correct value?
(b) Explain precisely where their reasoning breaks down.
(c) Which representation *would* have made them right, and why is it not used?

**C4.** A program counts events in a `signed char` called `count`, starting at 0 and adding 1 per event. It runs for a long time.
(a) What is the value after 127 events?
(b) What is it after 128 events?
(c) The program has a line that assumes `count` never decreases. Describe what goes wrong, and say why no error message appears.

**C5.** In LLDB a student runs three commands on the same single byte and gets three different numbers:
```
memory read -f d -s 1 -c 1 &b     ->  -112
memory read -f u -s 1 -c 1 &b     ->  144
memory read -f x -s 1 -c 1 &b     ->  0x90
```
(a) Is the memory changing between commands?
(b) Show that all three describe the same eight bits.
(c) Which of the three is "the real value"?

**C6.** Two students argue. One says `if (flags & 4)` and `if (flags && 4)` do the same thing "because 4 is not zero anyway".
(a) For `flags = 4`, what does each condition give?
(b) For `flags = 2`, what does each give?
(c) Which student is right, and explain the danger of testing only with `flags = 4`.

**C7.** A program contains `if (count != 0 && total / count > 5)`. A reviewer suggests changing `&&` to `&` because "it is shorter and means the same".
(a) What happens when `count` is 0 under each version?
(b) Name the property of `&&` that makes the original safe.
(c) Give one situation where `&` between two conditions is genuinely what you want.

**C8.** Using only NAND gates:
(a) Build `AND(a, b)` and explain why it needs two NANDs.
(b) Build `OR(a, b)` and state the law of logic that explains it.
(c) A factory can manufacture only one kind of gate. Explain why choosing NAND is a sensible decision.

**C9.** An HD photo is 1920 × 1080. A program must record which pixels belong to a person, and about half of the frame is the person.
(a) Estimate the storage as a coordinate list, using two 2-byte numbers per pixel.
(b) Give the storage as a one-bit-per-pixel bitmap.
(c) The subject moves closer and now fills the whole frame. What happens to each figure, and what does that tell you about how the two methods scale?

**C10.** You have `photo`, a new `background`, and a `mask` where 1 marks the subject.
(a) Write the single expression that produces the composited image.
(b) Explain what each of the three operators contributes.
(c) A student writes `(photo & mask) | (background & mask)` by mistake. Describe what the output looks like and why.

**C11.** A system stores a timestamp as a count of seconds in a **signed 4-byte** integer.
(a) What is the largest value it can hold?
(b) Explain, using Iteration 5's idea, what happens the second after that maximum is reached.
(c) What is the standard fix, and roughly how long does it last?

**C12.** An IPv4 address is written as four numbers separated by dots, such as `192.168.1.17`.
(a) Why is each number always in the range 0 to 255?
(b) How many IPv4 addresses exist in total? Give it as a power of 2 and as a decimal.
(c) The world has more than 8 billion people. What does your answer to (b) imply, and name one consequence you would expect.

---

# Part D — Convert and Trace

Show your working for every conversion.

**D1.** Convert to eight-bit two's complement, in binary and hex: (a) −1 (b) −2 (c) −16 (d) −100 (e) −128

**D2.** Each of these bytes is read as a `signed char`. Give the decimal value of each: (a) `0000 1010` (b) `1000 0001` (c) `1111 1110` (d) `0111 1111` (e) `1100 0000`

**D3.** Each byte below is read **both** ways. Complete the table.

| Bits | As `unsigned char` | As `signed char` |
|---|---|---|
| `0000 0000` | | |
| `0111 1111` | | |
| `1000 0000` | | |
| `1111 1111` | | |
| `1010 1010` | | |

**D4.** Give the result of each, and say whether it wraps:
(a) `signed char x = 120; x = x + 10;`
(b) `signed char y = -120; y = y - 10;`
(c) `unsigned char p = 250; p = p + 10;`
(d) `unsigned char q = 5; q = q - 6;`

**D5.** Add `1111 0001` and `0000 1111` as plain eight-bit binary. Give the nine-bit sum, then the byte that is actually stored, then read that byte as a `signed char`. What arithmetic have you just performed?

**D6.** For `a = 12` and `b = 10`, give all of: `a & b`, `a | b`, `a ^ b`, `~a`, `a << 1`, `a >> 1`, `a && b`, `a || b`, `!a`.

**D7.** Say which of these pairs give the **same** answer and which differ:
(a) `1 & 1` and `1 && 1`
(b) `2 & 1` and `2 && 1`
(c) `0 | 0` and `0 || 0`
(d) `~0` and `!0`

**D8.** Complete the truth table using only what NAND does.

| a | b | NAND | NOT a | AND | OR | XOR |
|---|---|---|---|---|---|---|
| 0 | 0 | | | | | |
| 0 | 1 | | | | | |
| 1 | 0 | | | | | |
| 1 | 1 | | | | | |

**D9.** `photo = 1010 1010`, `background = 0101 0101`, `mask = 1111 0000`.
(a) `photo & mask`
(b) `~mask`
(c) `background & ~mask`
(d) the composited result

**D10.** A student runs this and sees the output below. Fill in the three missing lines.
```
(lldb) memory read -f b -s 1 -c 3 &arr[0]
0x7fffffffe4c8: 0b00000001
0x7fffffffe4c9: 0b01111111
0x7fffffffe4ca: 0b11111111

(lldb) memory read -f d -s 1 -c 3 &arr[0]
?
(lldb) memory read -f u -s 1 -c 3 &arr[0]
?
(lldb) memory read -f x -s 1 -c 3 &arr[0]
?
```

**D11.** Complete the table.

| Bytes | Bits | Unsigned max | Signed min | Signed max |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |
| 4 | | | | |

**D12.** A 800 × 600 image.
(a) How many pixels?
(b) How many bytes for a one-bit-per-pixel mask?
(c) How many bytes if you instead stored a full `unsigned char` (0 or 1) per pixel, and how many times larger is that?
