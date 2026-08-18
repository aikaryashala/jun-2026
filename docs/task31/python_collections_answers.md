# Collections in Python — Answer Key

Check the **reasoning**, not just the letter. Predicting output is a skill you build by finding out *which rule* you forgot — the letter on its own teaches you nothing.

Everything here comes from five ideas: a collection groups many elements under one name; **ordered** means every element has a position; you reach an element either by **index** or by **key**; the **indentation is the block**; and each container is defined by how it answers *ordered? changeable? duplicates?*

---

# Part A — Multiple Choice

**A1 — B) any object that groups multiple elements together under one name.**
The general term, not a Python one. A C array is a collection; so are all four Python containers. Option C describes a C array specifically — a Python list is happy to be heterogeneous. Option D confuses the general word with the `collections` module (C10).

**A2 — B) the elements have no position, so asking for "the third one" is meaningless.**
Not random (A) and not sorted (C) — simply *positionless*. This is why a set has no `[0]`: there is nothing for the 0 to refer to. A set does not shuffle its contents; it never had an order to shuffle.

**A3 — B) `[82, 91, 77]`.**
Printing a container shows the container — brackets, commas and all — not its elements one at a time. Getting `82`, `91`, `77` on separate lines needs a loop.

**A4 — C) a Python list knows its own length and can grow; a C array does neither.**
Both are the direct consequence of the same design difference. In C you carry the length yourself in a separate variable and pass it around, and the size is fixed at declaration — growing means allocating new memory and copying. `len(marks)` and `marks.append(60)` replace all of that.

**A5 — C) holds each element in turn, and still exists after the loop.**
`m` is a name you chose (so A is wrong) that Python assigns each element to in turn (so B is wrong). And it is an ordinary variable — Python does not tidy it away, so after the loop it holds the **last** element. That is the part most people get wrong.

**A6 — B) the indentation.**
The single biggest difference from C. There are no braces; the indentation is not a convention for readers, it is the syntax. Two programs with identical statements in identical order can be different programs (C2).

**A7 — C) `IndentationError`, before anything runs.**
The colon promises a block; not indenting one is a syntax problem, so Python fails while *reading* the file. Nothing runs and nothing prints — the same timing as Task 30's `SyntaxError`, of which `IndentationError` is a special case.

**A8 — C) `data[3]`.**
Four elements, so valid indexes are 0, 1, 2, 3 — the last is `len - 1`. `data[4]` and `data[len(data)]` are the same thing and both raise `IndexError`. `data[-0]` is a trap: `-0` is just `0`, so it gives the **first** element. Use `data[-1]` for the last.

**A9 — B) `IndexError`.**
"No such **position**." Contrast `KeyError`, which is "no such **key**" (A15, D9), and `TypeError`, which is "this kind of container does not do that at all" (A11, A13).

**A10 — B) `0, 1, 2, 3`.**
Four numbers, starting at 0, *up to but not including* 4. That is exactly the set of valid indexes for a container of length 4 — which is why `range(len(x))` is the idiom for looping over positions.

**A11 — D) `t[0] = 9`.**
`TypeError: 'tuple' object does not support item assignment`. Everything else on the list — reading `t[0]`, `len(t)`, walking it with `for … in` — behaves exactly as it would for a list. A tuple is a list with precisely one ability removed.

**A12 — B) 3.**
The distinct elements are `4`, `2` and `9`. The repeats were never stored — a set does not remove duplicates afterwards, it declines to keep a second copy.

**A13 — B) a set has no order, so it has no positions.**
And with no positions there is nothing for `[0]` to name. Note the error confirms it is about the *type*, not the number: `TypeError: 'set' object is not subscriptable`, not "index out of range". *Subscript* is the formal name for the `[…]` operation.

**A14 — A) the keys.**
The default that surprises people. It is a sensible one — given a key you can always fetch the value with `d[k]` — but if you want the values say `d.values()`, and for both say `d.items()`.

**A15 — B) `False`.**
`"Ravi"` is a **value**, and `in` on a dictionary tests the **keys** only. Same rule as A14: plain access to a dictionary means its keys. `"name" in d` would be `True`; to search the values you must write `"Ravi" in d.values()`.

**A16 — B) 3.**
Three pairs, three elements. One pair is one element — `len` does not count keys and values separately.

**A17 — C) 2.**
Two elements, each of which happens to be a list of three. `len` counts the top level only and does not look inside. Nested containers do not flatten.

**A18 — B) `<class 'dict'>`.**
The gotcha worth remembering: **empty curly brackets are an empty dictionary**, not an empty set. Dictionaries got the `{}` notation first. An empty set must be written `set()`.

**A19 — C) `AttributeError`.**
`'tuple' object has no attribute 'append'`. An *attribute* is anything reached with a dot, including abilities like `.append()`. A tuple is immutable, so no way of growing it exists on that type at all. Note it is not `TypeError`: nothing was mistyped, the method simply does not exist.

**A20 — B) `add` is the set's word for growing; a list uses `append`.**
`AttributeError: 'list' object has no attribute 'add'`. A list absolutely can grow (A is wrong) — just not by that name. Mixing up the two container's vocabularies is among the most common beginner errors, and the error message tells you exactly which type you were holding.

**A21 — B) a list position must be a number, and `"0"` is a string.**
Task 30's lesson in a new place: `0` and `"0"` look identical on screen and are different kinds of thing. Option A describes an `IndexError`, which this is not — the complaint is about the *kind* of thing in the brackets, not about it being out of range. The message names the type it got: `not str`.

**A22 — B) `(1, 2)`.**
A key must be **immutable**, and of the three only the tuple is. A list and a set are both changeable, so both raise `TypeError: unhashable type`.

**A23 — C) immutable.**
*Hashable* means a number can be worked out from the value's contents, which is how a set or a dictionary finds an element instantly instead of scanning. If the contents could change afterwards, the element would no longer be where it was filed, and would be lost. So Python only accepts values that cannot change: ints, strings and tuples.

**A24 — A) `'int' object is not iterable`.**
*Iterable* means `for … in` can walk it. Option B is the message you would get from `n[0]` instead — *subscriptable* is about the `[…]` operation. Both complaints amount to "that is a single value, not a container", but they name different operations.

**A25 — B) `AttributeError` means the ability does not exist on that type; `TypeError` means the operation exists but not for these types.**
Compare `t.append(4)` → `AttributeError` (tuples have no `append` whatsoever) with `t[0] = 9` → `TypeError` (assignment is a real operation, just not one a tuple supports). Both happen while the program runs.

**A26 — C) `RuntimeError`.**
`dictionary changed size during iteration`. The loop keeps track of where it has reached; adding or removing elements makes that position meaningless, so Python stops rather than silently skipping or repeating elements. Build a new container instead of resizing the one you are walking.

**A27 — C) worse, because nothing tells you the bug is there.**
The lesson behind C12 and P24, and behind Task 30's `"12" + "5"` giving `125`. A crash names the file, the line and the problem, and it happens the first time you run the code. A plausible wrong answer can survive for months. **A crash is a gift.**

---

# Part B — Fill in the Blanks

**B1** — **ordered** / **unordered**.

**B2** — **indexed** access / **key** access. (Positional and associative are also right.)

**B3** — **`len()`**.

**B4** — **0** and **4**. Five elements, indexes 0–4; the last is always `len - 1`.

**B5** — the **indentation**.

**B6** — a **colon** (`:`).

**B7** — an ordered **immutable** sequence — in Python, the **tuple**.

**B8** — a **set**.

**B9** — an **associative array** (or a *map*, or a *hash table*, or a *key–value store*).

**B10** — a **`KeyError`** for the dictionary; an **`IndexError`** for the list. Both mean "nothing there"; one is about a key, the other about a position.

**B11** — **`in`**, answering **`True`** or **`False`**.

**B12** — an empty **dictionary**. An empty set must be written **`set()`**.

**B13** — an **`AttributeError`**.

**B14** — only **immutable** (or *hashable*) values; a **tuple** can be a key, a **list** cannot.

**B15** — **`append`** for a list, **`add`** for a set.

**B16** — a **`RuntimeError`** (`dictionary changed size during iteration`).

**B17** — the **name** gives the category; the **message** names the offending type or value. (The `^^^` marks then show which part of the line failed.)

---

# Part C — Scenario Questions

### C1 — one line instead of three

**(a)** One line: `[82, 91, 77]`.

**(b)** `print` was given **one value** — the list itself — so it printed one thing. Python displays a container with its brackets and commas, which is how it shows you *"this is a list, and these are its contents"*. Nothing has gone wrong; the student asked for the container, not its elements.

**(c)**
```python
for m in marks:
    print(m)
```

### C2 — the same statements, twice

**(a)** Program 1 prints **six** lines (`1 * 2 * 3 *`); Program 2 prints **four** (`1 2 3 *`).

**(b)** In Program 1 `print("*")` is indented, so it is **inside** the body and runs on every one of the three turns. In Program 2 it is not indented, so it is **after** the loop and runs once, when the loop is finished. The statements and their order on the page are identical — the indentation alone makes them different programs.

**(c)** The braces `{ }`. In C the braces decide the body and the indentation is only a courtesy to human readers; in Python the indentation *is* the syntax and there are no braces. This is the one adjustment every C programmer has to make.

### C3 — the off-by-one

**(a)** It prints all three lines correctly and *then* crashes:
```
0 82
1 91
2 77
```
followed by `IndexError: list index out of range`.

**(b)** The `=` in `<=`. With `<=`, `i` reaches 3 and is allowed one more turn, but `marks[3]` does not exist — the valid indexes stop at `len - 1`. The test must be `i < len(marks)`.

**(c)**
```python
for i in range(len(marks)):
    print(i, marks[i])
```
`range(len(marks))` produces exactly `0, 1, 2` — the valid indexes and no more — so the off-by-one becomes impossible rather than merely avoided. Note this is the same bug you can write in C, with one difference: Python stops you with a clear error, while C computes another address and reads whatever is lying there.

### C4 — "a tuple is useless"

**(a)** Any three of: reading an element by index (`t[0]`); `len(t)`; walking it with `for … in`; testing membership with `in`; negative indexes (`t[-1]`); printing it. All behave exactly as for a list.

**(b)** Changing it. `t[0] = 99` raises `TypeError: 'tuple' object does not support item assignment`. Note *which* error: not "no such position" — position 0 exists and reads fine — but "this type has no such operation".

**(c)** Two of: **it states that the data is fixed**, and makes that a rule the language enforces rather than a promise a programmer must remember — a date `(2026, 8, 11)` or a coordinate `(3, 7)` is not meant to change; **it cannot be modified by accident** from some distant part of a large program; **it can be used in places a list cannot**, because Python trusts frozen things where it does not trust changeable ones. The general point: a container that can do less is *making a promise*, and the promise is the feature.

### C5 — the cities

**(a)** A **set**. Each city is kept exactly once however many students share it, the order is meaningless, and membership is the only question being asked — which is precisely what a set is for.

**(b)** Two reasons. **Duplicates:** a list would store Warangal once per student, so "list each city once" would need de-duplicating work of your own. **Speed:** `"Warangal" in cities` on a list means checking the elements one at a time until a match is found — the same scan you would write in C — whereas a set answers that question immediately, however many elements it holds. For a job that is *repeatedly* asking "is this in there?", that is the whole argument.

**(c)** That it has any **order**. The print order of a set is an accident of internal storage, it differs between runs for strings, and no program should depend on it. If a fixed order is needed, `sorted(cities)` returns an ordered list.

### C6 — three runs, three answers

**(a)** Not broken. A set is an **unordered** collection — it has no order to report, so what you see is an accident of how Python happened to store the elements internally, and for strings that arrangement deliberately differs from run to run. Three different printings are three equally correct views of the same set. (Which is the sharpest possible demonstration that "unordered" is a real property and not a technicality.)

**(b)** A list is an **ordered** collection: every element has a position that is part of the data, fixed when you build it. There is nothing for Python to vary.

**(c)** `print(sorted(cities))` — which returns a new **list**, in order, and prints the same way every time. Note it is a list that comes back, not a set: sorting a thing that has no order can only produce a different kind of container.

### C7 — `student[0]` on a dictionary

**(a)** It raises `KeyError: 0`.

**(b)** A dictionary is reached by **key**, not by position. It has no notion of "the element at position 0", so Python does the only thing the brackets can mean for a dictionary: it looks for an element filed under the key `0`. There isn't one, so `KeyError`. The correct expression is `student["name"]`.

**(c)** The notation is identical; what goes inside is completely different. For a list, the brackets hold a **position** — a number Python computes with, bounded by the length. For a dictionary they hold a **key** — a name the programmer invented, and it does not have to be a number at all. The same `[…]` means "the element at this position" in one case and "the element filed under this name" in the other.

### C8 — three students, names and marks

**(a)** A **list** at the top level (three students, order kept, could grow), each element a **dictionary** (named fields — `name` and `marks`), and the value under `"marks"` itself a **list** of two ints (ordered, may repeat).
```python
students = [
    {"name": "Ravi",   "marks": [82, 91]},
    {"name": "Asha",   "marks": [77, 65]},
    {"name": "Balaji", "marks": [90, 88]},
]
```

**(b)** `students[2]["marks"][1]` — three steps left to right: element 2 of the list (the third student, counting from 0), then the value under key `"marks"`, then element 1 of that list (the second mark).

**(c)**
```python
for s in students:
    print(s["name"])
    for m in s["marks"]:
        print(m)
```
The name is printed once per student because it sits at one level of indentation; the marks are printed inside the inner loop at two levels.

### C9 — choosing a container

**(a)** **A list** (or a tuple). Order is part of the meaning — Monday must come before Tuesday. A tuple is arguably better still, since the days of the week never change; that would make it an ordered *immutable* sequence.

**(b)** **A set.** The deciding property is **uniqueness** — each distinct letter once, however often it appears — and order is meaningless.

**(c)** **A dictionary.** These are **named fields**, not a sequence. `person["city"]` says what it means; `person[2]` would force the reader to remember that position 2 was the city.

**(d)** **A tuple.** It is a fixed pair, it should not change once made, and the *position* within it carries meaning (first is row, second is column) — an ordered, immutable sequence exactly.

### C10 — the word doing two jobs

**(a)** No — they are different things that unluckily share a word.

**(b)** The general programming term for **any container object that groups multiple elements together under one name**. It is not a Python word: C arrays are collections too, and every language has some.

**(c)** A built-in **module** in Python's standard library holding some extra, specialised containers — `Counter`, `defaultdict`, `deque` and a few more — built on top of the four in this worksheet. It is one particular library with a confusingly similar name, and you do not need it yet.

### C11 — a key for each square

**(a)** The first fails: `TypeError: unhashable type: 'list'`. The second works — `{(0, 0): 5}` is a perfectly good dictionary.

**(b)** A set and a dictionary can answer "is this in there?" immediately, without scanning, because they work out a number from the element's **contents** — a **hash** — and file it in the place that number points to. If the element could change after being filed, its contents would no longer match its location and the container would never find it again. So Python refuses changeable values outright rather than allowing a container that quietly loses things. A list is changeable; a tuple is not.

**(c)** **Only a frozen thing can be used as a key.** That is the concrete answer to "why would I want a container that can do less?" from Iteration 7. The tuple's inability to change is not a missing feature — it is the exact property that makes it usable here, and it is why `(row, column)` pairs are conventionally tuples.

### C12 — the same mistake, two outcomes

**(a)** Student 1 crashes: `ValueError: too many values to unpack (expected 2)`. Nothing is printed.

**(b)** Student 2 gets no error at all, and prints:
```
p and q
r and s
```
*(using their keys `"ab"`/`"cd"` the output is `a and b` / `c and d`)* — the values `1` and `2` appear nowhere.

**(c)** Both wrote `for k, v in d:`, which promises that **each element splits into exactly two pieces**. Looping plainly over a dictionary gives its **keys**, so what is being split is a key. `"name"` has four characters and will not split into two, hence the crash. `"ab"` has exactly two, so it splits happily into `"a"` and `"b"` — the promise is technically satisfied and Python proceeds. **Student 2 is in far more trouble.** Student 1 was told immediately, on the first run, with a line number. Student 2 has a program that runs, prints something that looks like output, and is silently wrong — and will stay wrong until somebody notices the values are missing.

**(d)** `.items()` — `for k, v in d.items():` gives real pairs, and is correct whatever the keys look like.

### C13 — three errors in one afternoon

**(a)** `'list' object has no attribute 'add'` — they tried to add an element to a **list** using the **set's** word; they wanted `.append()`. `'set' object is not subscriptable` — they tried to reach a set element by position, `s[0]`; a set has no positions. `KeyError: 'age'` — they looked up a key in a dictionary that was never put there (or misspelled it).

**(b)** The first two. Using `add` on a list is the set's habit applied to a list; using `[0]` on a set is the list's habit applied to a set. The third is a different kind of mistake — the right habit for a dictionary, just a key that is not there.

**(c)** **Know which container you are holding, and what its properties are.** Every one of these comes from acting on the wrong mental picture — the four-way table in Iteration 13 is the whole defence. A useful reflex when confused: `print(type(x))`, exactly as in Task 30.

---

# Part D — Predict the Output

**D1 —**
```
a
a
b
b
end
```
Both `print(c)` lines are indented, so both are in the body and the body runs twice — twice per element. `print("end")` is not indented, so it runs once after the loop.

**D2 —**
```
3
9
3
```
then `IndexError: list index out of range`.
`nums[0]` is the first; `nums[-1]` counts from the back for the last; `len` is 3. Then `nums[len(nums)]` is `nums[3]`, and the valid indexes stop at `len - 1` = 2. Writing `len(nums)` instead of `3` does not help — it is the same mistake spelled differently, which is exactly why the last index is worth memorising as `len - 1`.

**D3 —**
```
0
1
2
i is 3
```
`i` ends at **3**, not 2 — the loop stopped precisely *because* `i` became 3 and the test `3 < 3` was false. The value that fails the test is the value that survives.

**D4 —**
```
0 p
1 q
2 r
```
`range(len(letters))` is `range(3)` → `0, 1, 2`, the valid indexes. Each turn prints the position and the element at it.

**D5 —**
```
[7, 2, 3]
3
```
A list is mutable in both senses: `x[0] = 7` replaced an element in place, and `.append(3)` made the list longer, so `len` is now 3. Neither is possible with a C array.

**D6 —**
```
a
b
2
```
then `TypeError: 'tuple' object does not support item assignment`.
The walk and the `len` work exactly as for a list — reading a tuple is no different. Only the assignment is refused, and the error is about the **type** having no such operation, not about position 0 being missing.

**D7 —**
```
3
True
False
```
The distinct elements are `2`, `4`, `6` — the repeats were never stored, so `len` is 3, not 6. Then `in` answers `True` for 4 and `False` for 5. Note that these are the questions a set is *for*: how many distinct, and is this one in there.

**D8 —**
```
x
y
10
20
x 10
y 20
```
The three walks of a dictionary. Plain `for k in d` gives the **keys**; `.values()` gives the values; `.items()` gives both, unpacked into two loop variables. The order is the insertion order, which a dictionary has kept since Python 3.7 — unlike a set, this you *can* rely on.

**D9 —**
```
True
False
2
```
then `KeyError: 'c'`.
`"a" in d` is `True` — it is a key. `1 in d` is `False` — `1` is a *value*, and `in` on a dictionary tests keys only. `len` counts pairs, so 2. Then the missing key raises `KeyError`, and the message names it.

**D10 —**
```
3
[5, 6]
5
2
```
`len` counts the top level only — three elements, each a list of two. `box[2]` is itself a list and prints with its brackets. `box[2][0]` is two steps: element 2, then element 0 of that. `box[0][1]` is element 0 (`[1, 2]`), then element 1 of it, which is `2`.

**D11 —**
```
a
b
|
c
d
|
```
Read the indentation. `print(ch)` is indented twice, so it belongs to the inner loop and runs four times in all. `print("|")` is indented once, so it belongs to the **outer** loop and runs twice — once after each row is finished.

**D12 —**
```
2
Ravi
[7, 6]
6
```
`len` is 2 — two records at the top level. `rec[0]["n"]` is element 0 then key `"n"`. `rec[1]["m"]` is a whole list, so it prints with brackets. `rec[1]["m"][1]` adds a third step: element 1 of that list, which is `6`. Each `[…]` is one lookup and they are read strictly left to right.

**D13 —**
```
<class 'list'>
<class 'tuple'>
<class 'set'>
<class 'dict'>
<class 'dict'>
```
The last line is the point: **`{}` is an empty dictionary, not an empty set.** Curly brackets are used for both containers, and the dictionary has first claim on the empty case. An empty set can only be written `set()`.

**D14 —**
- `[1, 2][1]` — no crash, prints `2`.
- `(1, 2)[5]` — **`IndexError: tuple index out of range`**. Note the wording says *tuple*; the error name is the same as a list's but the message names the type.
- `{1, 2}[0]` — **`TypeError: 'set' object is not subscriptable`**. Not an `IndexError`: a set has no positions at all, so the operation itself is refused rather than the number.
- `{"k": 1}["k"]` — no crash, prints `1`.

The contrast between lines 2 and 3 is the one to hold on to. Asking an **ordered** container for a position it does not have is an `IndexError` — a question about a number. Asking an **unordered** container for a position at all is a `TypeError` — the question itself does not apply.

**D15 —**
- `[1, 2] + [3, 4]` — no error, prints `[1, 2, 3, 4]`. `+` joins two lists exactly as it joins two strings.
- `[1, 2] + 3` — `TypeError: can only concatenate list (not "int") to list`. Task 30's sentence with the type names swapped.
- `(1, 2) + (3,)` — no error, prints `(1, 2, 3)`. Tuples join too — and this does **not** contradict immutability: nothing was changed, a **new** tuple was built. `(3,)` needs its trailing comma; `(3)` would just be the number 3.
- `[1, 2] + (3, 4)` — `TypeError: can only concatenate list (not "tuple") to list`. Even two containers will not join if they are different kinds. A list joins to a list, and to nothing else.

**D16 —** All `AttributeError` except the last.
- `t.append(3)` on a tuple → `'tuple' object has no attribute 'append'`
- `s.append(3)` on a set → `'set' object has no attribute 'append'` (a set uses `add`)
- `lst.add(3)` on a list → `'list' object has no attribute 'add'` (a list uses `append`)
- `lst.append(3)` on a list → **no error**; the list becomes `[1, 2, 3]`

Only one of the four pairings is right. The message always names the type you were actually holding, which is usually enough to see the mistake.

**D17 —**
```
2
a
(1, 1)
(2, 2)
```
Tuples are immutable, so they are perfectly good keys. `len` counts pairs, so 2. `scores[(1, 1)]` looks up by the whole tuple. Looping plainly gives the **keys**, which here are tuples and print with their brackets.

**D18 —**
```
p and q
r and s
```
**No error, and the values 5 and 6 never appear.** Both keys are exactly two characters, so `for k, v in d:` splits each *key* into its two letters and the promise is satisfied. Had the keys been longer it would have raised `ValueError: too many values to unpack (expected 2)`. The fix is `d.items()`.

**D19 —** The first `nums.remove(1)` succeeds, leaving `[2, 3]`. The second raises `ValueError: list.remove(x): x not in list` — there is no longer a `1` to remove. `ValueError`, not `IndexError`, because `remove` searches for a **value**, not a position.

**D20 —** It prints
```
2
82
```
and then line 4 raises `NameError: name 'mark' is not defined. Did you mean: 'marks'?` — a typo, the singular instead of the plural. Python 3.12 guesses the correction; older versions print only the first sentence.

---

## The pattern to notice

Almost every answer here is one of four ideas wearing a different hat.

**Ordered or unordered decides everything else.** Ordered means positions exist, so indexing works and `IndexError` is possible (A8, A9, D2, D14). Unordered means positions never existed, so `[0]` is refused outright with `TypeError`, and the print order is not yours to predict (A13, C5, C6, D14).

**Index access and key access use the same brackets for different jobs.** A number bounded by the length, or a name you invented. That is the whole of `IndexError` vs `KeyError` (A9, A15, C7, D9), and it is why `student[0]` fails in a way that surprises people.

**The indentation is the block.** Not a habit, not a style — the syntax. It is why two identical-looking programs print different numbers of lines (A6, C2, D1, D11), and why forgetting it is an error that stops the program before it starts (A7).

**A container's limits are its message.** A tuple that cannot change and a set that keeps no duplicates are not weaker containers; they are containers that *state something about the data*, and the language enforces the statement (A11, A12, C4, C9). Choosing the right one is how you tell the next reader what the data is — and immutability turns out to be the one property that makes a value usable as a key at all (A22, A23, C11).

And one about the errors themselves.

**Most container errors are one container's habits applied to another.** `add` on a list, `append` on a tuple, `[0]` on a set, a string where a position belongs, a list where a key belongs (A19–A24, C13). They are not really separate things to memorise: they are the four-way table being enforced. Read the error's **name** for the category and its **message** for the type you were actually holding, and the mistake is usually visible in one line.

**Finally: prefer the crash.** `for k, v in d:` raises `ValueError` with four-letter keys and silently prints nonsense with two-letter ones (C12, D18, P24) — same bug, and the silent version is the dangerous one. The same was true of `"12" + "5"` in Task 30. An error message is Python telling you where the problem is, on the first run, for free.

Learn those five and you can work out the rest — including the containers this worksheet did not cover, and the ones in whatever language you meet next.
