# Python `input()` and Types — Question Bank

Answer on paper, using the worksheet's ideas: `input()` always returns a **string**; `int()` and `str()` build a **new** value of the other type; `type()` tells you what you are holding; and every `print` is *the values joined by `sep`, followed by `end`*.

Where a question asks for output, write it **exactly** — count the spaces, and show where each line breaks. Answers are **not** in this file.

---

# Part A — Multiple Choice

**A1.** A program has `age = input("Age: ")`. The user types `45` and presses Enter. What does `print(type(age))` show?

- A) `<class 'int'>`
- B) `<class 'str'>`
- C) `<class 'float'>`
- D) `45`

**A2.** What does `print("3" + "4")` print?

- A) `7`
- B) `34`
- C) `3 4`
- D) an error

**A3.** What does `print(3 + 4)` print?

- A) `7`
- B) `34`
- C) `3 4`
- D) an error

**A4.** In `print("Ravi", "Kumar")`, what does Python put between the two values by default?

- A) nothing
- B) a comma
- C) a single space
- D) a newline

**A5.** By default, every `print` call finishes with:

- A) a space
- B) a newline
- C) nothing
- D) a full stop

**A6.** What does `print("a", "b", sep="")` print?

- A) `a b`
- B) `ab`
- C) `a, b`
- D) `a`

**A7.** `num = int("hi123")` raises:

- A) `SyntaxError`
- B) `TypeError`
- C) `ValueError`
- D) nothing — it gives `123`

**A8.** A file begins with the line `in = "hi123"`. What happens?

- A) it runs fine
- B) `SyntaxError` — and no line of the program runs
- C) `ValueError` on line 1
- D) `TypeError` on line 1

**A9.** `print("12" + 5)` raises:

- A) `ValueError`
- B) `SyntaxError`
- C) `TypeError`
- D) nothing — it prints `125`

**A10.** What does a `print()` with nothing inside the brackets do?

- A) nothing at all
- B) prints a blank line
- C) raises an error
- D) prints the word `None`

**A11.** What is the value of `int("  42  ")`?

- A) `42`
- B) `"  42  "`
- C) a `ValueError`
- D) `4`

**A12.** After `x = str(25)`, what is `type(x)`?

- A) `<class 'int'>`
- B) `<class 'str'>`
- C) `<class 'float'>`
- D) `<class 'type'>`

**A13.** What does `print("5" * 3)` print?

- A) `15`
- B) `555`
- C) `5 5 5`
- D) a `TypeError`

**A14.** Which error stops the program **before** any line runs, so nothing is printed?

- A) `TypeError`
- B) `ValueError`
- C) `SyntaxError`
- D) all three behave the same

---

# Part B — Fill in the Blanks

**B1.** `input()` always hands back a value of type ____________.

**B2.** To turn the string `"75"` into the number `75`, you write ____________.

**B3.** To turn the number `75` into the string `"75"`, you write ____________.

**B4.** To find out what kind of value a variable holds, you use the ____________ function.

**B5.** The `print` argument that changes what goes **between** the values is called ____________.

**B6.** The `print` argument that changes what goes **after** all the values is called ____________.

**B7.** `print("a", "b", ____________)` prints `a*b`.

**B8.** Fill the blank so both lines appear as one line `HelloWorld` on screen:
```python
print("Hello", ____________)
print("World")
```

**B9.** The error name for "the type was right but the contents were wrong" is ____________.

**B10.** `in`, `if`, `for` and `class` cannot be used as variable names because they are ____________ words.

---

# Part C — Scenario Questions

**C1.** Write the exact output.
```python
p = "3"
q = "4"
print(p + q)
print(int(p) + int(q))
print(p, q, sep="+")
```

**C2.** Write the exact output, and say how many lines appear on screen.
```python
print("Sita", "Rama", "Lakshmana", sep=", ", end="!\n")
print("End")
```

**C3.** Write the exact output.
```python
w = 50
print(str(w) + str(w))
print(w + w)
print(type(str(w)))
```

**C4.** Write the exact output, and say how many lines appear on screen.
```python
print("1", end="-")
print("2", end="-")
print("3")
```

**C5.** Write the exact output. Two of these three lines print the same thing — say which two, and explain why they are still not the same kind of value.
```python
r = "9"
print(r * 2)
print(int(r) * 2)
print(int(r * 2))
```

**C6.** Write the exact output.
```python
print("a", "b", sep="", end="")
print("c", "d", sep="", end="")
print()
print("next")
```

**C7.** Write the exact output. One of these two lines is a bug that does not crash — say which, and what the programmer probably wanted.
```python
m = "60"
n = "40"
print("Total:", int(m) + int(n))
print("Total:", m + n)
```

**C8.** Write the exact output.
```python
print(10, "10", sep=" and ")
print(type(10), type("10"), sep=" vs ")
```

**C9.** Write the exact output.
```python
print(1, 2, 3)
print(1, 2, 3, sep="")
print(1, 2, 3, sep="\n")
```

**C10.** Write the exact output.
```python
k = str(4)
j = int("4")
print(k + k)
print(j + j)
print(k, j, sep="")
```

**C11.** For each line below, say whether it runs or crashes. If it crashes, name the error. Treat each line as a separate program.
```python
print("age: " + 21)
print(int("twenty"))
print("5" - "2")
print(str(5) + "5")
print(int("42abc"))
print(int("-17"))
```

**C12.** This program is meant to read a student's two test marks and print the total. The user types `45`, then `50`.
```python
first = input("Mark 1: ")
second = input("Mark 2: ")
print("Total:", first + second)
```
(a) What does it print?
(b) Does it crash?
(c) Rewrite it so it prints the correct total.

**C13.** A student writes this and gets an error before anything is printed.
```python
print("Starting")
for = input("Roll number: ")
print(for)
```
(a) Why is `"Starting"` not printed, even though it is the first line?
(b) Name the error.
(c) Fix the program.

**C14.** Write a single `print` call that produces exactly this line, using the two variables given — do not type the words into the `print` yourself:
```python
name = "Govind"
roll = "42"
```
Wanted output:
```
Govind-42
```
