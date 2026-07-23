# Adding to an Address — Question Bank

Work everything **on paper** using the start-step-land rule from the worksheet: read `*(&a[i] + n)` as "start at the address of slot `i`, step `n` slots, open that box." Remember the two slot sizes you measured: `int` is **4 bytes**, `char` is **1 byte**, and a step of `n` moves `n × slot-size` bytes.

Unless a question says otherwise, arrays have four slots and the debugger addresses are examples — your differences will match, your exact hex will not. Answers are **not** in this file.

---

# Part A — Multiple Choice

**A1.** In `int a[4]`, which slot does `*(a + 3)` name?

- A) `a[0]`
- B) `a[2]`
- C) `a[3]`
- D) the slot 3 bytes after `a`

**A2.** `a[2]` is exactly the same as:

- A) `*(a + 2)`
- B) `&a[2]`
- C) `a + 2`
- D) `*a + 2`

**A3.** For `int a[4]`, how many **bytes** is `&a[2]` past `&a[0]`?

- A) 2
- B) 4
- C) 8
- D) 16

**A4.** For `char a[4]`, how many **bytes** is `&a[2]` past `&a[0]`?

- A) 1
- B) 2
- C) 4
- D) 8

**A5.** `*(&a[2] - 2)` names which slot?

- A) `a[0]`
- B) `a[1]`
- C) `a[2]`
- D) a slot before the array

**A6.** For an `int` array, if `&a[0]` is `0x1000`, then `&a[1]` is:

- A) `0x1001`
- B) `0x1002`
- C) `0x1004`
- D) `0x1010`

**A7.** For a `char` array, if `&a[0]` is `0x1000`, then `&a[1]` is:

- A) `0x1001`
- B) `0x1002`
- C) `0x1004`
- D) `0x1008`

**A8.** `*(a + 0)` is the same as:

- A) `a[0]`
- B) `a`
- C) `&a[0]`
- D) `*a + 0` which is a number, not a slot

**A9.** The **same** expression `*(&a[1] + 2)` is used on an `int a[4]` and on a `char a[4]`. It lands on:

- A) `a[3]` in both
- B) `a[3]` for the int, `a[2]` for the char
- C) a different slot in each, because the byte-jumps differ
- D) `a[2]` in both

**A10.** Between the int array and the char array, which `+1` step moves more **bytes**?

- A) the int array (4 bytes vs 1)
- B) the char array (they hold letters)
- C) neither — both move 1 byte
- D) it depends on the value stored

**A11.** `memory read -f x -s 1 -c 16 &a[0]` on an `int a[4]` shows how many bytes?

- A) 4
- B) 8
- C) 16
- D) 64

**A12.** Reading four **ints** as raw bytes, a small value like `9` appears as `09 00 00 00`. The three `00`s are there because:

- A) the number is wrong
- B) each int slot is 4 bytes wide, so the value fills one byte and the slot's other three are 0
- C) the debugger is padding the output
- D) `9` needs four bytes to be stored

**A13.** `*(&a[3] - 3)` names which slot?

- A) `a[0]`
- B) `a[1]`
- C) `a[3]`
- D) a slot before the array

**A14.** To ask the debugger how many bytes one slot occupies, you use:

- A) `memory read`
- B) `sizeof`
- C) `print_array`
- D) `&a[0]`

---

# Part B — Fill in the Blanks

**B1.** `a[i]` is shorthand for `*(________)`.

**B2.** The array name `a` on its own is the address of slot number ____, the same as `&a[____]`.

**B3.** For an `int` array, adding `1` to a slot's address moves forward ____ bytes.

**B4.** For a `char` array, adding `1` to a slot's address moves forward ____ bytes.

**B5.** `*(&a[3] - 2)` names slot `a[____]`.

**B6.** The bytes moved by `+ n` equal `n × ________`.

**B7.** `*(a + 2) = 5;` puts `5` into slot `a[____]`.

**B8.** Four ints occupy ____ bytes in memory; four chars occupy ____ bytes.

**B9.** `*(&a[0] + 3)` names the ____ slot of a four-slot array (word: first / last / middle).

**B10.** In a `memory read` of a whole array, the number of bytes between one value and the next is the ________ ________.

---

# Part C — Scenario Questions

**C1.** Trace this on paper and write the final `print_array` line (`int a[4]`):

```c
int a[4] = {0, 0, 0, 0};
*(a + 2)      = 7;
*(&a[3] - 1)  = 8;
*(&a[0] + 3)  = 9;
```

**C2.** An `int a[4]` has `&a[0] = 0x7fffffffe500`. Give the hex addresses of: `&a[1]`, `&a[2]`, `&a[1] + 2`, and `&a[2] - 2`.

**C3.** An `int a[4]` currently holds `{5, 0, 0, 10}`. Predict the output of `memory read -f x -s 1 -c 16 &a[0]` (write the 16 bytes).

**C4.** Trace this on paper and write the final `print_array` line (`char a[4]`, and `72` is `'H'`, `90` is `'Z'`):

```c
char a[4] = {'.', '.', '.', '.'};
*(&a[2] + 1) = 90;
*(a + 0)     = 72;
```

**C5.** For a `char a[4]`, how many bytes is `&a[3]` beyond `&a[0]`? For an `int a[4]`, how many bytes is `&a[3]` beyond `&a[0]`? Explain the difference in one sentence.

**C6.** In your own words: why does `*(&a[1] + 2)` land on `a[3]` for **both** an int array and a char array, even though in the debugger the address jumps by 8 bytes for the int and only 2 bytes for the char?

**C7.** `memory read -f x -s 1 -c 4 &a[0]` on a `char a[4]` prints `48 49 4a 4b`. Given `0x48` is `'H'`, `0x49` is `'I'`, `0x4a` is `'J'`, `0x4b` is `'K'`, what will `print_array` show?

---

When you finish, check the answer key — and check your *reasoning* (start-step-land, and slot-size × step), not only the final letters and numbers.
