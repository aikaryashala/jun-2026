# Collections in Python — Question Bank

Answer on paper, using the worksheet's ideas: a **collection** groups many elements under one name; an **ordered** collection gives every element a position and an **unordered** one does not; a container is reached either by **index** or by **key**; `for … in` walks any of them; and in Python the **indentation is the block**.

Every value in this paper is an **int** or a **string**.

Answers are **not** in this file.

---

# Part A — Multiple Choice

**A1.** In general programming terms, a collection is:

- A) a list, and only a list
- B) any object that groups multiple elements together under one name
- C) a group of variables that all have the same type
- D) a Python module you must import

**A2.** "Unordered collection" means:

- A) the elements are stored in random order and shuffled each time you look
- B) the elements have no position, so asking for "the third one" is meaningless
- C) the elements are sorted automatically
- D) the elements are stored backwards

**A3.** `marks = [82, 91, 77]`. What does `print(marks)` display?

- A) `82 91 77`
- B) `[82, 91, 77]`
- C) `82, 91, 77`
- D) `<class 'list'>`

**A4.** Which is the biggest practical difference between a C array and a Python list?

- A) a C array cannot hold integers
- B) a Python list is always sorted
- C) a Python list knows its own length and can grow; a C array does neither
- D) a C array is reached by key, a Python list by index

**A5.** In `for m in marks:`, the name `m`:

- A) must always be called `m`
- B) holds the whole list on every turn
- C) holds each element in turn, and still exists after the loop
- D) is deleted by Python when the loop finishes

**A6.** What decides which statements belong to a `for` loop's body in Python?

- A) curly brackets `{ }`
- B) the indentation
- C) the `end` keyword
- D) a semicolon at the end of the last line

**A7.** A program has `for m in marks:` and the next line is not indented. What happens?

- A) the loop runs with an empty body
- B) `SyntaxError` while the loop runs
- C) `IndentationError`, before anything runs
- D) it runs normally; indentation is only a style choice

**A8.** `data = [10, 20, 30, 40]`. Which index gives the last element?

- A) `data[4]`
- B) `data[len(data)]`
- C) `data[3]`
- D) `data[-0]`

**A9.** `nums = [1, 2, 3]`. `print(nums[3])` raises:

- A) `KeyError`
- B) `IndexError`
- C) `TypeError`
- D) `ValueError`

**A10.** `range(4)` produces:

- A) `1, 2, 3, 4`
- B) `0, 1, 2, 3`
- C) `0, 1, 2, 3, 4`
- D) `4` only

**A11.** `t = (1, 2, 3)`. Which of these raises an error?

- A) `print(t[0])`
- B) `print(len(t))`
- C) `for x in t: print(x)`
- D) `t[0] = 9`

**A12.** `s = {4, 2, 4, 9, 2}`. What is `len(s)`?

- A) 5
- B) 3
- C) 2
- D) 4

**A13.** Why can you not write `s[0]` for a set `s`?

- A) sets are always empty at position 0
- B) a set has no order, so it has no positions
- C) sets only allow string indexes
- D) you must use `s.0` instead

**A14.** `d = {"name": "Ravi", "marks": 82}`. What does `for k in d:` give you on each turn?

- A) the keys
- B) the values
- C) both, as a pair
- D) the numbers 0 and 1

**A15.** `d = {"name": "Ravi", "marks": 82}`. What is `print("Ravi" in d)`?

- A) `True`
- B) `False`
- C) `KeyError`
- D) `TypeError`

**A16.** `d = {"a": 1, "b": 2, "c": 3}`. What is `len(d)`?

- A) 6
- B) 3
- C) 1
- D) 2

**A17.** `grid = [[1, 2, 3], [4, 5, 6]]`. What is `len(grid)`?

- A) 6
- B) 3
- C) 2
- D) 5

**A18.** `print(type({}))` displays:

- A) `<class 'set'>`
- B) `<class 'dict'>`
- C) `<class 'list'>`
- D) `<class 'tuple'>`

**A19.** `t = (1, 2)` and then `t.append(3)`. Which error?

- A) `TypeError`
- B) `IndexError`
- C) `AttributeError`
- D) no error — the tuple becomes `(1, 2, 3)`

**A20.** `nums = [1, 2]` and then `nums.add(3)`. Why does this fail?

- A) a list cannot grow
- B) `add` is the set's word for growing; a list uses `append`
- C) `3` is the wrong type
- D) the list must be empty first

**A21.** `marks = [82, 91]`. `print(marks["0"])` raises `TypeError: list indices must be integers or slices, not str`. The problem is:

- A) there is no element at position `"0"`
- B) a list position must be a number, and `"0"` is a string
- C) the list is too short
- D) lists cannot be printed

**A22.** Which of these can be used as a dictionary key?

- A) `[1, 2]`
- B) `(1, 2)`
- C) `{1, 2}`
- D) all three

**A23.** `TypeError: unhashable type: 'list'` when you write `{[1, 2]: "x"}` tells you that a key must be:

- A) a string
- B) a number
- C) immutable
- D) shorter than the value

**A24.** `n = 5` and then `for x in n:`. Which message?

- A) `'int' object is not iterable`
- B) `'int' object is not subscriptable`
- C) `IndexError: int index out of range`
- D) no error — it loops five times

**A25.** What is the difference between `AttributeError` and `TypeError`?

- A) they are the same error with two names
- B) `AttributeError` means the ability does not exist on that type; `TypeError` means the operation exists but not for these types
- C) `AttributeError` happens before the program runs
- D) `TypeError` only happens with numbers

**A26.** `d = {"a": 1}`. Running `for k in d:` with `d["b"] = 2` inside the loop raises:

- A) `KeyError`
- B) `ValueError`
- C) `RuntimeError`
- D) nothing — it adds the key

**A27.** A program prints a wrong answer instead of crashing. Compared with a program that crashes, this is:

- A) better, because the program finished
- B) the same — both are equally easy to find
- C) worse, because nothing tells you the bug is there
- D) impossible in Python

---

# Part B — Fill in the Blanks

**B1.** A collection in which every element has a position is called an ____________ collection; one in which they have no position is called an ____________ collection.

**B2.** Reaching an element by its position is called ____________ access; reaching it by a name you chose is called ____________ access.

**B3.** The function that tells you how many elements a container holds is ____________.

**B4.** In a list of 5 elements, the first index is ____________ and the last is ____________.

**B5.** In Python, the thing that decides which statements are inside a loop's body is the ____________.

**B6.** A `for` loop's first line must end with a ____________.

**B7.** The general name for a container that is ordered but cannot be changed after it is built is an ordered ____________ sequence — in Python this is the ____________.

**B8.** A container that keeps each element only once and has no order is a ____________.

**B9.** The general name for a Python dictionary, used in most other languages, is an ____________ ____________ (or a *map*).

**B10.** Looking up a key that is not in a dictionary raises a ____________, while asking for a position that does not exist in a list raises an ____________.

**B11.** The operator that asks "is this element in this container?" is ____________, and it answers with ____________ or ____________.

**B12.** `{}` creates an empty ____________. To create an empty set you must write ____________.

**B13.** The error raised when a type has no such ability — for example `.append()` on a tuple — is called an ____________.

**B14.** Only ____________ values can be used as set elements or dictionary keys, which is why a ____________ can be a key but a ____________ cannot.

**B15.** The list's word for adding one element is ____________, while the set's word is ____________.

**B16.** Adding a key to a dictionary while a `for` loop is walking it raises a ____________.

**B17.** When reading any error message, the ____________ tells you the category and the ____________ usually names the offending type or value.

---

# Part C — Scenario Questions

**C1.** A student writes this and expects three lines of output:
```python
marks = [82, 91, 77]
print(marks)
```
(a) What actually prints?
(b) Why does the output look like that?
(c) Write the two lines that would print the three marks on three separate lines.

**C2.** Two programs, differing only in the indentation of one line:
```python
# Program 1                  # Program 2
for n in [1, 2, 3]:          for n in [1, 2, 3]:
    print(n)                     print(n)
    print("*")               print("*")
```
(a) How many lines does each print?
(b) Explain which statement belongs to the loop in each.
(c) In C, what would play the role that indentation plays here?

**C3.** A student wants the position as well as the element, and writes:
```python
marks = [82, 91, 77]
i = 0
while i <= len(marks):
    print(i, marks[i])
    i = i + 1
```
(a) What is printed before it crashes, and with which error?
(b) Which single character is wrong?
(c) Rewrite it as a `for` loop that still gives both the position and the element.

**C4.** A student says: "A tuple is useless — it is just a list that can do less."
(a) Name three operations that behave *identically* on a list and a tuple.
(b) Name the one that does not, and the error it raises.
(c) Give two reasons a programmer would deliberately choose the container that can do less.

**C5.** A program collects the home city of every student in a batch of 13. Several students come from the same city. The program must answer, quickly and repeatedly, "does anybody come from Warangal?", and must list each city once.
(a) Which container should hold the cities, and why?
(b) Why is a list a poor fit for this job — give both reasons.
(c) What must the programmer *not* assume about how this container prints?

**C6.** A student runs the same program three times and gets three different outputs:
```python
cities = {"delhi", "hyderabad", "chennai"}
print(cities)
```
(a) Is the program broken? Explain what is happening.
(b) Why does this not happen with a list?
(c) What should they write if they need the same order every time?

**C7.** A student has `student = {"name": "Ravi", "marks": 82}` and writes `print(student[0])`, expecting `"Ravi"`.
(a) What happens, and with which error?
(b) Explain why, in terms of how a dictionary is reached.
(c) The square brackets look the same as a list's. What is different about what goes *inside* them?

**C8.** A student needs to store, for each of three students, a name and a list of two marks.
(a) Describe the container arrangement you would use, naming the container at each level.
(b) Write the expression that reads the *second* mark of the *third* student.
(c) Write the nested loop that prints every student's name once, followed by each of their marks.

**C9.** For each, name the container you would choose and give the property that decides it:
(a) the days of the week, in order
(b) the set of distinct letters used in a word
(c) a person's name, age and city stored together
(d) a fixed `(row, column)` pair for a square on a chessboard

**C10.** A student sees the word "collection" in two places: in this worksheet's title, and in the line `import collections` in somebody else's Python program.
(a) Are these the same thing?
(b) What is the general programming meaning of the word?
(c) What is the thing that `import collections` brings in?

**C11.** A student is storing a score for each `(row, column)` square of a board. They try both of these:
```python
scores = {[0, 0]: 5}
scores = {(0, 0): 5}
```
(a) Which one fails, and with which exact message?
(b) Explain the reason in terms of what a set and a dictionary do to find an element quickly.
(c) This question finally answers something from earlier in the worksheet — what is the practical reason for a container that cannot be changed?

**C12.** Two students write the same loop over a dictionary. Neither uses `.items()`.
```python
# Student 1
d = {"name": "Ravi", "city": "Warangal"}
for k, v in d:
    print(k, v)

# Student 2
d = {"ab": 1, "cd": 2}
for k, v in d:
    print(k, v)
```
(a) What happens to Student 1?
(b) What happens to Student 2?
(c) Both wrote the same mistake. Explain why the outcomes differ, and say which student is in more trouble and why.
(d) Give the one-word fix that corrects both.

**C13.** A student meets these three errors in one afternoon:
```
AttributeError: 'list' object has no attribute 'add'
TypeError: 'set' object is not subscriptable
KeyError: 'age'
```
(a) For each, say in one sentence what the student was probably trying to do.
(b) Two of the three are about using one container's habits on another. Which two?
(c) What single piece of advice would prevent all three?

---

# Part D — Predict the Output

Write the **exact** output. If a snippet crashes, give everything printed before the crash *and* the error name.

**D1.**
```python
for c in ["a", "b"]:
    print(c)
    print(c)
print("end")
```

**D2.**
```python
nums = [3, 6, 9]
print(nums[0])
print(nums[-1])
print(len(nums))
print(nums[len(nums)])
```

**D3.**
```python
i = 0
while i < 3:
    print(i)
    i = i + 1
print("i is", i)
```

**D4.**
```python
letters = ["p", "q", "r"]
for i in range(len(letters)):
    print(i, letters[i])
```

**D5.**
```python
x = [1, 2]
x[0] = 7
x.append(3)
print(x)
print(len(x))
```

**D6.**
```python
t = ("a", "b")
for v in t:
    print(v)
print(len(t))
t[0] = "c"
```

**D7.**
```python
s = {2, 4, 2, 6, 4, 2}
print(len(s))
print(4 in s)
print(5 in s)
```

**D8.**
```python
d = {"x": 10, "y": 20}
for k in d:
    print(k)
for v in d.values():
    print(v)
for k, v in d.items():
    print(k, v)
```

**D9.**
```python
d = {"a": 1, "b": 2}
print("a" in d)
print(1 in d)
print(len(d))
print(d["c"])
```

**D10.**
```python
box = [[1, 2], [3, 4], [5, 6]]
print(len(box))
print(box[2])
print(box[2][0])
print(box[0][1])
```

**D11.**
```python
box = [["a", "b"], ["c", "d"]]
for row in box:
    for ch in row:
        print(ch)
    print("|")
```

**D12.**
```python
rec = [{"n": "Ravi", "m": [8, 9]}, {"n": "Asha", "m": [7, 6]}]
print(len(rec))
print(rec[0]["n"])
print(rec[1]["m"])
print(rec[1]["m"][1])
```

**D13.** For each line, give the type that `type()` would report.
```python
print(type([1, 2]))
print(type((1, 2)))
print(type({1, 2}))
print(type({1: 2}))
print(type({}))
```

**D14.** Four lines. Say which crash, and with which error name.
```python
print([1, 2][1])
print((1, 2)[5])
print({1, 2}[0])
print({"k": 1}["k"])
```

**D15.** For each line give the error name, or write "no error" and the output.
```python
print([1, 2] + [3, 4])
print([1, 2] + 3)
print((1, 2) + (3,))
print([1, 2] + (3, 4))
```

**D16.** Give the error name for each.
```python
t = (1, 2); t.append(3)
s = {1, 2}; s.append(3)
lst = [1, 2]; lst.add(3)
lst = [1, 2]; lst.append(3)
```

**D17.**
```python
scores = {(1, 1): "a", (2, 2): "b"}
print(len(scores))
print(scores[(1, 1)])
for k in scores:
    print(k)
```

**D18.** What is printed, and is there an error?
```python
d = {"pq": 5, "rs": 6}
for k, v in d:
    print(k, "and", v)
```

**D19.** Name the error for each line.
```python
nums = [1, 2, 3]
nums.remove(1)
nums.remove(1)
```

**D20.** Which line crashes and with which error? What was printed first?
```python
marks = [82, 91]
print(len(marks))
print(marks[0])
print(mark[0])
```
