# Python `input()` and Types — Answers with Reasoning

Check the **reasoning**, not just the letter. Four ideas cover almost every question here:

1. `input()` always returns a **string** — no matter what the user types.
2. `int()` and `str()` build a **new** value; the original variable never changes.
3. `+` and `*` do different jobs depending on the type, and refuse to mix types.
4. Every `print` is: **the values, joined by `sep`, followed by `end`** — defaults `" "` and `"\n"`.

---

# Part A — Multiple Choice

**A1. B) `<class 'str'>`** — `input()` hands back whatever was typed, as text. Typing digits does not make it a number; `"45"` is still a string. This is the single most important fact in the task.

**A2. B) `34`** — both values are strings, so `+` **joins** (concatenates) them: `"3"` followed by `"4"` is `"34"`.

**A3. A) `7`** — no quotes, so these are ints, and `+` **adds**. Compare with A2: the same `+` sign, two different jobs, decided entirely by the type.

**A4. C) a single space** — that is `print`'s default `sep`. Note the commas in the source are how you hand `print` three values; the commas themselves are never printed.

**A5. B) a newline** — that is the default `end`. It is the reason each `print` normally starts a fresh line; nothing magic is happening.

**A6. B) `ab`** — `sep=""` is an empty string, so nothing at all goes between the values.

**A7. C) `ValueError`** — the *type* was fine (`int()` wants a string, and got one), but the *contents* were wrong: `"hi123"` has letters, so there is no number to build. Full message: `invalid literal for int() with base 10: 'hi123'`.

**A8. B) `SyntaxError` — and no line of the program runs** — `in` is a reserved word, so Python cannot read the line as valid Python. It gives up before starting, which is why nothing prints. This is the key difference from A7 and A9: those happen *while running*.

**A9. C) `TypeError`** — `"12"` is a string and `5` is an int. Python will not guess whether you meant `"125"` or `17`, so it refuses. Message: `can only concatenate str (not "int") to str`.

**A10. B) prints a blank line** — `print()` has no values to print, but it still prints its `end`, which is a newline. That newline on an otherwise empty line is the blank line.

**A11. A) `42`** — `int()` ignores spaces around the outside of the string. Spaces *inside* are a different matter: `int("4 2")` is a `ValueError`.

**A12. B) `<class 'str'>`** — `str(25)` builds text. The value looks like `25` on screen either way, which is exactly the trap: the screen cannot show you a type.

**A13. B) `555`** — `*` on a string **repeats** it. `"5" * 3` gives `"555"`. If you wanted `15`, you needed `int("5") * 3`.

**A14. C) `SyntaxError`** — Python reads the whole file before running any of it. A syntax mistake anywhere means nothing runs at all. `TypeError` and `ValueError` happen mid-run, so everything printed before them has already appeared on screen.

---

# Part B — Fill in the Blanks

**B1. `str` (string)** — always, with no exceptions.

**B2. `int("75")`** — builds the number `75` from the text.

**B3. `str(75)`** — builds the text `"75"` from the number.

**B4. `type()`** — e.g. `print(type(x))`.

**B5. `sep`** — short for *separator*.

**B6. `end`** — short for *ending*.

**B7. `sep="*"`** — giving `print("a", "b", sep="*")` → `a*b`.

**B8. `end=""`** — with no ending, the next `print` continues on the same line, so the two calls produce the single line `HelloWorld`. (Note that `sep` would not help here — there is only one value in each call, so there is nothing for `sep` to sit between.)

**B9. `ValueError`** — right type, wrong contents. Contrast with `TypeError`: wrong type altogether.

**B10. reserved** (also accepted: *keywords*) — Python has claimed these words for its own grammar.

---

# Part C — Scenario Questions

**C1.**
```
34
7
3+4
```
Line 1: `p` and `q` are both strings, so `+` joins → `34`. Line 2: `int()` makes numbers first, so `+` adds → `7`. Line 3: two values handed to `print` with `sep="+"` between them — no arithmetic happens at all here, it is just a `+` character printed between the two strings. Three lines, three different mechanisms producing similar-looking output.

**C2.**
```
Sita, Rama, Lakshmana!
End
```
**Two lines.** `sep=", "` goes between each pair of values (comma-space, twice). `end="!\n"` replaces the default ending — but because it contains `\n`, you still get the newline you would otherwise have got free, so `End` starts on a fresh line.

**C3.**
```
5050
100
<class 'str'>
```
Line 1: `str(w)` is `"50"`, and `"50" + "50"` joins → `5050`. Line 2: `w` is still the int `50` — `str()` never changed it — so `50 + 50` adds → `100`. Line 3 confirms `str(w)` really is text.

**C4.**
```
1-2-3
```
**One line.** The first two `print` calls end with `-` instead of a newline, so everything runs together. Only the last `print` supplies a newline. Note that `end="-"` puts the dash *after* the value, which is why there is no trailing dash after `3`.

**C5.**
```
99
18
99
```
Lines 1 and 3 both print `99`, but they are **not the same kind of value**. Line 1: `"9" * 2` repeats the string → the string `"99"`. Line 3: `r * 2` builds that same string `"99"` first, *then* `int()` turns it into the **number** `99`. Line 2 is the odd one out: `int(r)` makes the number `9` first, so `*` multiplies → `18`. This is the worksheet's central lesson in miniature — identical on screen, different underneath.

**C6.**
```
abcd
next
```
The first two calls print `ab` and `cd` with no separator and no ending, so they land on one line as `abcd`. The bare `print()` then supplies the newline that closes it. Then `next` prints normally. Note there is **no blank line** — the `print()` did not add one, it only finished the line that was left hanging.

**C7.**
```
Total: 100
Total: 6040
```
The **second** line is the bug. `m` and `n` are strings, so `m + n` joins them into `"6040"` instead of adding. Nothing crashes — Python is doing exactly what it was told — which makes this the most dangerous kind of mistake: a wrong answer with no error message. The programmer wanted `100`, and gets it on the first line by converting with `int()` before adding. (The space after `Total:` on both lines is `print`'s default `sep`, not part of the text.)

**C8.**
```
10 and 10
<class 'int'> vs <class 'str'>
```
Line 1 is the trap: the int `10` and the string `"10"` print identically. Line 2 is the proof that they are different things. Together these two lines are the whole task in four values.

**C9.**
```
1 2 3
123
1
2
3
```
Five lines of output from three `print` calls. Default `sep` is a space; `sep=""` removes it; `sep="\n"` makes the separator a newline, so the three values land on three lines. `sep` can be any text you like, including invisible characters.

**C10.**
```
44
8
44
```
`k` is the string `"4"` and `j` is the number `4`. Line 1 joins → `44`. Line 2 adds → `8`. Line 3 prints both values with nothing between them: the string `"4"` then the number `4`, giving `44` again. Note that `print` never complains about mixing types — it just prints each value. Only *operators* like `+` care.

**C11.**

| Line | Result |
|---|---|
| `print("age: " + 21)` | **crashes — `TypeError`** (`can only concatenate str (not "int") to str`) |
| `print(int("twenty"))` | **crashes — `ValueError`** — no digits at all to read |
| `print("5" - "2")` | **crashes — `TypeError`** (`unsupported operand type(s) for -: 'str' and 'str'`). Unlike `+` and `*`, subtraction has no meaning at all between strings |
| `print(str(5) + "5")` | **runs** — prints `55`. `str(5)` is `"5"`, and string + string joins |
| `print(int("42abc"))` | **crashes — `ValueError`** — a single stray letter is enough; `int()` needs the *whole* string to be a number |
| `print(int("-17"))` | **runs** — prints `-17`. A leading minus sign is part of a valid number |

**C12.**

(a) It prints:
```
Mark 1: 45
Mark 2: 50
Total: 4550
```
(b) **No, it does not crash.** That is the point of the question. `input()` returned two strings, and `+` joined them into `"4550"`.

(c) Convert before calculating:
```python
first = input("Mark 1: ")
second = input("Mark 2: ")
total = int(first) + int(second)
print("Total:", total)
```
which prints `Total: 95`. (`int(input("Mark 1: "))` on one line is also correct.)

**C13.**

(a) Python reads the **whole file** before it runs any of it. Line 2 cannot be understood, so Python stops at reading time — the program never starts, and `print("Starting")` never gets its turn. Any error that lets `"Starting"` appear first must be a run-time error, not this one.

(b) **`SyntaxError`**:
```
  File "roll.py", line 2
    for = input("Roll number: ")
        ^
SyntaxError: invalid syntax
```

(c) `for` is a reserved word. Rename the variable:
```python
print("Starting")
roll = input("Roll number: ")
print(roll)
```

**C14.**
```python
print(name, roll, sep="-")
```
Hand `print` both variables and set the separator to a dash. Writing `print(name + "-" + roll)` also produces the right line, but it is doing by hand what `sep` was made for — and it only works because both values are strings. If `roll` had been the number `42`, the `+` version would raise a `TypeError` while the `sep` version would still print `Govind-42`.

---

## The pattern to notice

Look back at C1, C3, C5, C8 and C10. In every one of them, two values printed **exactly the same** on screen and were **not the same kind of thing**. The screen is not evidence of a type. Only `type()` is.

And the practical rule that follows from it: **`input()` gives you a string, so convert before you calculate.** If you ever see a "sum" that looks like the two numbers stuck together — `45` and `50` giving `4550` — you have found a missing `int()`.
