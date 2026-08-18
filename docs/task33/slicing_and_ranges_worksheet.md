# Slicing and Ranges — Three Numbers, One Rule

**Goal.** In Task 31 you learnt to reach **one** element with `marks[0]`. Today you will learn to reach a **stretch** of them — `marks[1:4]` — with a notation that takes up to three numbers. You will find that the same three numbers work on a string, and that `range`, which you have already used, takes the very same three numbers under the very same rule. By the end you should be able to look at any `[a:b:c]` and say what comes back without running it.

**You need:** your Linux VM (WSL), a terminal, `python3`, a notebook, and a pencil.

Make a folder for today:

```
mkdir -p ~/task33
cd ~/task33
```

Write each program with `nano`, save it, run it with `python3 filename.py`.

From Task 32 onward every variable we create carries its **type hint**, so each program says what it is holding before you read a single line of it.

---

## First, what you already know

Two things from earlier tasks are the whole foundation of today.

**From Task 31 — one index reaches one element.**

```python
marks: list[int] = [82, 91, 77]
print(marks[0])    # 82  — the first
print(marks[-1])   # 77  — the last
```

**Also from Task 31 — `range(3)` gave you `0, 1, 2`.** You wrote it as `for i in range(len(marks))`, and the worksheet said the numbers run *"up to but not including 3"*.

Hold on to that phrase. It is not a fact about `range`. It is the rule that runs through everything in this task.

> **The golden rule of today**
> One index reaches **an element**. Three numbers reach a **stretch**: start **at** the first, stop **before** the second, move by the third. Slicing hands you back a **new container of the same kind** — and unlike an index, a slice never falls off the end.

---

## Iteration 1 — Two numbers, not one

**a. What we set up**

```python
# first_slice.py
marks: list[int] = [82, 91, 77, 68, 95, 73]
print(marks[1])
print(marks[1:4])
print(len(marks[1:4]))
```

The new thing is the **colon** inside the square brackets. `marks[1]` has one number in it; `marks[1:4]` has two.

**b. Task**

Predict all three lines before running. For the second line especially: it starts at position 1. Does it end with the element at position 4, or the one at position 3? Write down your answer and the number of elements you expect.

**c. Observation (what you should find)**

```
91
[91, 77, 68]
3
```

Look at the first two lines together, because the difference between them is the whole idea.

**`marks[1]` gave `91` — an element.** No brackets. It is an `int`, the thing itself.

**`marks[1:4]` gave `[91, 77, 68]` — a list.** With brackets. It is a `list[int]`, a container, even though it came out of another container. One colon changed what *kind of thing* you got back.

Now count the elements. Positions 1, 2 and 3 came out. Position 4 — the `95` — did **not**.

```
position:    0    1    2    3    4    5
marks:      82   91   77   68   95   73
                 └─────────────┘
                 marks[1:4]  →  starts AT 1, stops BEFORE 4
```

The first number is **included**, the second is **excluded**. A stretch written this way is called a **half-open** range: closed at the start, open at the stop.

That looks like an odd choice until you notice what it buys you. `len(marks[1:4])` printed `3`, and `4 - 1` is `3`. **The length of a slice is the second number minus the first**, with no arithmetic to think about. Had both ends been included, every length would have needed a `+ 1`, and every one of those is a chance to be wrong by one.

**Takeaway to say out loud:** "One number gives me an element, two numbers give me a list — starting at the first and stopping *before* the second, so the length is just the difference."

**Write it yourself**

**W1.1** Make a list of seven city names. Print the element at position 2, then the slice from 2 to 5, then the length of that slice. In your notebook write down which city appears in the slice but not at position 2, and which city sits at position 5 and did not come out at all.

**W1.2** Using a list of eight ints, write five slices whose lengths are 1, 2, 3, 4 and 5 — but every one of them must start at position 2. Print each slice and its length on the same line. Before running, write down the second number you used each time and check it against the length you got.

---

## Iteration 2 — Leaving an end out

**a. What we set up**

```python
# open_ends.py
marks: list[int] = [82, 91, 77, 68, 95, 73]
print(marks[:3])
print(marks[3:])
print(marks[:])
print(marks[:3] + marks[3:] == marks)
```

Either number may simply be left out.

**b. Task**

Predict the first three lines. Then think about the fourth before you run it: it glues the first two slices back together and compares the result with the original. Will it print `True` or `False`?

**c. Observation (what you should find)**

```
[82, 91, 77]
[68, 95, 73]
[82, 91, 77, 68, 95, 73]
True
```

**A missing number means "as far as this side goes".** Leave out the start and Python begins at the beginning; leave out the stop and it runs to the end. Leave out both and you get everything.

So `marks[:3]` is exactly `marks[0:3]`, and `marks[3:]` is exactly `marks[3:len(marks)]`. The short forms are not a different feature — they are the same slice with an obvious number left unwritten.

The fourth line is where the half-open rule pays you back. `marks[:3]` stops *before* 3 and `marks[3:]` starts *at* 3, so the element at position 3 is in exactly one of them — not in both, and not in neither. Glue the two together and you have the original list back, with nothing repeated and nothing lost.

**The same number splits the list cleanly, and it does so at any value of `n`:**

```
marks[:n]  +  marks[n:]   ==   marks
```

If the ends had both been included, `marks[0:3]` and `marks[3:5]` would share the element at 3, and the join would have seven elements instead of six.

**Takeaway to say out loud:** "A missing number means the far end — and because the stop is excluded, `[:n]` and `[n:]` fit together exactly, with no overlap and no gap."

**Write it yourself**

**W2.1** Make a list of six marks. Print the first half and the second half using two slices, each with one number left out. Then print whether joining them gives back the original. Do it again splitting at a different position, and once more splitting at 0 — and write in your notebook what the two slices are in that last case.

**W2.2** With a list of five names, write the same slice four different ways: as `[0:5]`, as `[:5]`, as `[0:]`, and as `[:]`. Print all four. In your notebook write one sentence saying why all four printed the same thing, and one saying which you would actually write in a program.

---

## Iteration 3 — Counting from the right

**a. What we set up**

```python
# from_the_right.py
marks: list[int] = [82, 91, 77, 68, 95, 73]
print(marks[-3:])
print(marks[:-1])
print(marks[-1:-3])
```

In Task 31 you used `marks[-1]` for the last element. Negative numbers work inside a slice too.

**b. Task**

Predict the first two lines. Then look hard at the third: it asks for a stretch from `-1` to `-3`. Write down what you think it gives before running it.

**c. Observation (what you should find)**

```
[68, 95, 73]
[82, 91, 77, 68, 95]
[]
```

The first two are the two most useful slices you will write all year.

**`marks[-3:]` is "the last three".** Start three from the right, run to the end.

**`marks[:-1]` is "everything except the last".** Run from the beginning, stop *before* the last one — the same exclusion rule, now counted from the right. This is how you drop a trailing item: a header line, a newline character, a total row.

Then the third line, which printed **nothing at all** — an empty list, `[]`. No error, no complaint.

Line up what those numbers actually mean:

```
position:    0    1    2    3    4    5
negative:   -6   -5   -4   -3   -2   -1
marks:      82   91   77   68   95   73
                           ↑         ↑
                          -3        -1
```

`-1` sits at position 5. `-3` sits at position 3. So `marks[-1:-3]` is asking to start at 5 and stop before 3 — to walk from position 5 *backwards* to position 3. **A slice never walks backwards on its own.** It reads left to right, and when the start is already at or past the stop it has nothing to collect, so it hands you an empty list.

Negative numbers do not reverse anything. They are only another way of *naming a position*: `-3` and `3` are the same slot in this list. Swap the two numbers into left-to-right order and it works:

```python
print(marks[-3:-1])   # [68, 95]
```

**Takeaway to say out loud:** "A negative number names a position from the right, but it does not turn the slice around — the first number must still sit to the left of the second, or I get an empty list."

**Write it yourself**

**W3.1** With a list of seven ints, print: the last two elements, the last four, everything except the last, everything except the first, and everything except the first and last — each with a single slice. Write each slice down in your notebook first, then check.

**W3.2** For a list of five names, draw the position table in your notebook with both rows of numbers, positive and negative. Then write four slices that all produce `["the 2nd", "the 3rd", "the 4th"]` — one using two positive numbers, one using two negative numbers, one mixing positive start with negative stop, and one mixing negative start with positive stop. Print all four and confirm they match.

---

## Iteration 4 — A slice never falls off the end

**a. What we set up**

```python
# past_the_end.py
marks: list[int] = [82, 91, 77, 68, 95, 73]
print(marks[2:99])
print(marks[99:])
print(marks[4:2])
print(marks[99])
```

There are six elements. Every line here mentions a position that does not exist, or asks for something impossible.

**b. Task**

The list has six elements and there is certainly nothing at position 99. Predict each of the four lines. Which of them do you expect to be an error — one of them, some of them, all four?

**c. Observation (what you should find)**

```
[77, 68, 95, 73]
[]
[]
Traceback (most recent call last):
  File "/home/student/task33/past_the_end.py", line 5, in <module>
    print(marks[99])
          ~~~~~^^^^
IndexError: list index out of range
```

Three lines survived; the fourth stopped the program.

**`marks[2:99]` gave everything from position 2 onward.** You asked for far more than exists and Python quietly gave you what it had.

**`marks[99:]` gave `[]`.** The whole stretch is past the end, so there is nothing in it.

**`marks[4:2]` gave `[]`.** Start already to the right of the stop — the same empty result as Iteration 3, for the same reason.

**`marks[99]` raised `IndexError`.** The same `99`, the same list, one line later.

That difference is worth saying carefully, because it is the single most useful thing to know about slices:

| | asks for | if it is not there |
|---|---|---|
| `marks[99]` | **one element** | there is no such element to hand back → **`IndexError`** |
| `marks[99:]` | **a stretch** | the stretch is simply empty → **`[]`** |

An index must produce a value, so when it cannot, it must fail. A slice produces a *container*, and a container is allowed to be empty. Nothing is broken — you asked for the elements from 99 onward and there are none.

This is why slicing is safe on data whose size you do not know. `readings[:10]` is "the first ten, or all of them if there are fewer" — it will not crash on a short list, and you do not need to check `len` first.

Be careful with the other half of that, though: an empty result is not always good news. If `rows[1:]` comes back empty, your program will not crash — it will loop zero times and print nothing, and you will be left wondering why. **An `IndexError` tells you at once; an empty slice waits.**

**Takeaway to say out loud:** "An index that is out of range is an error, but a slice that is out of range is just empty — which is safe, and which is also why a wrong slice can go unnoticed."

**Write it yourself**

**W4.1** With a list of four elements, print `x[:100]`, `x[100:]`, `x[3:1]` and `x[-100:]`, and write down what each gives. Then add a line asking for `x[100]`, run it, and copy the exact error name and message into your notebook.

**W4.2** Write a program with a list of three names that prints "the first five names" using a slice, then prints how many it actually got. Now change the list to hold seven names and run the identical program again. In your notebook write down both counts, and one sentence about what you did *not* have to write to make this work on both lists.

---

## Iteration 5 — The third number: step

**a. What we set up**

```python
# step.py
marks: list[int] = [82, 91, 77, 68, 95, 73]
print(marks[::2])
print(marks[1::2])
print(marks[0:5:2])
print(marks[::3])
```

A slice can take a **third** number, after a second colon.

**b. Task**

Predict all four. For the first line, note both numbers are missing and only the step is given — what does that mean it works on?

**c. Observation (what you should find)**

```
[82, 77, 95]
[91, 68, 73]
[82, 77, 95]
[82, 68]
```

The third number is the **step** — how far to move each time. The default, all through Iterations 1 to 4, was `1`: take every element.

```
position:    0    1    2    3    4    5
marks:      82   91   77   68   95   73
[::2]        ✓         ✓         ✓          every 2nd, from the start
[1::2]            ✓         ✓         ✓     every 2nd, from position 1
[::3]        ✓                   ✓          every 3rd
```

`marks[::2]` starts at the beginning, runs to the end, and takes every second one — the elements at 0, 2, 4. `marks[1::2]` does the same but starts at 1, so it collects everything the first one missed. Together those two slices split the list into its even and odd positions.

The full form is now visible:

```
marks[start : stop : step]
        │      │      │
     start AT  stop   move
              BEFORE   by
```

and any of the three may be left out. `marks[0:5:2]` gave the same answer as `marks[::2]` here, because for this list `0` and `5` are what the missing numbers already meant — the stop is excluded, so `5` and "the end" happen to collect the same elements.

One number is not allowed: **the step may not be `0`.**

```python
print(marks[::0])
```
```
ValueError: slice step cannot be zero
```

A step of zero would mean "move nothing each time" — the slice would never reach the stop. Python refuses rather than running forever. Notice the error is a `ValueError` and not an `IndexError`: nothing here is out of range, the *value* itself is not usable.

**Takeaway to say out loud:** "The third number is how far I move each time — one by default — and `[::2]` gives me every second element from the whole list."

**Write it yourself**

**W5.1** With a list of ten ints, print every second element, every third, and every fifth. Then print two slices that between them collect all ten elements exactly once, each using a step of 2 — and add a line proving the two together have ten elements.

**W5.2** Make a list of twelve names. Print positions 2 up to 10, taking every second one, using a single slice with all three numbers written out. Write down in your notebook how many names you expect before running. Then add a line with a step of `0`, run it, and copy the exact error.

---

## Iteration 6 — A negative step

**a. What we set up**

```python
# backwards.py
marks: list[int] = [82, 91, 77, 68, 95, 73]
print(marks[::-1])
print(marks[5:2:-1])
print(marks[2:5:-1])
```

If the step can be `2`, it can also be `-1`.

**b. Task**

Predict the first line. Then compare the last two carefully — they use the same two numbers, `2` and `5`, in opposite order, both with a step of `-1`. Predict both.

**c. Observation (what you should find)**

```
[73, 95, 68, 77, 91, 82]
[73, 95, 68]
[]
```

**`marks[::-1]` is the whole list reversed.** It is the shortest way to reverse anything in Python, and you will see it constantly. Read it as: no start, no stop, step backwards by one.

Then the pair. `marks[5:2:-1]` started at position 5 and walked back to position 3, giving three elements. `marks[2:5:-1]` gave nothing.

In Iteration 3 you learnt that a slice will not walk backwards on its own. The step is what changes the direction — and once it does, **the two numbers must be the other way round.** With a negative step the slice moves right to left, so the start must be the *right-hand* end and the stop the *left-hand* one.

```
position:    0    1    2    3    4    5
marks:      82   91   77   68   95   73
                      ↑              ↑
                     stop          start
[5:2:-1]              └──────────────┘   walking ←   gives 5, 4, 3
[2:5:-1]         start at 2, walk ← to 5 … already past it → []
```

And the stop stays **excluded** even going backwards: `marks[5:2:-1]` collected positions 5, 4 and 3, but not position 2. The rule never changes; only the direction does.

This is why the empty-slice trap from Iteration 3 is worth remembering. Two numbers and a step that disagree about direction do not raise an error — they hand you `[]` and let the program carry on.

**Takeaway to say out loud:** "A negative step walks right to left, so the first number must now be the right-hand end — and `[::-1]` reverses the whole thing."

**Write it yourself**

**W6.1** With a list of eight ints, print: the whole list reversed, the last three in reverse order, and every second element taken from the right-hand end. Write each slice in your notebook before running it.

**W6.2** Take one list and write two slices that both produce the same three elements, one reading forwards and one reading backwards, then print both and note that the *order* differs. Then deliberately write a slice whose two numbers disagree with its step, print it, and write down in your notebook why it is empty rather than an error.

---

## Iteration 7 — A slice is a copy

**a. What we set up**

Two small programs this time. Write and run them separately.

```python
# copy.py
marks: list[int] = [82, 91, 77]
copy_marks: list[int] = marks[:]
copy_marks[0] = 0
print(marks)
print(copy_marks)
```

```python
# same.py
marks: list[int] = [82, 91, 77]
same_marks: list[int] = marks
same_marks[0] = 0
print(marks)
print(same_marks)
```

The two files differ by four characters: `marks[:]` in one, `marks` in the other. Everything else is identical.

**b. Task**

Predict the two printed lines for **each** program — four lines in all. Write all four down before you run either.

**c. Observation (what you should find)**

`copy.py`:

```
[82, 91, 77]
[0, 91, 77]
```

`same.py`:

```
[0, 91, 77]
[0, 91, 77]
```

Same edit, opposite outcome.

**`marks[:]` built a new list.** It is a slice, and every slice you have written today produced a *new* container — that is what Iteration 1 meant by "you get a list back". `copy_marks` is a separate list that happens to start with the same values, so changing it leaves `marks` alone.

**`same_marks = marks` built nothing at all.** It gave a second **name** to the list that was already there. There is one list, and now two names for it, so a change made through either name is visible through both.

You have met this before, in C. In Task 14 you stored the address of a variable and reached the box through it; writing through that address changed the original, because there was only ever one box. Python does not show you the addresses, but the shape of what happens is the same: `same_marks = marks` gives you another way to reach the one list, while `marks[:]` makes a second list.

So `marks[:]` — a slice that leaves out both numbers — is the ordinary way to say **"give me a copy of this list"**. It reads as "the whole of it", and because slices always build something new, the whole of it arrives as a fresh list.

Nothing changes for strings and tuples here, because you cannot modify those in the first place, so there is no way to tell a copy from a second name. It matters exactly for the containers you can change.

**Takeaway to say out loud:** "`b = a` gives the same list a second name, but `b = a[:]` builds a new list — because every slice hands back a new container."

**Write it yourself**

**W7.1** Write one program that has a list of four names, makes a copy with a slice and a second name without one, then changes the *third* element through each of them in turn, printing all three lists after each change. That is six printed lists — write down what you expect all six to be before running.

**W7.2** Take a list of five ints. Make a copy of just the first three with a slice, change an element in that copy, and print both lists. Then in your notebook answer: is a partial slice like `x[:3]` also a new list, or only the full `x[:]`? Show the line of output that proves your answer.

---

## Iteration 8 — A slice on the left of `=`

**a. What we set up**

```python
# slice_assign.py
nums: list[int] = [1, 2, 3, 4, 5]
print(nums, len(nums))
nums[1:3] = [20, 30, 40]
print(nums, len(nums))
del nums[1:3]
print(nums, len(nums))
```

Every slice so far has been on the **right** of an `=`, or inside a `print`. Now one goes on the left.

**b. Task**

`nums[1:3]` names two elements, and you are about to put **three** into that place. Predict the second line — both the list and the length. Then predict the third.

**c. Observation (what you should find)**

```
[1, 2, 3, 4, 5] 5
[1, 20, 30, 40, 4, 5] 6
[1, 40, 4, 5] 4
```

**A slice on the left replaces a stretch.** `nums[1:3]` named the elements `2` and `3` — positions 1 and 2, the stop excluded as always — and those two were taken out and `20, 30, 40` put in their place.

Notice what did *not* have to match. Two elements went out and three came in, and the list simply grew from 5 to 6. This is nothing like `nums[1] = 20`, which replaces exactly one element with exactly one and can never change the length. A slice on the left can make a list **longer or shorter**.

Then `del nums[1:3]` removed the stretch at positions 1 and 2 — the `20` and the `30` — leaving four elements. Removing a stretch is the same idea with nothing put back, and putting an empty list in does the same job:

```python
nums[1:3] = []
```

Two things to hold on to. First, this is the only place today where a slice **changes the original**. Everywhere else the original was untouched and you got a new container; here the slice is naming a region *to be overwritten*. Second, this works because a list is **mutable** — the property from Task 31. Keep it in mind for two iterations' time.

**Takeaway to say out loud:** "A slice on the left of `=` replaces a whole stretch, and it can change the length of the list — which is the one time slicing changes the original."

**Write it yourself**

**W8.1** Start with a list of six ints and print it with its length. Then, printing both after every step: replace the middle two elements with four new ones, delete the first two, and replace the last element with an empty list. Write down the six lengths you expect before running.

**W8.2** Write two programs on one list of five names. In the first use `x[2] = "New"`; in the second use `x[2:3] = ["New", "Newer"]`. Print the list and its length in both. In your notebook write one sentence on what the second one can do that the first cannot.

---

## Iteration 9 — A string takes the same three numbers

Everything from here on is the same notation on different containers. There is no new syntax left to learn today — only new places to use it.

**a. What we set up**

```python
# string_slice.py
word: str = "Karyashala"
print(len(word))
print(word[0:3])
print(word[:3])
print(word[3:])
print(word[-3:])
print(word[::2])
print(word[::-1])
```

**b. Task**

Count the letters of `Karyashala` in your notebook and write the position table, `0` to `9`. Then predict all seven lines — you already know every rule you need.

**c. Observation (what you should find)**

```
10
Kar
Kar
yashala
ala
Krahl
alahsayraK
```

Not one new rule. `word[0:3]` starts at 0 and stops before 3, so it gives three letters. `word[-3:]` is the last three. `word[::2]` takes every second letter. `word[::-1]` reverses the whole string.

```
position:   0  1  2  3  4  5  6  7  8  9
word:       K  a  r  y  a  s  h  a  l  a
[0:3]       └─────┘
[-3:]                            └─────┘
[::2]       ✓     ✓     ✓     ✓     ✓
```

The reason it all transfers is that a string, like a list, is an **ordered** container — it has positions, first to last. Task 31 taught you that ordered containers support indexed access, and slicing is simply indexed access to a stretch. Any ordered container gets it for free.

That is why `word[::-1]` is the answer to "reverse this string" in Python. In C, reversing a string means a loop, a temporary variable and careful bookkeeping about where the middle is. Here it is three characters, and it is the identical three characters that reverse a list.

The out-of-range behaviour comes along too — `word[2:99]` gives `'ryashala'`, not an error, exactly as in Iteration 4.

**Takeaway to say out loud:** "A string is an ordered container too, so every slice I learnt on lists works on it unchanged."

**Write it yourself**

**W9.1** Take your own full name as a string. Print its length, its first four letters, its last four, everything except the first letter, every second letter, and the whole thing reversed. Write the position table in your notebook first and predict each line.

**W9.2** Take the string `"2026-08-18"`. Using one slice each, print the year, the month and the day. Then print the date reversed, and the string without its two dashes using three slices joined together. Write each slice down before running it.

---

## Iteration 10 — What comes back is a string

**a. What we set up**

```python
# string_types.py
word: str = "Karyashala"
print(type(word))
print(word[0])
print(type(word[0]))
print(len(word[0]))
print(word[0:1])
print(type(word[0:3]))
```

**b. Task**

Predict all six. Line 3 is the interesting one: what **type** is a single letter taken out of a string? Write your answer down before running.

**c. Observation (what you should find)**

```
<class 'str'>
K
<class 'str'>
1
K
<class 'str'>
```

A single letter out of a string is **a string**. Not a letter type, not a character type — a `str`, of length 1.

This is a real difference from C, and worth a moment. In C you wrote:

```c
char name[] = "Ravi";
char c = name[0];
```

`name` is a string and `name[0]` is a `char` — a **different type**, one byte, printed with `%c` while the string needs `%s`. C has a character type and a string is built out of characters.

**Python has no character type at all.** There is only `str`. `word[0]` is a `str` holding one letter, `word[0:3]` is a `str` holding three, and the whole `word` is a `str` holding ten. Same type, different lengths — which is why `len(word[0])` is `1` and not an error.

This is also why `word[0]` and `word[0:1]` printed the same thing, when the equivalent on a list did not. Compare the two containers side by side:

| | one index | slice of one |
|---|---|---|
| `marks` | `marks[0]` → `82` — an `int` | `marks[0:1]` → `[82]` — a `list[int]` |
| `word` | `word[0]` → `'K'` — a `str` | `word[0:1]` → `'K'` — a `str` |

For a list the two differ, because the elements are `int` and the container is `list`. For a string they look identical, because the "elements" of a string are strings themselves. The rule has not changed — a slice still gives back a container of the same kind. It is just that with strings, the container and its elements are the same kind.

**Takeaway to say out loud:** "Python has no character type — a single letter is a string of length 1, which is why slicing a string always gives back a string."

**Write it yourself**

**W10.1** With any word of at least six letters, print its type, its first letter, the type of that first letter, and the length of that first letter. Then do the same four lines for a list of six ints, and write down in your notebook the one line where the two behave differently.

**W10.2** Take a string and print `s[2]`, `s[2:3]` and `s[2:5]`, along with the type and length of each. Then write in your notebook: which two of the three printed the same thing, and what would the answer have been if `s` had been a list instead?

---

## Iteration 11 — A string cannot be changed

**a. What we set up**

```python
# string_immutable.py
word: str = "Karyashala"
word[0] = "M"
```

Then, after you have seen what happens, comment that line out and try this one instead:

```python
word[0:2] = "Ma"
```

**b. Task**

Iteration 8 put a slice on the left of `=` and it worked. Predict what each of these two lines does. Do you expect them to behave the same as each other, or differently?

**c. Observation (what you should find)**

Both lines give the same error:

```
Traceback (most recent call last):
  File "/home/student/task33/string_immutable.py", line 3, in <module>
    word[0] = "M"
    ~~~~^^^
TypeError: 'str' object does not support item assignment
```

A string is **immutable** — the word from Task 31, where you met it on tuples. Once made, it cannot be modified, and that applies whether you aim at one position or at a stretch. Iteration 8 has no string version.

Read the error carefully, because it names the right thing. It is a **`TypeError`**, not an `IndexError`. Position 0 certainly exists. The complaint is not "there is nothing there" but "**this kind of container does not do that at all**" — the same distinction you met in Task 31 when a set refused `[0]`.

So how do you change a letter? You do not. You **build a new string** out of slices:

```python
word: str = "Karyashala"
print("M" + word[1:])
print(word[:2] + "X" + word[3:])
```
```
Maryashala
KaXyashala
```

Read the second one as three pieces: everything before position 2, then the new letter, then everything from position 3 onward. Position 2 is the one that gets left out — it appears in neither slice, which is exactly how it gets replaced.

Notice that the original is untouched. Print `word` afterwards and it is still `Karyashala`. Every string operation works this way: it hands you a new string and leaves the old one alone.

Now put the whole task's containers in one row:

| | ordered? | slicing reads it? | slicing can change it? |
|---|---|---|---|
| `list` | yes | yes | **yes** — `x[1:3] = [...]` |
| `str` | yes | yes | **no** — `TypeError` |
| `tuple` | yes | yes | no |
| `set`, `dict` | no | **no** — no positions | — |

Slicing is a feature of **ordered** containers. Assigning through a slice is a feature of ordered containers that are also **mutable** — which, of everything you have met, is only the list.

**Takeaway to say out loud:** "A string is immutable, so I cannot assign into it at a position or a stretch — I build a new string out of slices instead."

**Write it yourself**

**W11.1** Take the string `"Hyderabad"`. Try to change its first letter directly, run it, and copy the exact error name and message into your notebook. Then produce each of these as a new string using slices and `+`: with the first letter replaced, with the last letter removed, with the middle three letters replaced by `"---"`. Print the original at the end to show it is unchanged.

**W11.2** Write a program that starts from `"2026-08-18"` and prints it as `"18-08-2026"`, using only slices and `+`. Then write in your notebook which of today's iterations would have let you do this with a single assignment if the date had been stored as a list instead.

---

## Iteration 12 — `range` takes the same three numbers

You have used `range` since Task 31 — but only ever with **one** number, as `range(3)` or `range(len(marks))`. It takes the same three as a slice.

**a. What we set up**

```python
# ranges.py
print(list(range(5)))
print(list(range(2, 7)))
print(list(range(1, 10, 2)))
print(len(range(2, 7)))
print(range(2, 7))
print(type(range(2, 7)))
```

**b. Task**

The first line is the form you know. Predict the second and third by applying the rule you have used all day. In particular, for `range(2, 7)`: does the `7` come out?

**c. Observation (what you should find)**

```
[0, 1, 2, 3, 4]
[2, 3, 4, 5, 6]
[1, 3, 5, 7, 9]
5
range(2, 7)
<class 'range'>
```

**The `7` does not come out** — for the very same reason the `4` in `marks[1:4]` did not. In Task 31 you already said this about `range(3)`: *up to but not including 3*. That sentence was never really about `range`; it was this rule, met early.

Put the two side by side and there is nothing left to learn:

```
marks[2:7]           start at 2, stop before 7, step 1
range(2, 7)          start at 2, stop before 7, step 1

marks[1:10:2]        start at 1, stop before 10, step 2
range(1, 10, 2)      start at 1, stop before 10, step 2
```

Same three numbers, same order, same rule. Even the length formula carries over: `len(range(2, 7))` is `5`, which is `7 - 2`, exactly as `len(marks[1:4])` was `4 - 1`.

The one difference is what happens when a number is missing. A slice fills a gap with "the far end"; a range has no container to be at the end of, so it fills in fixed defaults instead — **start `0`, step `1`** — and the stop is the one number that can never be left out. That is why `range(5)` means `0` up to `5`, and why there is no such thing as `range()`.

**The last two lines are the surprise.** `print(range(2, 7))` did not print the numbers. It printed `range(2, 7)`, and the type is `range` — its own type, and **not a list**.

A range does not hold the numbers. It holds the three numbers you gave it and works out each value as it is asked for — a *recipe* for numbers rather than the numbers themselves. That is why `list(...)` has been wrapped around every one of these lines: `list()` is what runs the recipe and collects the results.

It costs nothing to make a huge one:

```python
big = range(1000000)
print(len(big))
print(big[0])
print(999 in big)
```
```
1000000
0
True
```

A list of a million ints would occupy real memory; this range holds three numbers. And as those last lines show, you can still ask a range its length, index it, and test membership with `in` — it behaves like an ordered container without ever building one.

**Takeaway to say out loud:** "`range(start, stop, step)` is the same three numbers as a slice under the same rule — but a range is a recipe for numbers, not a list of them."

**Write it yourself**

**W12.1** Using `list(range(...))` each time, print: the numbers 1 to 10, the numbers 0 to 20 in fives, the even numbers below 12, and the numbers 5 to 8. Beside each in your notebook, write the slice that would take those same positions out of a long list.

**W12.2** Write a program that prints `range(3, 12, 2)` directly, then prints it wrapped in `list()`, then prints its type and its length. In your notebook, work out the length by hand from the three numbers before running, and write one sentence explaining why the first two lines look so different.

---

## Iteration 13 — A range that counts down

**a. What we set up**

```python
# countdown.py
print(list(range(10, 0, -1)))
print(list(range(2, 7, -1)))
```

**b. Task**

The first line counts down from 10. Predict its **last** number — is it `1` or `0`? Then predict the second line, remembering Iteration 6.

**c. Observation (what you should find)**

```
[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
[]
```

**The countdown ends at `1`, not `0`.** The stop is excluded going down just as it was going up. If you want a countdown that reaches `0` you must write `range(10, -1, -1)` — stop *before* `-1`.

This is the most common mistake made with a counting-down range, and now you can see it is not a special rule to memorise. It is the same exclusion you have applied since Iteration 1.

**The second line is empty**, and you have seen this exact failure before. `marks[2:5:-1]` gave `[]` in Iteration 6 because a backwards step needs its numbers the other way round. `range(2, 7, -1)` fails identically: start at 2, walk downwards, stop before 7 — it is already past the stop before it begins.

The mirroring is complete:

| | forwards | backwards | numbers disagree with the step |
|---|---|---|---|
| slice | `marks[0:3]` | `marks[5:2:-1]` | `marks[2:5:-1]` → `[]` |
| range | `range(0, 3)` | `range(10, 0, -1)` | `range(2, 7, -1)` → `[]` |

And the zero step is refused by both, in almost the same words:

```python
marks[::0]        # ValueError: slice step cannot be zero
range(1, 10, 0)   # ValueError: range() arg 3 must not be zero
```

Two different wordings of one refusal. Neither is an `IndexError`: nothing is out of range, the value simply cannot be used.

**Takeaway to say out loud:** "Counting down, the stop is still excluded — `range(10, 0, -1)` stops at 1 — and numbers that disagree with the step give an empty result, not an error."

**Write it yourself**

**W13.1** Print, using `list(range(...))`: 10 down to 1, 10 down to 0, 20 down to 10 in twos, and 5 down to 5. Write down the last number of each before running, and note in your notebook which of the four came out empty and why.

**W13.2** Write a loop that prints a countdown from 5 to 1 and then prints `"Start!"`. Then write a second version that prints the same countdown using a **list and a slice** instead of a range. In your notebook write one sentence on which of the two you find easier to read.

---

## Iteration 14 — Slice or range?

**a. What we set up**

```python
# which_one.py
marks: list[int] = [82, 91, 77, 68, 95, 73]

print(marks[1:5:2])

for i in range(1, 5, 2):
    print(i, marks[i])
```

The same three numbers, `1`, `5` and `2`, used both ways on the same list.

**b. Task**

Predict the output of both parts, and — before running — write down in one sentence what you think the difference between them is.

**c. Observation (what you should find)**

```
[91, 68]
1 91
3 68
```

Both reached the same two elements. They handed you different things.

**The slice gave you the elements**, collected into a new list: `[91, 68]`. It never mentions the positions 1 and 3 at all.

**The range gave you the positions**, `1` and `3`, one at a time — and you had to write `marks[i]` yourself to turn each into an element.

```
marks[1:5:2]           →  [91, 68]        the stretch itself
range(1, 5, 2)         →  1, 3            the positions of that stretch
```

That is the whole choice, and it decides which one to reach for:

- **Want the values? Slice.** `marks[:5]`, `word[-3:]`, `readings[::-1]`. Shorter, and it says what you mean.
- **Want the numbers themselves? Range.** Counting, numbering lines from 1, generating 1 to 100 — cases where no container exists to slice.
- **Want both the position and the value?** Neither alone is comfortable. `range(len(marks))` is the Task 31 idiom, and there is a better tool for it that you will meet later.

The advice from Task 31 still stands and now has a third member. Use `for m in marks` when you want the elements one at a time; use a **slice** when you want a stretch of them together; use `range` when what you actually want is numbers.

**Everything today, in one table:**

| | list | string | range |
|---|---|---|---|
| whole thing | `x[:]` | `s[:]` | `range(n)` |
| first three | `x[:3]` | `s[:3]` | `range(3)` |
| from 3 on | `x[3:]` | `s[3:]` | — needs a stop |
| last three | `x[-3:]` | `s[-3:]` | — no "end" to count from |
| every second | `x[::2]` | `s[::2]` | `range(0, n, 2)` |
| reversed | `x[::-1]` | `s[::-1]` | `range(n-1, -1, -1)` |
| stop excluded | yes | yes | yes |
| out of range | `[]` | `''` | — |
| what you get back | a new `list` | a new `str` | a `range` |
| can you assign into it? | **yes** | no | no |

**Takeaway to say out loud:** "A slice hands me the elements, a range hands me the positions — same three numbers, different thing to work with."

**Write it yourself**

**W14.1** With a list of eight names, print the third to sixth names twice: once with a single slice, and once with a `for` loop over a range that prints the same names one per line. Write both sets of three numbers in your notebook and confirm they are identical.

**W14.2** For each of these five, write down in your notebook whether you would use a slice or a range, and one sentence saying why — then write the line: the last five readings from a long list; the numbers 1 to 100; every second letter of a word; numbering a list of names from 1; the whole of a list in reverse.

---

## Practice — Predict the output

Write your answers in your notebook **before** you run anything. Use this list for P1–P8 and this string for P9–P12:

```python
data: list[int] = [10, 20, 30, 40, 50, 60, 70, 80]
text: str = "programming"
```

**P1** `print(data[2:5])`

**P2** `print(len(data[1:6]))`

**P3** `print(data[:4] + data[4:] == data)`

**P4** `print(data[-2:])`

**P5** `print(data[3:1])`

**P6** `print(data[5:100])`

**P7** `print(data[::3])`

**P8** `print(data[::-1][:3])`

**P9** `print(text[:7])`

**P10** `print(text[-4:])`

**P11** `print(len(text[2]))`

**P12** `print(text[::-1])`

**P13** `print(list(range(3, 9)))`

**P14** `print(list(range(9, 3, -2)))`

**Self-check.** Now run each one. For every answer you got wrong, write down which of the three numbers you misread — the start, the stop or the step — and which iteration explains it. P8 is worth a second look even if you got it right: work out what the first slice produces before you read the second one.

---

## One-page reference

**The three numbers**

```
x[start : stop : step]
    │      │      │
    │      │      └─ how far to move each time (default 1; may not be 0)
    │      └──────── stop BEFORE this position   (default: the far end)
    └─────────────── start AT this position      (default: the near end)
```

**The rules, all of them**

- The start is **included**, the stop is **excluded** — so `len(x[a:b])` is `b - a`.
- A missing number means the far end on that side.
- Negative numbers name positions from the right; they do **not** reverse anything.
- A negative step reverses the direction — and then the two numbers must be swapped too.
- Out of range is **empty**, never an error. `x[i]` out of range **is** an error.
- A slice always returns a **new container of the same kind**.

**Common slices worth knowing by heart**

| want | write |
|---|---|
| first `n` | `x[:n]` |
| last `n` | `x[-n:]` |
| all but the last | `x[:-1]` |
| all but the first | `x[1:]` |
| middle, dropping one each end | `x[1:-1]` |
| every second | `x[::2]` |
| reversed | `x[::-1]` |
| a copy | `x[:]` |

**Only on a list (mutable)**

```python
x[1:3] = [20, 30, 40]    # replace a stretch; length may change
x[1:3] = []              # remove a stretch
del x[1:3]               # remove a stretch
```

On a `str` or a `tuple` all three raise `TypeError` — those are **immutable**; build a new one with `+` instead.

**`range` — the same three numbers**

```python
range(stop)                 # start 0, step 1
range(start, stop)          # step 1
range(start, stop, step)
```

- The stop is excluded, going up **or** down: `range(10, 0, -1)` ends at `1`.
- A range is **not a list** — `print(range(2,7))` shows `range(2, 7)`. Wrap it in `list()` to see the numbers.
- It still supports `len()`, `in` and indexing, without ever building the numbers.

**Which to use**

| you want | use |
|---|---|
| a stretch of the elements | a **slice** — `x[1:5]` |
| the positions, or just numbers | a **range** — `range(1, 5)` |
| each element, one at a time | `for e in x:` (Task 31) |

**The errors**

| what you wrote | error |
|---|---|
| `x[99]` on a short list | `IndexError: list index out of range` |
| `x[::0]` | `ValueError: slice step cannot be zero` |
| `range(1, 10, 0)` | `ValueError: range() arg 3 must not be zero` |
| `s[0] = "M"` on a string | `TypeError: 'str' object does not support item assignment` |
| `s[0:2] = "Ma"` on a string | `TypeError: 'str' object does not support item assignment` |

---

## Real-World Problem — A day of sensor readings

A temperature sensor writes one reading every hour. A day's file arrives as a list whose **first element is a label**, not a reading:

```python
day: list[str] = ["2026-08-18", "27", "26", "26", "25", "25", "24", "24", "25",
                  "28", "31", "33", "34", "35", "35", "34", "32", "30", "29",
                  "28", "28", "27", "27", "26", "26"]
```

That is one label followed by 24 hourly readings, from hour 0 to hour 23.

**Write one program that prints, each part clearly labelled:**

1. The date, taken out on its own.
2. How many readings there are — not how long the list is.
3. The readings only, with the label removed, as a list.
4. The first six readings (the small hours) and the last six (the late evening).
5. The reading at hour 12, reached from the readings list.
6. Every third reading across the day.
7. The whole day's readings newest-first.
8. The readings for hours 11, 12 and 13 — the hottest part of the afternoon — as one slice.
9. The date printed as `18-08-2026`, built from slices of the date string.
10. Each of the first five readings printed on its own line as `hour 0: 27`, numbering the hours with a range.
11. The last five readings printed one per line, **counting down** from hour 23 to hour 19.
12. A second list holding the readings with the label removed, in which you then replace hours 0 and 1 with a single corrected value — and print the length of both lists afterwards to show which one changed.

**Then, in your notebook:**

- Parts 3 and 12 both remove the label. One of them left the original list untouched and one did not have to. Which is which, and which iteration explains it?
- Part 2 asks for a count that is not `len(day)`. Write down the two ways you could get it, and say which one keeps working if a second label is added at the front.
- Part 10 used a range and part 4 used a slice, on the same data. Write one sentence for each saying why that was the right choice.
- If the file arrived with the label at the **end** instead of the front, which of your twelve answers change? Write the new slice for each one that does.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| slice | స్లైస్ | కంటైనర్ నుండి ఒక భాగాన్ని తీసుకోవడం — `x[1:4]` |
| stretch / range of positions | వరుస భాగం | ఒకదాని తర్వాత ఒకటిగా ఉన్న స్థానాల గుంపు |
| start | ప్రారంభం | మొదటి సంఖ్య — ఇక్కడి నుండి మొదలు (కలుపుకొని) |
| stop | ఆపు స్థానం | రెండవ సంఖ్య — దీనికి **ముందే** ఆగిపోతుంది |
| step | అడుగు | మూడవ సంఖ్య — ప్రతిసారీ ఎంత దూరం కదలాలి |
| half-open | సగం-తెరిచిన | మొదటిది కలుపుకొని, రెండవది వదిలేసి |
| inclusive | కలుపుకొని | ఆ స్థానం లోపల ఉంటుంది — start |
| exclusive | వదిలేసి | ఆ స్థానం రాదు — stop |
| default | డిఫాల్ట్ | రాయకపోతే వాడే విలువ |
| copy | కాపీ | కొత్తగా తయారైన వేరే కంటైనర్ |
| reverse | తిరగేయడం | వెనుక నుండి ముందుకు — `x[::-1]` |
| empty | ఖాళీ | ఏమీ లేని కంటైనర్ — `[]` లేదా `''` |
| out of range | పరిధి దాటి | ఉన్న స్థానాల కంటే బయట |
| mutable | మార్చగలిగేది | తయారైన తర్వాత మార్చవచ్చు — లిస్ట్ |
| immutable | మార్చలేనిది | తయారైన తర్వాత మార్చలేము — స్ట్రింగ్, ట్యూపుల్ |
| ordered container | క్రమం ఉన్న కంటైనర్ | స్థానాలు ఉన్నది — స్లైసింగ్ దీనికే పనిచేస్తుంది |
| character | అక్షరం | ఒక్క అక్షరం — పైథాన్‌లో ఇది కూడా స్ట్రింగే |
| `range` | రేంజ్ | సంఖ్యలను తయారుచేసే విధానం — లిస్ట్ కాదు |
| recipe (for numbers) | తయారీ విధానం | సంఖ్యలను నిల్వ చేయకుండా అవసరమైనప్పుడు లెక్కించేది |
| `IndexError` | ఇండెక్స్ ఎర్రర్ | ఆ స్థానం లేదు — ఇండెక్స్‌కి మాత్రమే వస్తుంది |
| `ValueError` | వాల్యూ ఎర్రర్ | విలువ వాడటానికి పనికిరాదు — step సున్నా |
| `TypeError` | టైప్ ఎర్రర్ | ఈ రకానికి ఆ పనే చేతకాదు — స్ట్రింగ్‌ను మార్చడం |
