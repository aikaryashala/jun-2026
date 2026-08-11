# Collections in Python — Lists, Tuples, Sets, Dictionaries, and `for … in`

**Goal.** Today you will meet the four containers Python gives you for holding many values under one name, and the loop that walks all four of them. You will predict the output of every program before running it, make several errors on purpose, and finish by building a container whose elements are themselves containers — the shape the rest of the world calls JSON.

**You need:** your Linux VM (WSL), a terminal, `python3`, a notebook, and a pencil.

```
python3 --version
```

You should see something like `Python 3.12.3`. Any `3.x` will run today's programs, though the wording of one or two error messages changed in 3.10 — where that matters, this worksheet says so.

Make a folder for today:

```
mkdir -p ~/task31
cd ~/task31
```

Write each program with `nano`, save it, run it with `python3 filename.py`. Today everything you store is an **int** or a **string** — the two types from Task 30.

---

## First, in general terms

Before any Python: **a collection — also called a container — is any object that groups multiple elements together under one name.** Nearly every programming language has them, because programs almost always deal with *many* of something: many marks, many names, many rows.

Languages differ in which collections they offer, but the collections themselves are usually told apart by two questions:

1. **Is it ordered or unordered?** In an **ordered collection** every element has a **position** — a first, a second, a third — and that position stays put. In an **unordered collection** the elements are simply *in* there, with no position at all, and asking for "the third one" is a meaningless question.
2. **How do you reach one element?** By its **position** (this is called **indexed access** — `arr[0]`, `arr[1]`), or by a **name you chose for it** (called **key access** — `student["name"]`).

You already know one collection. In C you have written:

```c
int marks[3] = {82, 91, 77};
printf("%d\n", marks[1]);
```

A C array is an **ordered collection** with **indexed access**. Python has that too — and three other containers besides, two of which C does not give you at all.

> **The golden rule of today**
> One name, many values. The four built-in containers differ by answering three questions differently — **is it ordered? can it be changed after it is built? are duplicates kept?** — and a dictionary answers a fourth: **is each element a pair?** Learn a container's answers and you know how it behaves. And **`for … in` walks any of them**, one element at a time, without caring which one it is.

---

## Iteration 1 — One name, many values

**a. What we set up**

```python
# first_list.py
marks = [82, 91, 77]
print(marks)
print(type(marks))
print(len(marks))
```

**b. Task**

Predict all three lines. In particular: will the first line print `82 91 77`, or something else?

**c. Observation (what you should find)**

```
[82, 91, 77]
<class 'list'>
3
```

Three things to notice.

**The square brackets are printed.** Printing a list does not print its elements one by one — it shows you the whole container, brackets, commas and all. That is Python showing you *"this is a list, and here is what is in it"*. Getting the elements out on their own is Iteration 2's job.

**The type is `list`.** A fourth type to add to Task 30's `int`, `str` and `float`. Notice that the list *itself* has a type, separate from the types of the things inside it.

**`len(marks)` is 3** — the number of elements. This is the first place Python differs sharply from C. Write this in C:

```c
int marks[3] = {82, 91, 77};
```

…and the array does **not** carry its own length. You have to remember `3` yourself, keep it in a variable, and pass it to every function that touches the array — and if you get it wrong, C will happily read past the end. A Python list knows how long it is, and `len()` asks it. A great deal of C bookkeeping simply disappears.

One more difference, shown once and then set aside:

```python
mixed = [82, "Ravi", 91]
print(mixed)
```
```
[82, 'Ravi', 91]
```

A C array is **homogeneous** — every element must be the same type. A Python list is **heterogeneous**: it will hold an int and a string side by side without complaint. *(Notice also that the string is displayed with quotes, `'Ravi'`, when it is inside a container. That is how Python shows you it is a string and not a name.)*

**Takeaway to say out loud:** "A list is one name holding many elements — and unlike a C array, it knows its own length."

---

## Iteration 2 — `for … in` walks the collection

Now get the elements out on their own. This is the loop you will use more than any other in Python.

**a. What we set up**

```python
# walk.py
marks = [82, 91, 77]
for m in marks:
    print(m)
print("done")
print(m)
```

Read the shape before you run it:

```
for  <name>  in  <collection>  :
     ‹four spaces›  the body
```

- `for` and `in` are the reserved words Task 30 warned you not to use as variable names. This is what Python was saving them for.
- `m` is a name **you** choose. Python creates it and puts each element into it in turn.
- The line **ends with a colon**, and the body is **indented** — four spaces. Both are required.

**b. Task**

Predict the exact output, counting lines. Then predict the last line specifically: after the loop has finished, what does `print(m)` show — an error, or a value?

**c. Observation (what you should find)**

```
82
91
77
done
77
```

The loop ran its body **three times, once per element**, and each time `m` held a different one. No brackets this time — you are printing the elements themselves, not the container.

The last line is the surprise: **`m` still exists after the loop, holding `77`.** The loop variable is an ordinary variable. Python does not tidy it away when the loop ends, so it keeps whatever it had on the final turn. Nothing about `for` is magical — it is just "assign the next element to this name, run the body, repeat".

Notice too what you did **not** have to write: no counter, no length, no `i = i + 1`, no test. You said "for each mark in marks", and that was the whole thing. Iteration 5 shows you the version where you do it all by hand.

**Takeaway to say out loud:** "`for m in marks:` puts each element into `m` in turn — and `m` survives the loop."

---

## Iteration 3 — The indentation *is* the loop body

In C, `{ }` decides what belongs to a loop, and the indentation is only for humans. **In Python the indentation is the only thing that decides.** This trips up every C programmer exactly once.

**a. What we set up**

Two programs. The only difference is four spaces on the last line.

```python
# indent_a.py
marks = [82, 91, 77]
for m in marks:
    print("mark:", m)
    print("---")
```

```python
# indent_b.py
marks = [82, 91, 77]
for m in marks:
    print("mark:", m)
print("---")
```

**b. Task**

Predict both outputs, and count the lines in each, before running either.

**c. Observation (what you should find)**

`indent_a.py` — six lines:

```
mark: 82
---
mark: 91
---
mark: 77
---
```

`indent_b.py` — four lines:

```
mark: 82
mark: 91
mark: 77
---
```

Same two statements, same order on the page, **different programs**. In `indent_a.py` the `print("---")` is indented, so it is **inside** the body and runs on every turn. In `indent_b.py` it is not indented, so it is **after** the loop and runs once, at the end.

Now two errors on purpose.

**Forget the colon:**

```python
marks = [82, 91, 77]
for m in marks
    print(m)
```
```
  File "/home/student/task31/no_colon.py", line 2
    for m in marks
                  ^
SyntaxError: expected ':'
```

Task 30's `SyntaxError` again — Python could not read the line, so nothing ran at all. Here it even tells you exactly which character is missing. *(On Python 3.9 and older the message is the vaguer `invalid syntax`.)*

**Forget the indentation:**

```python
marks = [82, 91, 77]
for m in marks:
print(m)
```
```
  File "/home/student/task31/no_indent.py", line 3
    print(m)
    ^^^^^
IndentationError: expected an indented block after 'for' statement on line 2
```

A **new error name** for your collection: **`IndentationError`**. It is a kind of `SyntaxError` — it happens *before* the program runs, so nothing prints — and it means "you promised me a block with that colon, and then did not indent one". *(Older Pythons say only `expected an indented block`, without naming the `for`.)*

Be consistent: four spaces per level, spaces not tabs. `nano` inserts spaces when you press Space; do not mix in tab characters, because Python can see the difference and you cannot.

**Takeaway to say out loud:** "Indentation is not decoration in Python — it *is* the block."

---

## Iteration 4 — Position: `[0]`, `[-1]`, and off the end

A list is an **ordered** collection, so every element has a position, and you can ask for one directly instead of walking the whole thing.

**a. What we set up**

```python
# positions.py
marks = [82, 91, 77]
print(marks[0])
print(marks[2])
print(marks[-1])
print(len(marks))
print(marks[3])
```

**b. Task**

Predict all five lines. Think hard about the last two: `len(marks)` is 3, so is `marks[3]` the last element?

**c. Observation (what you should find)**

```
82
77
77
3
Traceback (most recent call last):
  File "/home/student/task31/positions.py", line 6, in <module>
    print(marks[3])
          ~~~~~^^^
IndexError: list index out of range
```

**Counting starts at 0**, exactly as in C. `marks[0]` is the first element, and for a list of three the valid indexes are `0`, `1`, `2`. So the **last index is `len - 1`**, never `len`. Asking for `marks[3]` gives a new error name — **`IndexError`** — meaning "there is no such position".

*(Notice the `~~~~~^^^` marks under the failing part. Python 3.11 and newer point at the exact expression that broke, not just the line.)*

`marks[-1]` is something C does not have: **negative indexes count from the back.** `-1` is the last element, `-2` the second-to-last. It is a genuine convenience — `marks[-1]` never needs to know the length.

And here is the honest comparison with C. This Python line:

```python
print(marks[1])
```

looks identical to C's `printf("%d\n", marks[1]);` — but the machinery underneath is not the same thing at all. Task 11 showed that in C, `marks[1]` is **address arithmetic**: the position is a distance in bytes from where the array starts, worked out and followed. That is also why C will cheerfully let you read `marks[3]` — it just computes another address and reads whatever is lying there, giving you a garbage number or a crash, with no complaint. Python's `[1]` is a **lookup** on an object that knows how many elements it has, which is why it can stop you with a clear `IndexError` instead. **Same notation, different machinery, very different behaviour when you get it wrong.**

**Takeaway to say out loud:** "Indexes start at 0 and stop at `len - 1` — and Python tells me when I go past the end."

---

## Iteration 5 — The C loop, translated

Before `for … in` existed you had to walk a collection by hand, and in C you still do. It is worth writing that version once, to see what `for … in` is doing for you.

**a. What we set up**

You have written this shape in C many times:

```c
for (int i = 0; i < 3; i++) {
    printf("%d\n", marks[i]);
}
```

Three separate jobs in that one line: **start** at 0, **test** `i < 3`, **step** `i++`. Here it is in Python, using a `while` loop, which repeats its body for as long as its test is true:

```python
# by_hand.py
marks = [82, 91, 77]
i = 0
while i < len(marks):
    print(marks[i])
    i = i + 1
print("i ended at", i)
```

Two small pieces of new notation, both doing what they look like:

- `i < len(marks)` is a **comparison** — it asks a yes/no question, and `while` keeps going while the answer is yes.
- `i = i + 1` reads right to left: *work out `i + 1`, then store it back in `i`.* This is the **step**, and forgetting it is how you write a loop that never ends.

**b. Task**

Predict the output, including the final line. Then predict what happens if you change `<` to `<=`.

**c. Observation (what you should find)**

```
82
91
77
i ended at 3
```

The same three lines as Iteration 2, from four lines of code instead of two — and three separate things to get right. `i` ends at **3**, not 2: the loop stopped *because* `i` reached 3 and the test `3 < 3` was false.

Now the deliberate error. Change `<` to `<=`:

```
82
91
77
Traceback (most recent call last):
  File "/home/student/task31/by_hand.py", line 4, in <module>
    print(marks[i])
          ~~~~~^^^
IndexError: list index out of range
```

The whole list printed correctly, and *then* it crashed — because `i <= 3` allowed one extra turn, and `marks[3]` does not exist. This is the **off-by-one error**, the most common bug in all of programming, and it is exactly the same mistake you can make in C. The difference is that Python stopped you; C would have printed a garbage number and carried on.

**Every one of those three moving parts is a chance to be wrong, and `for m in marks:` has none of them.** That is the argument for `for … in`, and why you should reach for it by default.

But sometimes you genuinely need the **position** as well as the element — and there is a tidier way than a `while` loop:

```python
# with_range.py
marks = [82, 91, 77]
for i in range(len(marks)):
    print(i, marks[i])
```
```
0 82
1 91
2 77
```

`range(3)` produces the numbers `0, 1, 2` — *up to but not including* 3, which is exactly the set of valid indexes for a list of length 3. You can see them:

```python
print(list(range(3)))
```
```
[0, 1, 2]
```

So `for i in range(len(marks))` is the counted loop with the start, test and step all handled for you. Use `for m in marks` when you want the elements, and `for i in range(len(marks))` when you truly need the positions.

**Takeaway to say out loud:** "`while` makes me write the start, the test and the step — `for … in` writes all three for me."

---

## Iteration 6 — A list can be changed

> **In general terms:** a list is an **ordered, mutable sequence** — its elements have positions, you reach them by index, and it **can be changed after it is built**. *Mutable* is simply the word for "changeable".

**a. What we set up**

```python
# change.py
marks = [82, 91, 77]
marks[0] = 99
print(marks)
marks.append(60)
print(marks)
print(len(marks))
```

**b. Task**

Predict all three lines. Pay attention to the third: does `len` still say 3?

**c. Observation (what you should find)**

```
[99, 91, 77]
[99, 91, 77, 60]
4
```

Two different kinds of change, and both are worth naming.

**`marks[0] = 99` replaced an element in place.** Same list, same length, different content at position 0. You are using `[0]` on the *left* of the `=` this time — reaching a position in order to write to it rather than read from it.

**`marks.append(60)` made the list longer.** It now has four elements and `len` says 4. In C this is simply not possible: `int marks[3]` reserves room for exactly three ints when you declare it, and that is all you ever get. Growing a C array means allocating new memory and copying — real work you do by hand. A Python list grows when you ask it to.

`append` is written differently from anything in Task 30: `marks.append(60)`, with a dot. Read it as *"the list called `marks`: append 60 to yourself."* Many of Python's containers carry their own abilities like this, reached with a dot after the name.

Keep this property in mind — **mutable** — because the next container is defined by not having it.

**Takeaway to say out loud:** "A list is mutable: I can replace an element, and I can make it longer."

---

## Iteration 7 — A tuple is a list that cannot change

> **In general terms:** a tuple is an **ordered, immutable sequence**. Same positions, same indexed access as a list — but **frozen** once built. *Immutable* means "not changeable".

**a. What we set up**

Round brackets instead of square ones:

```python
# frozen.py
t = (82, 91, 77)
print(t)
print(type(t))
print(len(t))
print(t[0])
for x in t:
    print(x)
t[0] = 99
```

**b. Task**

Predict every line. The real question: which of these operations still work on a tuple, and which one does not?

**c. Observation (what you should find)**

```
(82, 91, 77)
<class 'tuple'>
3
82
82
91
77
Traceback (most recent call last):
  File "/home/student/task31/frozen.py", line 8, in <module>
    t[0] = 99
    ~^^^
TypeError: 'tuple' object does not support item assignment
```

**Everything worked exactly as it did for a list** — printing, `len`, `t[0]`, and `for … in` walking all three elements — right up until the attempt to change it. Then `TypeError`, Task 30's error for "wrong kind of value for this operation".

Notice which error it is, and what that tells you. It is not "position 0 is missing" — position 0 is right there and printed fine one line earlier. It is that a tuple, *as a type*, has no such operation as assignment. The refusal is about the container, not the position.

So a tuple is a list with one ability removed. Why would you ever want that?

- **It says the data is fixed.** Some groups of values are not meant to change once made — a date as `(2026, 8, 11)`, a coordinate as `(3, 7)`. Using a tuple makes that a rule the language enforces, rather than a promise a programmer has to remember.
- **It cannot be changed by accident** by some distant part of a big program.
- **A tuple can be used where a list cannot.** You will meet this properly later; for now, note that Python trusts frozen things in places it does not trust changeable ones.

One trap for later. A tuple with a single element needs a trailing comma — `(5,)`, not `(5)` — because plain `(5)` is just the number 5 in ordinary brackets.

**Takeaway to say out loud:** "A tuple is an ordered collection like a list, but immutable — `t[0] = …` is a `TypeError`."

---

## Iteration 8 — A set has no duplicates and no order

> **In general terms:** a set is an **unordered collection of unique elements**. No positions, therefore no index. It answers one question extremely well: *is this element in here?*

**a. What we set up**

Curly brackets, elements separated by commas:

```python
# unique.py
nums = {3, 1, 2, 3, 7, 1}
print(nums)
print(len(nums))
print(type(nums))
```

**b. Task**

Count the elements written between the brackets. Predict what `len` will say. Predict the order they print in.

**c. Observation (what you should find)**

```
{1, 2, 3, 7}
4
<class 'set'>
```

**Six elements went in; four came out.** The repeated `3` and the repeated `1` are simply not there. A set does not complain about duplicates and does not remove them later — it never stores a second copy in the first place. That is the definition of the thing: **every element in a set is unique.**

And the order changed. You wrote `3, 1, 2, 3, 7, 1` and got `1, 2, 3, 7`. It is tempting to conclude that a set sorts its elements. **It does not.** Prove it — put strings in one and run the *same program* three times:

```python
# no_order.py
names = {"ravi", "asha", "balaji", "govind"}
print(names)
```

```
$ python3 no_order.py
{'ravi', 'asha', 'balaji', 'govind'}
$ python3 no_order.py
{'asha', 'balaji', 'ravi', 'govind'}
$ python3 no_order.py
{'asha', 'ravi', 'govind', 'balaji'}
```

**Three runs, three different orders, same program, same file.** (Your three will differ from these three, and from each other. If two runs happen to match, run it again.) Nothing is broken. A set genuinely has **no order to report**, so what you see is an accident of how Python stored the elements internally this time — and for strings that changes from run to run by design.

So: **never write a program whose output depends on the order of a set**, and never try to predict it. If you need a fixed order, ask for one — `sorted(names)` gives back an ordered list.

Because there is no order, there are no positions, and so there is nothing for `[0]` to mean:

```python
names = {"ravi", "asha", "balaji"}
print(names[0])
```
```
Traceback (most recent call last):
  File "/home/student/task31/no_index.py", line 2, in <module>
    print(names[0])
          ~~~~~^^^
TypeError: 'set' object is not subscriptable
```

`TypeError` again, and note it is the *type* being refused once more: not "position 0 is out of range" but "this kind of container does not do positions at all". *Subscript* is the formal word for the `[…]` operation.

You can still walk a set with `for … in` — you just cannot rely on the order it hands the elements to you.

C gives you no set at all. Removing duplicates from a C array means writing the loops yourself, and checking membership means scanning the whole array every time.

**Takeaway to say out loud:** "A set keeps each element once, in no order — so no duplicates, and no `[0]`."

---

## Iteration 9 — A dictionary stores pairs

> **In general terms:** this is an **associative array**, also called a **map**, a **hash table**, or a **key–value store** — the same idea in every language. Instead of reaching an element by its position, you reach it by a **key** you chose yourself. This is **key access** rather than indexed access.

**a. What we set up**

Curly brackets again, but the elements come in `key: value` pairs:

```python
# student.py
student = {"name": "Ravi", "marks": 82, "city": "Warangal"}
print(student)
print(type(student))
print(len(student))
print(student["name"])
print(student["marks"])
print(student["age"])
```

**b. Task**

Predict the first five lines. Then predict what the last one does — `age` was never put in.

**c. Observation (what you should find)**

```
{'name': 'Ravi', 'marks': 82, 'city': 'Warangal'}
<class 'dict'>
3
Ravi
82
Traceback (most recent call last):
  File "/home/student/task31/student.py", line 7, in <module>
    print(student["age"])
          ~~~~~~~^^^^^^^
KeyError: 'age'
```

**`len` is 3, not 6.** One pair is one element. The dictionary holds three pairs, not six separate things.

**You look up by key, not by position.** `student["name"]` reads *"in `student`, the value filed under `name`"*. The square brackets look like list indexing but the thing inside them is completely different — a name you invented rather than a position. `student[0]` would not give you `"Ravi"`; it would be a `KeyError` for the key `0`.

**A missing key is a `KeyError`** — the last new error name today, and a close relative of `IndexError`. Both mean "there is nothing there"; one is about a position, the other about a key. Helpfully, the message names the key that was missing: `'age'`.

Now walk it. A dictionary's elements are pairs, so "each element" is ambiguous — and Python gives you three different walks:

```python
# walk_dict.py
student = {"name": "Ravi", "marks": 82, "city": "Warangal"}
for k in student:
    print(k)
print("---")
for v in student.values():
    print(v)
print("---")
for k, v in student.items():
    print(k, "=", v)
```
```
name
marks
city
---
Ravi
82
Warangal
---
name = Ravi
marks = 82
city = Warangal
```

| You write | You get |
|---|---|
| `for k in student:` | the **keys** — this is the default, and it surprises people |
| `for v in student.values():` | the **values** |
| `for k, v in student.items():` | **both**, into two loop variables at once |

Plain `for k in student:` giving keys is worth memorising. It is a sensible default — with a key you can always reach the value — but it is not what most people guess.

Note that these came out in the order they were written. Since Python 3.7 a dictionary **remembers its insertion order**, so unlike a set you *can* rely on this. In general-terms language an associative array is not required to be ordered — Python's happens to be, and older Pythons were not.

C gives you no dictionary either. Reaching a value by a string name in C means writing a hash table yourself, or scanning an array of structs and comparing strings one by one.

**Takeaway to say out loud:** "A dictionary is key access, not position access — and looping over it plainly gives me the keys."

---

## Iteration 10 — `in` asks whether something is there

You have used `in` all day as part of `for … in`. On its own it does a completely different job — and works on all four containers.

**a. What we set up**

```python
# member.py
marks = [82, 91, 77]
names = {"ravi", "asha"}
student = {"name": "Ravi", "marks": 82}

print(82 in marks)
print(50 in marks)
print("ravi" in names)
print("name" in student)
print("Ravi" in student)
print(82 in student)
```

**b. Task**

Predict all six. Lines 4, 5 and 6 are the interesting ones: `student` contains `"name"`, `"Ravi"` and `82` somewhere inside it — so will all three say the same thing?

**c. Observation (what you should find)**

```
True
False
True
True
False
False
```

`in` asks *"is this element in this container?"* and answers **`True`** or **`False`**. Those are Python's two answers to a yes/no question, and they form a fifth type, `bool` — you met the words in Task 30's list of reserved words, and now you know what they are for. They are written with a capital letter and no quotes: `True`, not `"true"`.

Now the three dictionary lines, which are the point of this iteration:

- `"name" in student` → **`True`** — `"name"` is a **key**.
- `"Ravi" in student` → **`False`** — `"Ravi"` is a **value**, and `in` did not look at the values.
- `82 in student` → **`False`** — also a value.

**On a dictionary, `in` tests the keys only.** This is the same default you saw in Iteration 9 with `for k in student:` — plain iteration over a dictionary means its keys, and `in` follows the same rule. If you really need to search the values, say so: `"Ravi" in student.values()`.

Two words for the same symbol, then. In `for m in marks:` the `in` is part of the loop's grammar. In `82 in marks` it is an operator that produces `True` or `False`. Python reuses the word; the position on the line tells you which one you are looking at.

And a note on speed, since you have seen how C would do this. Checking `82 in marks` on a **list** means Python looking at the elements one by one until it finds a match — exactly the loop you would write in C. Checking `"ravi" in names` on a **set**, or a key in a dictionary, does not scan at all; those containers are built to answer that one question immediately, however many elements they hold. **That is the main reason to choose a set over a list.**

**Takeaway to say out loud:** "`in` gives me `True` or `False` — and on a dictionary it looks at the keys, never the values."

---

## Iteration 11 — Containers inside containers

Nothing so far said an element has to be a simple value. An element can be **another container** — and once you see that, the four small containers become enough to describe almost any real data.

**a. What we set up**

Start with a list whose elements are lists — three students, two marks each:

```python
# nested.py
class_marks = [[82, 91], [77, 65], [90, 88]]
print(class_marks)
print(len(class_marks))
print(class_marks[0])
print(class_marks[0][1])
```

**b. Task**

Predict all four lines. Line 2 especially: is `len` 3, or 6? And read `class_marks[0][1]` carefully before you guess.

**c. Observation (what you should find)**

```
[[82, 91], [77, 65], [90, 88]]
3
[82, 91]
91
```

**`len` is 3** — three elements, each of which happens to be a list of two. `len` counts the top level only; it does not look inside.

**`class_marks[0]` is itself a list**, printed with its brackets.

**`class_marks[0][1]` reads strictly left to right**: take `class_marks`, go to position 0 — that is `[82, 91]` — then go to position 1 *of that* — which is `91`. Two steps, written next to each other. There is no new rule here; it is Iteration 4 applied twice.

Walking a nested container needs a loop inside a loop — and **two levels of indentation**, which is where Iteration 3 pays off:

```python
# nested_loop.py
class_marks = [[82, 91], [77, 65], [90, 88]]
for row in class_marks:
    for m in row:
        print(m)
    print("--- end of row")
```
```
82
91
--- end of row
77
65
--- end of row
90
88
--- end of row
```

Read the indentation. `print(m)` is indented twice, so it belongs to the inner loop and runs six times. `print("--- end of row")` is indented once, so it belongs to the **outer** loop and runs three times, after each row finishes.

Now the shape that matters. Mix the containers — a **list of dictionaries**, where one value is itself a **list**:

```python
# records.py
students = [
    {"name": "Ravi",   "marks": [82, 91]},
    {"name": "Asha",   "marks": [77, 65]},
    {"name": "Balaji", "marks": [90, 88]},
]

print(len(students))
print(students[0])
print(students[0]["name"])
print(students[1]["marks"])
print(students[1]["marks"][0])

for s in students:
    print(s["name"], "scored", s["marks"])
```
```
3
{'name': 'Ravi', 'marks': [82, 91]}
Ravi
[77, 65]
77
Ravi scored [82, 91]
Asha scored [77, 65]
Balaji scored [90, 88]
```

*(A list may be spread over several lines like that, one element per line, which is much easier to read. Python looks for the closing `]`, not the end of the line.)*

`students[1]["marks"][0]` is three steps left to right: element 1 of the list → the value under key `"marks"` → element 0 of that list. Read it slowly and it says exactly what it does.

And the two loops can nest across container types too:

```python
for s in students:
    for m in s["marks"]:
        print(s["name"], m)
```
```
Ravi 82
Ravi 91
Asha 77
Asha 65
Balaji 90
Balaji 88
```

**Look at the shape of `students` once more.** A list of records; each record a set of named fields; the values ints, strings, and lists of ints. That is how nearly all real data is shaped — an API's reply, a configuration file, a saved game, a database row.

Written down as **text**, so it can be saved to a file or sent over a network, this exact shape has a name the whole world uses: **JSON**. That is **Task 33**, and you have just built the thing it describes. Task 32 comes first, on strings.

**Takeaway to say out loud:** "An element can be another container — and `data[1]["marks"][0]` is just three lookups, left to right."

---

## Iteration 12 — The errors you will meet

You have already met five error names today. Working with containers produces a few more, and they are worth meeting **on purpose**, in a quiet moment, rather than for the first time in the middle of a program you are trying to finish.

Every error below is a real message from Python 3.12, produced by the line shown. Read the **name** first — it tells you the category — then the message, which usually names the exact type or value that went wrong.

**a. What we set up**

Ten short programs. Type them one at a time (or keep one file and edit it), run each, and read what comes back.

**b. Task**

Before running each one, write down: **will it crash, and if so which error name?** One of the ten does not crash at all, and that one is the most dangerous of the lot.

**c. Observation (what you should find)**

### "That operation belongs to a different type" — `AttributeError`

```python
t = (1, 2, 3)
t.append(4)
```
```
    t.append(4)
    ^^^^^^^^
AttributeError: 'tuple' object has no attribute 'append'
```

A **new error name**. An *attribute* is anything you reach with a dot after the name — including abilities like `.append()`. This message means: *this type has no such ability.* A tuple is immutable, so of course it cannot append.

The same error appears whenever you give a container an ability belonging to a different one:

```python
nums = [1, 2]
nums.add(3)          # AttributeError: 'list' object has no attribute 'add'

d = {"a": 1}
d.append(2)          # AttributeError: 'dict' object has no attribute 'append'
```

`add` is the **set's** word for growing; `append` is the **list's**. They are not interchangeable, and mixing them up is one of the most common beginner errors. Note the difference from `TypeError`: a `TypeError` usually means "this operation exists but not for these types", while `AttributeError` means "there is no such thing on this type at all".

### "Wrong kind of thing inside the brackets" — `TypeError`

```python
marks = [82, 91]
print(marks["0"])
```
```
    print(marks["0"])
          ~~~~~^^^^^
TypeError: list indices must be integers or slices, not str
```

A list is reached by **position**, and a position is a number. `"0"` in quotes is a *string* — Task 30's lesson, arriving again in a new place. Notice this is not `IndexError`: the problem is not that position `"0"` is missing, it is that `"0"` is not a position at all. The message even names the type it received: `not str`.

This one catches people moving between dictionaries and lists, where the brackets look identical but want completely different things inside them.

### "That is not a container at all" — `TypeError`

```python
n = 5
print(n[0])
```
```
TypeError: 'int' object is not subscriptable
```

```python
for x in 5:
    print(x)
```
```
    for x in 5:
             ^
TypeError: 'int' object is not iterable
```

Two different complaints about the same mistake — treating a single value as a collection. **Subscriptable** means "you can put `[…]` after it". **Iterable** means "`for … in` can walk it". A plain int is neither. You saw `not subscriptable` before, for a set; there it meant "this container has no positions", here it means "this is not a container".

### "A changeable thing cannot be a set element or a dictionary key" — `TypeError`

This is the one that finally explains why tuples exist.

```python
s = {[1, 2], [3, 4]}
```
```
    s = {[1, 2], [3, 4]}
        ^^^^^^^^^^^^^^^^
TypeError: unhashable type: 'list'
```

```python
d = {[1, 2]: "x"}
```
```
TypeError: unhashable type: 'list'
```

Now swap the lists for tuples, and both work:

```python
d = {(1, 2): "x"}
print(d[(1, 2)])
```
```
x
```

**A list cannot be a set element or a dictionary key. A tuple can.** Iteration 10 mentioned that a set and a dictionary answer "is this in there?" immediately, instead of scanning. They manage that by working out a number from the element's *contents* — that number is called a **hash** — and filing the element in the place that number points to. **Hashable** means "a hash can be worked out for it".

But if the element could change afterwards, its contents would no longer match the place it was filed under, and the container would never find it again. So Python only allows **immutable** elements: ints, strings and tuples are hashable; lists, sets and dictionaries are not.

So the answer to "why would I want a container that can do less?" from Iteration 7 is: **because only a frozen thing can be used as a key.** That `(row, column)` tuple can be a dictionary key; the same pair written as a list cannot.

### "The pairs did not come apart the way you promised" — `ValueError`

```python
d = {"name": "Ravi", "city": "Warangal"}
for k, v in d:
    print(k, v)
```
```
    for k, v in d:
        ^^^^
ValueError: too many values to unpack (expected 2)
```

Task 30's `ValueError` — right kind of thing, wrong content. Writing `for k, v in …` promises that **each element will come apart into exactly two pieces**. But looping over a dictionary plainly gives its **keys** (Iteration 9), and the key `"name"` is a string of four characters, not a pair. The fix is to ask for the pairs: `for k, v in d.items():`.

**Now the dangerous one — the program that does not crash.** Change the keys to two letters each:

```python
d = {"ab": 1, "cd": 2}
for k, v in d:
    print(k, v)
```
```
a b
c d
```

**No error. Completely wrong output.** With exactly two characters, `"ab"` *does* come apart into two pieces — `"a"` and `"b"` — so Python does it, and the values `1` and `2` never appear at all. Same bug as the line above; it only crashed there because the key happened to be longer than two characters.

This is the pattern to fear, and Task 30 had one just like it (`"12" + "5"` giving `125`). **A crash is a gift.** The bug that prints something plausible and wrong is the one that survives to production.

### "Do not resize a container while you are walking it" — `RuntimeError`

```python
d = {"a": 1, "b": 2}
for k in d:
    d["c"] = 3
```
```
    for k in d:
             ^
RuntimeError: dictionary changed size during iteration
```

Another new name. `for … in` walks a container by keeping track of where it has got to; adding or removing elements mid-walk makes that position meaningless, so Python stops you rather than skipping or repeating elements silently. If you need to build a changed version, walk the original and build a **new** container.

### "That value is not in there" — `ValueError`

```python
nums = [1, 2, 3]
nums.remove(9)
```
```
ValueError: list.remove(x): x not in list
```

`ValueError` again — right type, wrong content. Note it is **not** an `IndexError`: `remove` takes a *value* to find, not a position. Check first with `if 9 in nums` — or in the meantime, with `print(9 in nums)`.

### "You indented for no reason" — `IndentationError`

```python
marks = [82, 91]
print(marks)
    print(len(marks))
```
```
    print(len(marks))
IndentationError: unexpected indent
```

The mirror image of Iteration 3's error. There, a colon promised a block and none arrived — `expected an indented block`. Here a line is indented with no colon above it to justify it — `unexpected indent`. Both happen **before** the program runs, so nothing prints.

### "No such name" — `NameError`

```python
marks = [82, 91]
print(mark[0])
```
```
    print(mark[0])
          ^^^^
NameError: name 'mark' is not defined. Did you mean: 'marks'?
```

The last new name, and the friendliest. `NameError` means you used a name Python has never seen — nearly always a typo or a variable used before it was created. Python 3.12 even guesses what you meant. *(Older Pythons print only the first sentence.)*

### One from Task 30, wearing new clothes

```python
nums = [1, 2]
print(nums + 3)
```
```
TypeError: can only concatenate list (not "int") to list
```

Compare with Task 30's `TypeError: can only concatenate str (not "int") to str`. **The same sentence with the type names swapped.** `+` joins two lists into a longer list, exactly as it joins two strings — and just as before, Python refuses to mix a container with a single value. Even `nums + (3, 4)` fails: `can only concatenate list (not "tuple") to list`. A list joins to a list, and to nothing else.

### The whole set, at a glance

| What you wrote | Error | It means |
|---|---|---|
| `t.append(4)` on a tuple | `AttributeError` | this type has no such ability |
| `nums.add(3)` on a list | `AttributeError` | `add` is the set's word, `append` the list's |
| `marks["0"]` | `TypeError: list indices must be integers…` | a position is a number, not a string |
| `n[0]` where `n = 5` | `TypeError: … not subscriptable` | that is not a container |
| `for x in 5:` | `TypeError: … not iterable` | that cannot be walked |
| `{[1, 2]}` or `{[1, 2]: "x"}` | `TypeError: unhashable type: 'list'` | only immutable things can be elements of a set or keys of a dictionary |
| `for k, v in d:` | `ValueError: too many values to unpack` | plain iteration gives keys, not pairs — use `.items()` |
| `d["c"] = 3` inside `for k in d:` | `RuntimeError` | do not resize while walking |
| `nums.remove(9)` | `ValueError: x not in list` | that value is not there |
| an indent with no colon above | `IndentationError: unexpected indent` | before the program runs |
| a misspelled variable | `NameError` | no such name — check the spelling |
| `nums + 3` | `TypeError: can only concatenate list…` | a list joins only to a list |

**How to read any of them, in three steps:** the **name** gives the category; the **message** usually names the offending type or value; and the **line with the `^^^` marks** shows exactly which part of the expression failed. That is enough to fix nearly every error on this page without looking anything up.

**Takeaway to say out loud:** "The error name is the category, the message names the culprit — and a crash is safer than a wrong answer."

---

## Iteration 13 — Choosing one

**a. What we set up**

No new program. Fill this table in your notebook from what you observed today, *then* compare with the version below.

| | List | Tuple | Set | Dictionary |
|---|---|---|---|---|
| written with | | | | |
| ordered? | | | | |
| changeable? | | | | |
| duplicates kept? | | | | |
| reach one element by | | | | |

**b. Task**

Then answer: which container would you choose for each of these, and why?

1. The marks of one student, in the order the exams were taken.
2. The `(row, column)` position of a square on a board.
3. Every distinct city that a batch of students comes from.
4. One student's name, roll number and city, stored together.

**c. Observation (what you should find)**

| | **List** | **Tuple** | **Set** | **Dictionary** |
|---|---|---|---|---|
| general name | ordered mutable sequence | ordered immutable sequence | unordered collection of unique elements | associative array / map |
| written with | `[1, 2, 3]` | `(1, 2, 3)` | `{1, 2, 3}` | `{"a": 1, "b": 2}` |
| ordered? | **yes** | **yes** | **no** | insertion order kept (3.7+) |
| changeable? | **yes** | **no** | yes (add/remove) | **yes** |
| duplicates kept? | yes | yes | **no** | keys unique; values may repeat |
| reach one element by | index `x[0]` | index `x[0]` | **you cannot** — only `in` | key `x["name"]` |
| `for … in` gives | elements | elements | elements, **any order** | **keys** |
| `len` counts | elements | elements | unique elements | pairs |

The four answers:

1. **A list.** Order matters (which exam was which), duplicates are possible (two 82s), and you may want to add another mark later.
2. **A tuple.** A position is a fixed pair; it should not change once made, and the *first* one meaning "row" is part of what it is.
3. **A set.** You want each city once no matter how many students come from it, the order is meaningless, and you will mostly ask "is Warangal in there?".
4. **A dictionary.** These are named fields, not a sequence — `student["city"]` says what it means, where `student[2]` would make you remember that position 2 was the city.

**A general rule that outlives Python:** pick the container whose *properties* match the data. If order is meaningless, do not use an ordered collection — a set says "these are unique and unordered", and any programmer reading it knows that instantly. The container you choose is a message to the next person.

### One last thing about the word

Python also ships a built-in module *named* **`collections`**:

```python
import collections
```

It contains some extra, specialised containers — `Counter`, `defaultdict`, `deque` and a few others — built on top of the four you learnt today. **That module is a different thing from what this worksheet has meant by "a collection".** Today's word is the general programming term for any container that groups elements; the module is one specific library with a confusingly similar name. You do not need it yet. Just do not be surprised when you see the word doing both jobs.

**Takeaway to say out loud:** "Choose the container whose properties match the data — the choice tells the next reader what the data is."

---

## Practice — Predict the output

Write the **exact** output in your notebook *before* running. Count the lines. Watch the indentation. Then run each and compare — and when you are wrong, find the rule you forgot.

**P1.**
```python
items = [4, 8, 15]
print(items)
print(len(items))
print(items[1])
```

**P2.**
```python
for x in [1, 2, 3]:
    print(x)
print(x)
```

**P3.**
```python
for n in [1, 2]:
    print("a")
    print("b")
print("c")
```

**P4.**
```python
for n in [1, 2]:
    print("a")
print("b")
print("c")
```

**P5.**
```python
words = ["red", "green", "blue"]
print(words[0])
print(words[-1])
print(words[len(words) - 1])
```

**P6.** Which line crashes, and with which error name?
```python
nums = [10, 20, 30]
print(nums[2])
print(nums[3])
```

**P7.**
```python
i = 0
data = [5, 6, 7]
while i < len(data):
    print(i, data[i])
    i = i + 1
```

**P8.**
```python
for i in range(4):
    print(i)
```

**P9.**
```python
nums = [1, 2, 3]
nums[1] = 99
nums.append(4)
print(nums)
print(len(nums))
```

**P10.** Which line crashes, and with which error name?
```python
t = (1, 2, 3)
print(t[1])
print(len(t))
t[1] = 99
```

**P11.**
```python
s = {5, 3, 5, 1, 3}
print(len(s))
```

**P12.** Explain why the exact output of this cannot be predicted.
```python
cities = {"delhi", "hyderabad", "chennai"}
print(cities)
```

**P13.**
```python
d = {"a": 1, "b": 2}
print(len(d))
print(d["b"])
for k in d:
    print(k)
```

**P14.**
```python
d = {"name": "Asha", "roll": 12}
print("name" in d)
print("Asha" in d)
print(12 in d)
```

**P15.** Which line crashes, and with which error name?
```python
d = {"x": 1}
print(d["x"])
print(d["y"])
```

**P16.**
```python
grid = [[1, 2], [3, 4]]
print(len(grid))
print(grid[1])
print(grid[1][0])
```

**P17.**
```python
grid = [[1, 2], [3, 4]]
for row in grid:
    for v in row:
        print(v)
    print("-")
```

**P18.**
```python
people = [{"n": "Ravi", "m": [8, 9]}, {"n": "Asha", "m": [7, 6]}]
print(len(people))
print(people[1]["n"])
print(people[0]["m"][1])
```

**P19.** For each, name the container type and say whether it is ordered.
```python
a = [1, 2]
b = (1, 2)
c = {1, 2}
d = {1: 2}
e = {}
```

**P20.** Which of these four crash, and with which error name?
```python
print([1, 2, 3][3])
print((1, 2, 3)[0])
print({1, 2, 3}[0])
print({"a": 1}["b"])
```

**P21.** Name the error for each line.
```python
t = (1, 2)
t.append(3)
```
```python
nums = [1, 2]
nums.add(3)
```

**P22.** Which crashes, and with which error?
```python
marks = [82, 91, 77]
print(marks[1])
print(marks["1"])
```

**P23.** One of these two works and one does not. Which, and why?
```python
a = {(1, 2): "first"}
b = {[1, 2]: "first"}
```

**P24.**
```python
d = {"xy": 1, "zw": 2}
for k, v in d:
    print(k, v)
```
(Predict carefully — then say what would happen if the keys were `"name"` and `"city"` instead.)

**P25.** Name the error for each.
```python
n = 7
print(n[0])
```
```python
for q in 7:
    print(q)
```

**P26.** Which of these four crash, and with which error name?
```python
print([1, 2] + [3])
print([1, 2] + 3)
print([1, 2].remove(5))
print(len([1, 2]))
```

---

### Self-check

**Cover this until every prediction above is written down.** Checking as you go teaches you nothing.

**P1** — `[4, 8, 15]` / `3` / `8`. Printing the list shows the brackets; `[1]` is the *second* element.

**P2** — `1` `2` `3` `3`. The loop variable survives, holding the last element.

**P3** — `a b a b c` on five lines. Both prints are indented, so both are in the body; the body runs twice.

**P4** — `a a b c` on four lines. Only the first print is in the body. Compare with P3: same statements, different indentation, different program.

**P5** — `red` / `blue` / `blue`. The last two are two ways of saying the same thing; `[-1]` is the shorter one.

**P6** — Line 3. `IndexError: list index out of range`. Valid indexes are 0, 1, 2 — the last is `len - 1`. Line 2 prints `30` first.

**P7** — `0 5` / `1 6` / `2 7`. The counted loop, all three parts written by hand.

**P8** — `0` `1` `2` `3` on four lines. `range(4)` is *up to but not including* 4 — four numbers starting at 0.

**P9** — `[1, 99, 3, 4]` / `4`. A list is mutable in both ways: replace an element, and grow.

**P10** — Line 4. `TypeError: 'tuple' object does not support item assignment`. Lines 2 and 3 work fine and print `2` and `3` — reading a tuple is no different from reading a list.

**P11** — `3`. The elements are `5, 3, 1`; the repeated `5` and `3` were never stored twice.

**P12** — A set has **no order**, so what gets printed is an accident of internal storage — and for strings it differs from run to run. You could not predict it, and neither could anyone else; running it again is likely to give a different order. `sorted(cities)` is how you get a fixed one.

**P13** — `2` / `2` / `a` `b`. `len` counts pairs. Looping over a dictionary plainly gives the **keys**.

**P14** — `True` / `False` / `False`. `in` on a dictionary tests **keys only**. `"Asha"` and `12` are values, so both are `False`.

**P15** — Line 3. `KeyError: 'y'`. Line 2 prints `1` first. Note the error names the missing key.

**P16** — `2` / `[3, 4]` / `3`. `len` counts the top level only — two elements, each a list.

**P17** — `1 2 - 3 4 -` on six lines. `print(v)` is indented twice (inner loop, four times); `print("-")` once (outer loop, twice).

**P18** — `2` / `Asha` / `9`. `people[0]["m"][1]` is three steps left to right: element 0 → key `"m"` → element 1.

**P19** — `a` list, ordered. `b` tuple, ordered. `c` set, **not** ordered. `d` dictionary, keeps insertion order. `e` is the trap: **empty curly brackets are an empty dictionary**, not an empty set — `<class 'dict'>`. An empty set has to be written `set()`.

**P20** — Line 1: `IndexError` (valid indexes are 0–2). Line 2: no crash, prints `1`. Line 3: `TypeError: 'set' object is not subscriptable` — a set has no positions at all. Line 4: `KeyError: 'b'`.

**P21** — Both `AttributeError`. `'tuple' object has no attribute 'append'` (a tuple is immutable, so it has no way to grow) and `'list' object has no attribute 'add'` (`add` is the **set's** word; a list uses `append`). `AttributeError` means the ability does not exist on that type at all.

**P22** — Line 2 prints `91`. Line 3 crashes: `TypeError: list indices must be integers or slices, not str`. Note it is **not** `IndexError` — the problem is not a missing position, it is that `"1"` in quotes is a string and a position must be a number.

**P23** — `a` works; `b` raises `TypeError: unhashable type: 'list'`. A dictionary key must be **immutable**, because the container files the element by a number worked out from its contents and a changeable element would break that. A tuple is frozen, so it can be a key; a list can never be. This is the concrete reason tuples exist.

**P24** — It prints `x y` then `z w`, with **no error** — and no `1` or `2` anywhere. Looping plainly over a dictionary gives the **keys**, and `for k, v in …` promises each element splits into two pieces. `"xy"` is two characters, so it obligingly splits into `"x"` and `"y"`. With `"name"` and `"city"` it would crash instead: `ValueError: too many values to unpack (expected 2)`. Same bug, two outcomes — and the silent one is worse. The fix either way is `for k, v in d.items():`.

**P25** — Both `TypeError`, with different messages. `n[0]` → `'int' object is not subscriptable` (you cannot put `[…]` after it). `for q in 7:` → `'int' object is not iterable` (`for … in` cannot walk it). Two ways of saying "that is a single value, not a container".

**P26** — Line 1: no crash, prints `[1, 2, 3]` — `+` joins two lists just as it joins two strings. Line 2: `TypeError: can only concatenate list (not "int") to list` — Task 30's sentence with the type names swapped. Line 3: `ValueError: list.remove(x): x not in list` — `remove` searches for a **value**, and 5 is not there. Line 4: no crash, prints `2`.

---

## One-page reference

**The four built-in containers**

| | List | Tuple | Set | Dictionary |
|---|---|---|---|---|
| general name | ordered mutable sequence | ordered immutable sequence | unordered collection of unique elements | associative array / map |
| written | `[1, 2, 3]` | `(1, 2, 3)` | `{1, 2, 3}` | `{"a": 1}` |
| empty one | `[]` | `()` | `set()` | `{}` |
| ordered | yes | yes | **no** | insertion order |
| mutable | yes | **no** | yes | yes |
| duplicates | yes | yes | **no** | keys unique |
| reach an element | `x[0]` | `x[0]` | — | `x["a"]` |
| `for … in` gives | elements | elements | elements, any order | **keys** |
| error if missing | `IndexError` | `IndexError` | — | `KeyError` |

**The loop**

```python
for <name> in <collection>:
    <indented body>
```

- the **colon** ends the line; the **four-space indent** is the body
- the loop variable still exists after the loop, holding the last element
- `for m in marks:` — when you want the **elements**
- `for i in range(len(marks)):` — when you want the **positions**
- `for k, v in d.items():` — when you want **both halves of a pair**

**Counted loop by hand** (what C makes you write, and `for … in` saves you)

```python
i = 0
while i < len(marks):     # test:  <  not  <=
    print(marks[i])
    i = i + 1             # the step — forget it and it never ends
```

**Working on any container**

| | |
|---|---|
| `len(x)` | how many elements (pairs, for a dictionary) |
| `x[0]` | first element — ordered containers only |
| `x[-1]` | last element — ordered containers only |
| `e in x` | `True` / `False`; on a dictionary tests the **keys** |
| `for … in x` | walk it |
| `sorted(x)` | a new **list**, in order — how to get a fixed order out of a set |

**Python and C, side by side**

| | C array | Python list |
|---|---|---|
| knows its own length | no — you track it | yes — `len()` |
| element types | all the same | may be mixed |
| size | fixed at declaration | grows with `.append()` |
| `x[i]` is | address arithmetic (Task 11) | a lookup that checks the range |
| past the end | garbage, or a crash | `IndexError`, every time |
| set, dictionary | not built in | built in |

**Errors you can now name**

| Error | Means | Typical cause | When |
|---|---|---|---|
| `SyntaxError` | Python could not read the line | missing `:` after `for` | before running |
| `IndentationError` | the indenting does not add up | no indent after a colon, or an indent with no colon above | before running |
| `NameError` | no such name | a misspelled variable | while running |
| `TypeError` | wrong type for this operation | `t[0] = 9`, `s[0]`, `marks["0"]`, `n[0]`, `for x in 5`, `nums + 3`, `{[1,2]}` | while running |
| `AttributeError` | this type has no such ability | `t.append(…)`, `nums.add(…)` | while running |
| `IndexError` | no such **position** | `marks[len(marks)]` | while running |
| `KeyError` | no such **key** | `d["age"]` when there is no `age` | while running |
| `ValueError` | right type, bad content | `for k, v in d:`, `nums.remove(9)` | while running |
| `RuntimeError` | you changed the container mid-walk | adding a key inside `for k in d:` | while running |

**The four `TypeError` messages worth recognising**

| Message | Means |
|---|---|
| `'tuple' object does not support item assignment` | that container is immutable |
| `'set' object is not subscriptable` | that container has no positions |
| `'int' object is not subscriptable` / `not iterable` | that is not a container at all |
| `list indices must be integers or slices, not str` | a position is a number, not a string |
| `unhashable type: 'list'` | only immutable things can be set elements or dict keys |
| `can only concatenate list (not "int") to list` | a list joins only to a list |

**Reading any error, in three steps:** the **name** is the category → the **message** names the offending type or value → the **`^^^` marks** show which part of the line failed.

**Rules to keep:**
- A collection is one name holding many elements. Ordered means every element has a position.
- Indexes start at 0, so the last is `len - 1` — never `len`.
- In Python the **indentation is the block**, not just a habit.
- A list is changeable; a tuple is the same thing frozen.
- A set keeps each element once, in **no** order — never depend on how it prints.
- A dictionary is reached by key, and both `for … in` and `in` use its **keys**.
- An element can itself be a container; `d[1]["m"][0]` is three lookups, left to right.
- `{}` is an empty **dictionary**. An empty set is `set()`.
- Only **immutable** things — ints, strings, tuples — can be set elements or dictionary keys.
- `append` belongs to a list, `add` to a set. Using the wrong one is an `AttributeError`.
- Never add or remove elements while a `for … in` is walking the container.
- Read the error's **name** first, then the type or value its message names.
- Choose the container whose properties match the data — it tells the next reader what the data is.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| collection / container | సేకరణ / కంటైనర్ | ఒకే పేరుతో అనేక విలువలను కలిపి ఉంచేది |
| element / item | ఎలిమెంట్ | కంటైనర్ లోపలి ఒక్క విలువ |
| ordered collection | క్రమం ఉన్న సేకరణ | ప్రతి ఎలిమెంట్‌కూ ఒక స్థానం ఉన్నది |
| unordered collection | క్రమం లేని సేకరణ | స్థానాలే లేనిది — సెట్ లాంటిది |
| index | ఇండెక్స్ | ఎలిమెంట్ స్థానం — 0 నుండి మొదలు |
| indexed access | స్థానం ద్వారా చేరడం | `marks[0]` — స్థానంతో తీసుకోవడం |
| key access | కీ ద్వారా చేరడం | `student["name"]` — పేరుతో తీసుకోవడం |
| list | లిస్ట్ | క్రమం ఉన్న, మార్చగలిగే సేకరణ — `[1, 2]` |
| tuple | ట్యూపుల్ | క్రమం ఉన్న, మార్చలేని సేకరణ — `(1, 2)` |
| set | సెట్ | క్రమం లేని, పునరావృతం లేని సేకరణ — `{1, 2}` |
| dictionary | డిక్షనరీ | కీ–విలువ జతల సేకరణ — `{"a": 1}` |
| associative array / map | అసోసియేటివ్ అరే / మ్యాప్ | డిక్షనరీకి సాధారణ (అన్ని భాషల) పేరు |
| key | కీ | విలువను వెతకడానికి వాడే పేరు |
| value | విలువ | కీ కింద నిల్వ ఉన్నది |
| pair | జత | ఒక కీ + దాని విలువ = ఒక ఎలిమెంట్ |
| mutable | మార్చగలిగేది | తయారైన తర్వాత మార్చవచ్చు — లిస్ట్ |
| immutable | మార్చలేనిది | తయారైన తర్వాత మార్చలేము — ట్యూపుల్ |
| duplicate | పునరావృతం | ఒకే విలువ మళ్ళీ రావడం — సెట్‌లో ఉండదు |
| unique | ప్రత్యేకం | ఒక్కసారే ఉండేది |
| iterate / traverse | చుట్టి చూడటం | ఒక్కొక్క ఎలిమెంట్‌ను వరుసగా తీసుకోవడం |
| loop body | లూప్ బాడీ | ప్రతిసారీ నడిచే ఇండెంట్ చేసిన భాగం |
| indentation | ఇండెంటేషన్ | ముందున్న ఖాళీలు — పైథాన్‌లో ఇదే బ్లాక్ |
| nested | లోపల ఉన్న | ఒక కంటైనర్ లోపల మరో కంటైనర్ |
| membership (`in`) | సభ్యత్వం | "ఇది ఇందులో ఉందా?" — `True` / `False` |
| hashable | హాష్ చేయదగినది | సెట్‌లో / కీగా వాడగలిగేది — మారని విలువలే |
| attribute | అట్రిబ్యూట్ | పేరు తర్వాత చుక్కతో చేరేది — `.append()` లాంటిది |
| unpack | విడదీయడం | ఒక జతను రెండు పేర్లలోకి విడదీయడం — `for k, v in …` |
| `IndentationError` | ఇండెంటేషన్ ఎర్రర్ | ఇండెంట్ సరిపోలేదు — ప్రోగ్రామ్ మొదలే కాలేదు |
| `IndexError` | ఇండెక్స్ ఎర్రర్ | ఆ స్థానం లేదు |
| `KeyError` | కీ ఎర్రర్ | ఆ కీ లేదు |
| `AttributeError` | అట్రిబ్యూట్ ఎర్రర్ | ఈ రకానికి ఆ సామర్థ్యమే లేదు |
| `NameError` | నేమ్ ఎర్రర్ | ఆ పేరు లేదు — స్పెల్లింగ్ చూడండి |
| `RuntimeError` | రన్‌టైమ్ ఎర్రర్ | లూప్ నడుస్తుండగా కంటైనర్‌ను మార్చారు |
