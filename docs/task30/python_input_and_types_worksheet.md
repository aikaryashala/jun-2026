# Python `input()` and Types — Strings, Numbers, and How `print` Lays Them Out

**Goal.** Today you will find out what `input()` actually hands you, learn to ask any value "what are you?" with `type()`, move values between the two worlds with `int()` and `str()`, and take full control of `print` using `sep` and `end`. Along the way you will make two errors **on purpose** and learn to read what Python says back.

**You need:** your Linux VM (WSL), a terminal, `python3`, a notebook, and a pencil.

Check that Python is there:

```
python3 --version
```

You should see something like `Python 3.12.3`. Any `3.x` is fine.

Make a folder for today's programs:

```
mkdir -p ~/task30
cd ~/task30
```

You will write each program with `nano`, save it, and run it with `python3 filename.py`.

> **The golden rule of today**
> What a person types is always **text**. `input()` hands you a **string** — even when you type `12345`. A string that *looks* like a number is **not** a number. `int()` and `str()` carry a value between the two worlds, and `type()` tells you which world you are standing in.

---

## Iteration 1 — What does `input()` actually give you?

**a. What we set up**

```python
# ask_age.py
age = input("Age: ")
print(age)
print(type(age))
```

**b. Task**

Before running, write in your notebook: after you type `17` and press Enter, what will the **third** line print?

```
nano ask_age.py
python3 ask_age.py
```

Type `17` and press Enter.

**c. Observation (what you should find)**

```
Age: 17
17
<class 'str'>
```

The `17` printed back looks like a number. But Python says `<class 'str'>` — **str** means *string*, a piece of text. You typed the two characters `1` and `7`, and that is exactly what `input()` gave you.

Run it again and type `hello`. Same answer: `<class 'str'>`. `input()` does not care what you type — it **always** hands back a string.

**Takeaway to say out loud:** "`input()` always gives me a string, never a number."

---

## Iteration 2 — `type()` tells you which world a value is in

**a. What we set up**

```python
# show_types.py
num = 25
text = "25"

print(type(num))
print(type(text))
print(type("Balaji"))
print(type(2.5))
```

**b. Task**

Predict all four lines *before* running. Pay special attention to lines 1 and 2 — the value looks the same on the screen.

**c. Observation (what you should find)**

```
<class 'int'>
<class 'str'>
<class 'str'>
<class 'float'>
```

`25` and `"25"` print identically, but they are **different kinds of thing**. The quotes are not decoration — they decide the type.

- **int** — a whole number. `25`, `0`, `-17`.
- **str** — text. `"25"`, `"Balaji"`, `"hello"`. Written inside quotes.
- **float** — a number with a decimal point, like `2.5`. You will meet floats properly another day; today everything we build uses only **int** and **str**.

**Takeaway to say out loud:** "Quotes make it a string. `type()` tells me which one I have."

---

## Iteration 3 — `int()` and `str()` carry a value across

**a. What we set up**

```python
# convert.py
Balaji = "12345"
num = int(Balaji)
Govind = str(num)

print(Govind)
print(Balaji, num, Govind)
```

Read it line by line:

- `Balaji` is the **string** `"12345"`.
- `int(Balaji)` reads that text and builds the **number** `12345`. That new number goes into `num`.
- `str(num)` takes the number back to text. That new text goes into `Govind`.

Notice that `Balaji` never changed. `int()` does not *convert* a variable — it **makes a new value** from it.

**b. Task**

Predict both printed lines. Then run, then add this line at the end and run again:

```python
print(type(Balaji), type(num), type(Govind))
```

**c. Observation (what you should find)**

```
12345
12345 12345 12345
<class 'str'> <class 'int'> <class 'str'>
```

This is the important moment of the whole worksheet. **Three values that print identically, and one of them is a completely different kind of thing.** The screen cannot show you the difference. Only `type()` can.

**Takeaway to say out loud:** "Printing looks the same. The type is not the same."

---

## Iteration 4 — Same look, different behaviour

If `"12345"` and `12345` really are different, they should *behave* differently. Let's force them to show it.

**a. What we set up**

```python
# plus.py
print("12" + "5")
print(12 + 5)
```

**b. Task**

Predict both lines. Then run. Then add this third line and run again:

```python
print("12" + 5)
```

**c. Observation (what you should find)**

The first two lines:

```
125
17
```

Same `+` sign, two completely different jobs. Between **strings**, `+` **joins** them end to end — `"12"` followed by `"5"` gives `"125"`. Between **ints**, `+` **adds** — `12 + 5` is `17`.

The third line does not print at all. It crashes:

```
Traceback (most recent call last):
  File "/home/student/task30/plus.py", line 3, in <module>
    print("12" + 5)
TypeError: can only concatenate str (not "int") to str
```

**TypeError** means: the types don't fit together. Python cannot decide whether you wanted `"125"` or `17`, so it refuses to guess. *Concatenate* is the proper word for joining strings.

Fix it by choosing a world and putting both values in it:

```python
print("12" + str(5))     # both strings  → 125
print(int("12") + 5)     # both ints     → 17
```

**Takeaway to say out loud:** "`+` joins strings and adds ints — and it refuses to mix the two."

---

## Iteration 5 — `print`'s **separator**

You have already been printing several values at once. Now look closely at what sits *between* them.

**a. What we set up**

```python
# sep.py
print("Ravi", "Kumar")
print("Ravi", "Kumar", sep="-")
print("Ravi", "Kumar", sep="")
print(1, 2, 3, sep="+")
```

**b. Task**

Predict all four lines, counting spaces carefully. Then run.

**c. Observation (what you should find)**

```
Ravi Kumar
Ravi-Kumar
RaviKumar
1+2+3
```

When you give `print` several values separated by commas, it puts something **between** them. By default that something is a **single space**. `sep=` replaces it with whatever you choose — a dash, nothing at all, a plus sign.

Two things worth noticing:

- The commas in `print(a, b, c)` are how you hand `print` three values. They are not printed.
- `sep=""` (empty quotes, nothing inside) means "put nothing between them."

**Takeaway to say out loud:** "`sep` is what `print` puts *between* the values — a space unless I say otherwise."

---

## Iteration 6 — `print`'s **ending**

**a. What we set up**

```python
# end.py
print("Ravi", "Kumar", sep="", end="")
print("Reddy")
```

**b. Task**

Predict the output. How many lines will appear on screen?

Then run this second file too:

```python
# end2.py
print("x", end="")
print("y", end="")
print("z")
print()
print("after blank")
```

**c. Observation (what you should find)**

`end.py` prints **one** line:

```
RaviKumarReddy
```

`end2.py` prints:

```
xyz

after blank
```

Every `print` finishes with something. By default it finishes with a **newline** — that is why each `print` normally starts a fresh line. `end=""` says "finish with nothing," so the next `print` continues on the same line.

And `print()` with nothing inside it prints no values at all — but it still prints its ending, a newline. That is how you get a blank line.

So every `print` call is really: *the values, joined by `sep`, followed by `end`.*

**Takeaway to say out loud:** "`sep` goes between the values, `end` goes after all of them — a newline unless I say otherwise."

---

## Iteration 7 — Making an error on purpose: a reserved word

**a. What we set up**

```python
# bad_name.py
in = "hi123"
num = int(in)
print(num)
```

**b. Task**

Predict what happens. Then run it.

**c. Observation (what you should find)**

Nothing runs at all — not even the first line:

```
  File "/home/student/task30/bad_name.py", line 1
    in = "hi123"
    ^
SyntaxError: invalid syntax
```

*(Newer Python versions may underline with `^^` instead of a single `^`. The message word is the same.)*

`in` is a **reserved word** — a word Python has already claimed for its own grammar. You cannot use it as a variable name. Python could not even understand the line, so it stopped before running anything. That is what **SyntaxError** means: *I could not read this as Python.*

Compare with the errors you saw before: a `TypeError` happens *while the program runs*, so earlier lines have already printed. A `SyntaxError` happens *before the program starts*, so **nothing** prints.

Fix it by choosing a name that is not reserved:

```python
text = "hi123"
```

Other reserved words you must not use as variable names: `if`, `else`, `for`, `while`, `class`, `def`, `import`, `return`, `and`, `or`, `not`, `is`, `in`, `True`, `False`, `None`.

**Takeaway to say out loud:** "`SyntaxError` means Python couldn't even read my line — nothing ran."

---

## Iteration 8 — Making an error on purpose: `int()` on text that isn't a number

**a. What we set up**

```python
# bad_convert.py
text = "hi123"
num = int(text)
print(num)
```

**b. Task**

Predict. Then run.

**c. Observation (what you should find)**

```
Traceback (most recent call last):
  File "/home/student/task30/bad_convert.py", line 2, in <module>
    num = int(text)
ValueError: invalid literal for int() with base 10: 'hi123'
```

**ValueError** means: the *type* was right (you gave `int()` a string, which is what it wants), but the **value** inside was wrong. `"hi123"` is not a number written in digits, so there is nothing for `int()` to build.

Now test the edges. Try each of these one at a time and note which work:

| You write | What happens |
|---|---|
| `int("42")` | `42` — works |
| `int("  42  ")` | `42` — works, spaces around are ignored |
| `int("-17")` | `-17` — works, a minus sign is allowed |
| `int("07")` | `7` — works, leading zeros are fine |
| `int("42abc")` | **ValueError** — one stray letter is enough |
| `int("3 4")` | **ValueError** — a space *inside* breaks it |
| `int("")` | **ValueError** — empty string, nothing to read |

So `int()` is strict: the whole string must be a number, with only spaces allowed around the outside.

Notice too that the error message tells you exactly which value failed — `'hi123'` is printed right there at the end. Read your error messages; they name the culprit.

**Takeaway to say out loud:** "`ValueError` means the string wasn't a number — `int()` needs digits, all the way through."

---

## Iteration 9 — Put it all together

**a. What we set up**

```python
# add_them.py
first = input("First number: ")
second = input("Second number: ")
print("Sum:", first + second)
```

**b. Task**

Run it. Type `12`, press Enter, type `5`, press Enter. Predict the answer first.

**c. Observation (what you should find)**

```
First number: 12
Second number: 5
Sum: 125
```

`125`, not `17`. Nothing crashed — and that is what makes this bug dangerous. `input()` gave you two **strings**, and `+` between strings **joins** them. The program is happily doing the wrong thing.

Fix it by converting before calculating:

```python
# add_them.py  (fixed)
first = input("First number: ")
second = input("Second number: ")
total = int(first) + int(second)
print("Sum:", total)
```

```
First number: 12
Second number: 5
Sum: 17
```

**Takeaway to say out loud:** "Convert before you calculate — `input()` then `int()`, every single time."

---

## Practice — Predict the output

For each snippet, write the **exact** output in your notebook *before* running it. Count spaces. Count lines. Then run each one and compare. If you were wrong, work out which rule you forgot — that is the whole point of this exercise.

**P1.**
```python
a = "7"
b = 7
print(a, b)
print(a + a)
print(b + b)
```

**P2.**
```python
x = "100"
y = int(x)
z = str(y)
print(x, y, z, sep="|")
```

**P3.**
```python
print("Ravi", "Kumar", sep="")
print("Ravi", "Kumar")
print("Ravi", "Kumar", sep="  ")
```

**P4.**
```python
print(1, 2, sep="", end="")
print(3, 4, sep="", end="")
print(5)
```

**P5.**
```python
n = "25"
print(type(n))
print(type(int(n)))
print(type(str(int(n))))
```

**P6.**
```python
print("10" + "20")
print(int("10") + int("20"))
print(str(10 + 20))
```

**P7.**
```python
print("A", 1, "B", 2, sep="-")
print("A", 1, "B", 2, sep="", end="!")
print()
```

**P8.**
```python
marks = "88"
total = int(marks) + 12
print("Total:", total)
print("Total:" + str(total))
```

**P9.**
```python
print("x", end="")
print("y", end="")
print("z")
```

**P10.**
```python
print(1, 2, 3, sep=", ", end=".\n")
print("done")
```

**P11.**
```python
s = "5"
print(s * 3)
print(int(s) * 3)
```

**P12.**
```python
print("a", "b", "c", sep="\n")
```

**P13.**
```python
v = 9
print(str(v) + str(v))
print(int(str(v) + str(v)))
```

**P14.** Which of these four lines crash, and with which error name?
```python
print("age: " + 21)
print(int("twenty"))
print("5" - "2")
print(str(5) + "5")
```

---

### Self-check

**Cover this section with your hand until every prediction above is written down.** Checking as you go teaches you nothing.

**P1** — `7 7` / `77` / `14`. Line 1: two values, default space between. Line 2: strings join. Line 3: ints add.

**P2** — `100|100|100`. Three different values (str, int, str), one look, joined by `|`.

**P3** — `RaviKumar` / `Ravi Kumar` / `Ravi  Kumar` (two spaces — `sep` is exactly what you gave).

**P4** — one line: `12345`. Both `end=""` lines refuse to break; the last `print` supplies the newline.

**P5** — `<class 'str'>` / `<class 'int'>` / `<class 'str'>`. `int()` then `str()` returns to text.

**P6** — `1020` / `30` / `30`. Line 1 joins. Line 2 adds. Line 3 adds *first* (inside the brackets), then turns `30` into text — so it looks identical to line 2 on screen, but it is a string.

**P7** — `A-1-B-2` / `A1B2!`. On line 2, `end="!"` means no newline, so the bare `print()` on line 3 supplies it.

**P8** — `Total: 100` / `Total:100`. Line 1 has `print`'s default space; line 2 joins with no space because *you* did the joining, not `print`.

**P9** — `xyz`, one line.

**P10** — `1, 2, 3.` then `done`. The `\n` inside `end` is the newline you would otherwise have got free.

**P11** — `555` / `15`. `*` on a string **repeats** it; on ints it multiplies.

**P12** — `a`, `b`, `c` on three lines. `sep` can be a newline too.

**P13** — `99` / `99`. Line 1 builds the string `"99"`. Line 2 builds the same string, then `int()` turns it into the number `99`. Identical on screen, different types.

**P14** — Line 1: **TypeError** (str + int). Line 2: **ValueError** (`"twenty"` has no digits). Line 3: **TypeError** — `-` has no meaning between two strings. Line 4: no crash, prints `55`.

---

## One-page reference

| Thing | What it does |
|---|---|
| `input()` | waits for the user, returns what they typed — **always a string** |
| `input("Age: ")` | the same, but prints `Age: ` first as a prompt |
| `int(s)` | builds a **new int** from a string of digits |
| `str(n)` | builds a **new string** from a number |
| `type(v)` | tells you what kind of value `v` is |
| `print(a, b)` | prints the values with `sep` between and `end` after |
| `sep="..."` | what goes **between** the values (default: one space `" "`) |
| `end="..."` | what goes **after** all the values (default: newline `"\n"`) |
| `print()` | prints nothing, then its ending — i.e. a blank line |

**Types you have met:** `int` (whole number, no quotes) · `str` (text, in quotes) · `float` (has a decimal point — for another day).

**Operators behave by type:**

| | on two `str` | on two `int` |
|---|---|---|
| `+` | joins: `"12" + "5"` → `"125"` | adds: `12 + 5` → `17` |
| `*` | repeats: `"5" * 3` → `"555"` | multiplies: `5 * 3` → `15` |
| `-` | **TypeError** | subtracts |

**The three errors you can now name:**

| Error | Means | When it happens |
|---|---|---|
| `SyntaxError` | Python couldn't read the line | **before** running — nothing prints |
| `TypeError` | wrong kinds of value together | while running |
| `ValueError` | right kind, bad content | while running |

**Rules to keep:**
- `input()` always returns a string. Always.
- Quotes decide the type: `25` is an int, `"25"` is a str.
- The screen cannot show you a type — only `type()` can.
- Convert **before** you calculate: `int(input(...))`.
- `int()` needs digits all the way through; spaces are allowed only around the outside.
- Read the error's **name** first, then the value it names at the end of the line.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| input | ఇన్‌పుట్ | వాడుకరి టైప్ చేసిన దాన్ని తీసుకోవడం — ఎప్పుడూ స్ట్రింగ్‌గా |
| string (`str`) | స్ట్రింగ్ / అక్షరాల వరుస | కోట్స్‌లో ఉండే వచనం — `"25"`, `"Balaji"` |
| integer (`int`) | పూర్ణాంకం | కోట్స్ లేని పూర్ణ సంఖ్య — `25`, `-17` |
| float | ఫ్లోట్ | దశాంశ బిందువు ఉన్న సంఖ్య — `2.5` (తర్వాత చూద్దాం) |
| type | రకం | ఒక విలువ ఏ జాతిదో — `type()` చెబుతుంది |
| convert | మార్చడం | ఒక రకం నుండి మరో రకానికి — `int()`, `str()` |
| separator (`sep`) | విభాజకం | `print` విలువల **మధ్య** పెట్టేది (డిఫాల్ట్: ఒక ఖాళీ) |
| ending (`end`) | ముగింపు | `print` అన్నిటి **తర్వాత** పెట్టేది (డిఫాల్ట్: కొత్త లైన్) |
| newline | కొత్త లైన్ | తదుపరి వరుసకు వెళ్లే గుర్తు — `\n` |
| concatenate | కలపడం | రెండు స్ట్రింగ్‌లను వరుసగా జోడించడం — `"12" + "5"` |
| reserved word | రిజర్వ్డ్ పదం | పైథాన్ తనకోసం ఉంచుకున్న పదం — `in`, `if`, `for` |
| `SyntaxError` | సింటాక్స్ ఎర్రర్ | లైన్ చదవలేకపోయింది — ప్రోగ్రామ్ మొదలే కాలేదు |
| `TypeError` | టైప్ ఎర్రర్ | పొంతన లేని రకాలను కలిపారు |
| `ValueError` | వాల్యూ ఎర్రర్ | రకం సరైనదే, కానీ లోపలి విలువ తప్పు |
