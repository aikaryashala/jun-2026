# Slicing and Ranges — Questions

Unless a question says otherwise, every item uses these three:

```python
nums: list[int] = [5, 10, 15, 20, 25, 30, 35]
names: list[str] = ["Ravi", "Sita", "Anil", "Kiran", "Meena", "Bhanu"]
word: str = "Hyderabad"
```

Work each one out on paper **before** you type it. Draw the position table if it helps — both rows of numbers, positive and negative.

---

## Part A — Multiple Choice

**A1.** `print(nums[2:5])` prints:

- A. `[10, 15, 20]`
- B. `[15, 20, 25]`
- C. `[15, 20, 25, 30]`
- D. `[20, 25, 30]`

**A2.** `print(len(nums[1:6]))` prints:

- A. `4`
- B. `5`
- C. `6`
- D. `7`

**A3.** Which one gives the **last two** elements of `nums`?

- A. `nums[2:]`
- B. `nums[-2:]`
- C. `nums[:-2]`
- D. `nums[2:-2]`

**A4.** `print(nums[-2:-4])` prints:

- A. `[30, 35]`
- B. `[25, 20]`
- C. `[]`
- D. `IndexError`

**A5.** `nums` has seven elements. `print(nums[100:])` prints:

- A. `[35]`
- B. `[]`
- C. `None`
- D. it raises `IndexError`

**A6.** With the same list, `print(nums[100])`:

- A. prints `[]`
- B. prints `None`
- C. raises `IndexError`
- D. raises `ValueError`

**A7.** `print(nums[::2])` prints:

- A. `[5, 15, 25, 35]`
- B. `[10, 20, 30]`
- C. `[5, 10]`
- D. `[15, 25, 35]`

**A8.** `print(nums[::-1])` prints:

- A. `[5, 10, 15, 20, 25, 30, 35]`
- B. `[35, 30, 25, 20, 15, 10, 5]`
- C. `[35]`
- D. `[]`

**A9.** `print(nums[3:6:-1])` prints:

- A. `[20, 25, 30]`
- B. `[30, 25, 20]`
- C. `[]`
- D. it raises `ValueError`

**A10.** `print(nums[::0])`:

- A. prints the whole list
- B. prints `[]`
- C. raises `ValueError`
- D. never finishes

**A11.** After these three lines, what does `print(a)` show?

``` python
a: list[int] = [1, 2, 3]
b: list[int] = a
b[0] = 99
```

- A. `[1, 2, 3]`
- B. `[99, 2, 3]`
- C. `[1, 2, 3, 99]`
- D. it raises `TypeError`

**A12.** Starting from `x: list[int] = [1, 2, 3, 4, 5]`, the line `x[1:3] = [20, 30, 40]` leaves `len(x)` equal to:

- A. `3`
- B. `5`
- C. `6`
- D. `8`

**A13.** `print(len(range(3, 11)))` prints:

- A. `7`
- B. `8`
- C. `9`
- D. `11`

**A14.** `print(list(range(6, 1, 1)))` prints:

- A. `[6, 5, 4, 3, 2]`
- B. `[1, 2, 3, 4, 5]`
- C. `[6]`
- D. `[]`

---

## Part B — Fill in the Blanks

For **B1–B6**, write the missing slice. Use the shortest form that works.

**B1.** The first three elements of `nums`: `nums[________]`

**B2.** The last three elements of `nums`: `nums[________]`

**B3.** Every element of `names` except the first: `names[________]`

**B4.** Every element of `names` except the first **and** the last: `names[________]`

**B5.** The elements of `nums` at positions 1, 3 and 5, in one slice: `nums[________]`

**B6.** A new list holding the same elements as `nums`, which can be changed without touching `nums`: `nums[________]`

For **B7–B10**, complete the sentence.

**B7.** In `nums[2:6]`, the number `2` is ____________ and the number `6` is ____________, which is why the slice holds ____________ elements.

**B8.** Leaving out the first number in a slice means Python starts at ____________; leaving out the second means it stops at ____________.

**B9.** Asking for `nums[100]` raises ____________, but asking for `nums[100:]` gives ____________ — because an index must produce ____________ while a slice produces ____________.

**B10.** `range(10, 0, -1)` counts down and its last number is ____________, because the second number is ____________ just as it is in a slice.

---

## Part C — Scenario Questions

**C1.** Trace this program and write down all four printed lines in order.

``` python
first: list[int] = [1, 2, 3, 4]
second: list[int] = first[:]
third: list[int] = first
second[0] = 100
third[3] = 400
print(first)
print(second)
print(third)
print(first == second)
```

Then explain in your own words why `second` and `third` behaved differently, given that both were built from `first` in a single line.

**C2.** Give the list **and its length** after each of the three marked lines.

``` python
x: list[int] = [1, 2, 3, 4, 5, 6]
x[2:4] = [30, 40, 50]     # line 1
del x[0:2]                # line 2
x[1:4] = []               # line 3
```

Which of the three lines could not have been written as an assignment to a single position, and why?

**C3.** A student wants the last three names from `names`. They write:

``` python
print(names[-3:-1])
```

Write down exactly what they get. Then write the slice they should have used, and explain in one sentence what the `-1` did to their result.

**C4.** These two lines use the same two numbers and the same step.

``` python
print(nums[5:2:-1])
print(nums[2:5:-1])
```

Write down what each prints. Explain why one of them is empty, and say why Python gives an empty list here rather than raising an error.

**C5.** A program reads a list that sometimes holds only two readings and sometimes holds fifty. It always does this:

``` python
print(readings[:10])
```

Explain what happens in each case, and why no length check is needed before this line. Then describe one situation where this same safety could hide a bug from you, and say what you would print to catch it.

**C6.** For the list `nums` and **any** value of `n` from 0 to 7, the expression `nums[:n] + nums[n:] == nums` is always `True`.

Explain why, in terms of which slice the element at position `n` lands in. Then say what would go wrong with this if a slice included both of its numbers instead of excluding the stop.

**C7.** A data file arrives as a list whose first element is a header, not data:

``` python
rows: list[str] = ["date,temp", "18,35", "19,33", "20,31"]
```

Write two different lines that give you the data without the header — one that leaves `rows` untouched, and one that changes `rows` itself. Say which is which, and which one you would choose if another part of the program still needs the header later.

**C8.** Each of these outputs came from a single slice of `nums`. Write the slice that produced it.

- (a) `[10, 15, 20]`
- (b) `[35, 30, 25]`
- (c) `[5, 20, 35]`
- (d) `[]`

---

## Part D — String Slicing

Use `word: str = "Hyderabad"` for D1–D7.

**D1.** Write down what each of these prints.

``` python
print(word[:5])
print(word[-3:])
print(word[3:6])
print(word[::2])
```

**D2.** Write down what `print(word[::-1])` prints, and name the one other line in this whole question bank that does the same job to a list.

**D3.** Write down all three printed lines, and say what each one tells you about how Python stores text.

``` python
print(word[0])
print(type(word[0]))
print(len(word[0]))
```

**D4.** In C you would write `char c = name[0];`. Explain in two sentences what is different about `word[0]` in Python, and why `len(word[0])` is not an error.

**D5.** This line raises an error. Write down the error's **name** and its **message**, and say why it is not an `IndexError`.

``` python
word[0] = "M"
```

**D6.** Does `word[0:2] = "My"` raise the same error as D5, a different one, or none at all? Explain your answer in one sentence.

**D7.** Using only slices and `+`, write one line that produces each of these from `word`. Do not use any method you have not been taught.

- (a) `"Myderabad"`
- (b) `"Hyderaba"`
- (c) `"HydXXXbad"`

**D8.** A date arrives as `stamp: str = "2026-08-18"`. Using only slices and `+`, write one line that prints it as `18/08/2026`. Then write down how many characters long the result is, and how you know without running it.

---

## Part E — Range

**E1.** Write down what each of these prints.

``` python
print(list(range(2, 6)))
print(list(range(1, 8, 3)))
print(list(range(0, 10, 4)))
```

**E2.** Write down what `print(list(range(6, 1, -1)))` prints, paying attention to its **last** number.

**E3.** One of these produces a countdown that reaches `0` and one does not. Say which is which, and write down both results in full.

``` python
print(list(range(5, 0, -1)))
print(list(range(5, -1, -1)))
```

**E4.** These two lines print different things. Write down both, and explain in one sentence what a `range` actually holds.

``` python
print(range(2, 7))
print(list(range(2, 7)))
```

**E5.** Write the slice of `nums` and the `range` that both use the numbers `1`, `6` and `2`. Run both, write down what each gives you, and say in one sentence what the difference is between the two results.

**E6.** For each of these, say whether you would use a **slice** or a **range**, and give one reason:

- (a) the last four readings from a list of unknown length
- (b) printing the numbers 1 to 50
- (c) every second letter of a word
- (d) numbering a list of six names as `1.`, `2.`, `3.` …
