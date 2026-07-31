# Address Variables — Answers with Reasoning

Check the **reasoning**, not just the numbers. For every `*(p ± k)` ask two questions: *which box does it name* (start where `p` points, step `k` slots), and *is it a read or a write* (right of `=` reads, left writes). If your final values matched but you named a wrong box on the way, redo it.

---

# Part A — Final values

**A1.** `a = {10, 99, 30, 77}`, `p → &a[1]`

```
p = &a[1]
*(p) = 99      → a[1] = 99            {10, 99, 30, 40}
*(p + 2) = 77  → a[3] = 77            {10, 99, 30, 77}
```

`p` never moved — only the boxes did.

**A2.** `a = {1, 1, 14, 4, 5}`, `p → &a[3]`

```
p = &a[3]              (a[3] is 4)
*(p - 1) = *(p) + 10   → a[2] = 4 + 10 = 14    {1, 2, 14, 4, 5}
*(p - 2) = *(p - 3)    → a[1] = a[0] = 1        {1, 1, 14, 4, 5}
```

`*(p)` on the right read `a[3]`; `*(p-1)` on the left wrote `a[2]`.

**A3.** `c = {'a', 'X', 'c', 'a'}`, `q → &c[0]`

```
q = &c[0]
*(q + 1) = 'X'   → c[1] = 'X'          {a, X, c, d}
*(q + 3) = *(q)  → c[3] = c[0] = 'a'   {a, X, c, a}
```

A `char` address variable steps 1 byte per slot, but the slot arithmetic reads the same: `q + 3` is `c[3]`.

**A4.** `a = {2, 12, 6, 2}`, `p → &a[0]`

```
p = &a[0]
*(p + 1) = *(p + 1) + *(p + 3)  → a[1] = 4 + 8 = 12   {2, 12, 6, 8}
*(p + 3) = *(p)                 → a[3] = a[0] = 2      {2, 12, 6, 2}
```

Line 1 reads `a[1]` and `a[3]` on the right, then writes `a[1]` on the left — the read of `a[3]` (8) happened before it was changed.

**A5.** `a = {0, 10, 5, 15, 0}`, `p → &a[2]`

```
p = &a[2]
*(p) = 5                    → a[2] = 5                {0, 0, 5, 0, 0}
*(p - 1) = *(p) * 2         → a[1] = 5 * 2 = 10       {0, 10, 5, 0, 0}
*(p + 1) = *(p - 1) + *(p)  → a[3] = 10 + 5 = 15      {0, 10, 5, 15, 0}
```

**A6.** `a = {10, 30, 20, 1}`, `p → &a[2]`

```
p = &a[0]
*(p) = 10       → a[0] = 10               {10, 1, 1, 1}
p = p + 2       → p now points to a[2]     (no box changed)
*(p) = 20       → a[2] = 20               {10, 1, 20, 1}
*(p - 1) = 30   → a[1] = 30               {10, 30, 20, 1}
```

The line `p = p + 2` moves the **address variable itself** two slots along — after it, `*(p)` means `a[2]` and `*(p-1)` means `a[1]`.

---

# Part B — Output and final state

**B1.** Output: `7` / `6` / `11`. Final: `a = {5, 6, 11, 8}`, `p → &a[2]`

```
p = &a[2]
print *(p)                  → a[2] = 7
print *(p - 1)              → a[1] = 6
*(p) = *(p - 2) + *(p - 1)  → a[2] = a[0] + a[1] = 5 + 6 = 11
print *(p)                  → a[2] = 11
```

**B2.** Output: `4` / `8` / `8`. Final: `a = {3, 1, 8, 1, 5}`, `p → &a[0]`

```
p = &a[0]
print *(p + 2)              → a[2] = 4
*(p + 2) = *(p) + *(p + 4)  → a[2] = 3 + 5 = 8
print *(p + 2)              → a[2] = 8
print a[2]                  → 8      (a[2] and *(p+2) are the same box)
```

The last two prints agree because `a[2]` **is** `*(p + 2)` — the identity from Task 11.

**B3.** Output: `e` / `l` / `l`. Final: `c = {'H', 'l', 'l', 'l', 'o'}`, `q → &c[1]`

```
q = &c[1]
print *(q)        → c[1] = 'e'
*(q) = *(q + 2)   → c[1] = c[3] = 'l'
print *(q)        → c[1] = 'l'
print c[1]        → 'l'
```

**B4.** Output: `40` / `20` / `30`. Final: `a = {10, 30, 30, 40}`, `p → &a[1]`

```
p = &a[3]
print *(p)        → a[3] = 40
p = p - 2         → p now points to a[1]     (no box changed)
print *(p)        → a[1] = 20
*(p) = *(p + 1)   → a[1] = a[2] = 30
print *(p)        → a[1] = 30
```

After `p = p - 2`, `*(p)` is `a[1]` and `*(p + 1)` is `a[2]`.

**B5.** Output: `10` / `10`. Final: `a = {10, 2, 3, 4}`, `total = 10`, `p → &a[0]`

```
p = &a[0]
total = *(p) + *(p+1) + *(p+2) + *(p+3)  = 1 + 2 + 3 + 4 = 10
print total       → 10
*(p) = total      → a[0] = 10
print a[0]        → 10
```

---

# Part C — Plain variables (no stepping)

**C1.** `x = 20`, `p → &x`

```
p = &x
*(p) = 20   → writes 20 into x
```

**C2.** `a = 8`, `b = 7`, `p → &a`

```
p = &a
b = *(p)      → read a (7) into b        → b = 7
*(p) = b + 1  → write 7 + 1 into a       → a = 8
```

**C3.** `x = 10`, `y = 20`, `p → &y`

```
p = &x
*(p) = 10   → write 10 into x            → x = 10
p = &y      → re-point p at y (x unchanged)
*(p) = 20   → write 20 into y            → y = 20
```

`p = &y` changes only where `p` points; `x` keeps the `10` written earlier.

**C4.** `n = 10`, `p → &n`, `q → &n`

```
p = &n,  q = &n           (both point at the same box)
*(p) = 9          → n = 9
*(q) = *(q) + 1   → read n (9), write 9 + 1 → n = 10
```

Because `p` and `q` point at the *same* variable, a write through either one is seen by the other.

**C5.** `a = 8`, `b = 8`, `p → &a`, `q → &b`

```
p = &a,  q = &b
*(p) = *(q)   → read b (8), write it into a   → a = 8
```

`b` is only read, so it stays `8`; the copy went through the two address variables.

**C6.** `a = 10`, `b = 15`, `c = 5`, `d = 4`. `p → &c`, `q → &c`

```
p = &a,  q = &b
*(p) = 10          → a = 10
*(q) = *(p) + 5    → b = 10 + 5 = 15
p = &c             → p now points at c
q = p              → q now points at c too  (address copied from p)
*(q) = d           → c = 4
*(p) = *(q) + 1    → c = 4 + 1 = 5   (p and q are the same box now)
```

After `q = p`, both point at `c`, so the last two lines both act on `c`.

**C7.** `w = 8`, `x = 14`, `y = 7`, `z = 16`. `p → &x`, `q → &x`

```
p = &w,  q = &z
*(p) = *(q)          → w = 8
*(q) = *(q) + *(p)   → z = 8 + 8 = 16
p = &x               → p now points at x
*(p) = y             → x = 7
q = p                → q now points at x too
*(q) = *(q) * 2      → x = 7 * 2 = 14
```

`y` is only ever read, so it stays `7`.

**C8.** `a = 7`, `b = 6`, `c = 6`, `d = 14`. `p → &d`, `q → &d`

```
p = &a,  q = &b
*(p) = 5           → a = 5
*(q) = 6           → b = 6
*(p) = *(q) + 1    → a = 6 + 1 = 7
p = &c             → p points at c
*(p) = *(q)        → c = 6
q = &d             → q points at d
*(q) = *(p) + a    → d = 6 + 7 = 13
p = q              → p points at d too
*(p) = *(p) + 1    → d = 13 + 1 = 14
```

**C9.** `e = 'X'`, `f = 'd'`, `g = 'X'`, `h = 'd'`. `p → &f`, `q → &f`

```
p = &e,  q = &g
*(p) = 'X'    → e = 'X'
*(q) = *(p)   → g = 'X'
p = &f        → p points at f
q = p         → q points at f too
*(q) = 'Y'    → f = 'Y'
*(p) = h      → f = 'd'   (overwrites the 'Y' just written)
```

`char` address variables behave exactly like `int` ones — one-byte boxes instead of four.

**C10.** `a = 6`, `b = 4`, `c = 6`, `d = 10`. `p → &d`, `q → &d`

```
p = &a,  q = &b
*(p) = *(q) + *(p)   → a = 4 + 2 = 6
q = &c               → q points at c
*(q) = *(p)          → c = 6
p = &d               → p points at d
*(p) = *(q) + b      → d = 6 + 4 = 10
q = p                → q points at d too
*(q) = *(q) - a      → d = 10 - 6 = 4
*(p) = *(p) + c      → d = 4 + 6 = 10
```

**C11.** `m = 100`, `n = 10`, `o = 20`, `s = 100`. `p → &s`, `q → &s`

```
p = &m,  q = &n
*(p) = 100         → m = 100
*(q) = *(p) - 90   → n = 100 - 90 = 10
p = &o             → p points at o
*(p) = *(q) + n    → o = 10 + 10 = 20
q = &s             → q points at s
*(q) = *(p)        → s = 20
p = q              → p points at s too
*(p) = *(p) + m    → s = 20 + 100 = 120
*(q) = *(q) - o    → s = 120 - 20 = 100
```

**C12.** `a = 50`, `b = 30`, `c = 10`, `d = 60`. `p → &c`, `q → &a`

```
p = &a,  q = &d
*(p) = *(q)          → a = 40
*(q) = *(p) + b      → d = 40 + 20 = 60
p = &b               → p points at b
q = &c               → q points at c
*(p) = *(q)          → b = 30
*(q) = *(p) + a      → c = 30 + 40 = 70
p = q                → p points at c too
*(p) = *(p) - d      → c = 70 - 60 = 10
q = &a               → q points at a
*(q) = *(p) + *(q)   → a = 10 + 40 = 50
```

Here `p` and `q` end pointing at *different* variables — track each one separately through every re-point.

---

**Pattern to notice across your mistakes:** almost every error is one of three — naming the wrong box for `*(p ± k)` (recount the slots from where `p` currently points), forgetting that assigning to `p` moves the *address variable* and not any array box, or reading a box on the right *after* you meant to (each line's right side uses the values as they are the instant that line runs). Decode every `*(...)` as "which box, read or write," and the values fall out.
