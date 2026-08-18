# Linters, Formatters and Type Checkers — Questions

**Before you start:** read the reference page for Task 34 once, all the way through.

Every program in Part C is checked against the same project contract you saw there:

```toml
[tool.ruff]
line-length = 88

[tool.ruff.lint]
select = ["E", "F", "I", "B"]
```

Answer in your notebook. Where a question asks for a rule code, write the code exactly as the tool prints it (`F401`, `reportAssignmentType`).

---

## Part A — Multiple Choice

**A1.** Which question does `ruff format` answer?

- A. Is my code written properly?
- B. How should my code look?
- C. Are my types consistent?
- D. Does my program give the right answer?

**A2.** A file contains exactly these three lines, and nothing else:

```python
age: int = 20
age = "twenty"
print(age)
```

What does `ruff check .` print?

- A. `F401 'age' imported but unused`
- B. `E501 Line too long`
- C. `All checks passed!`
- D. `error: Type "Literal['twenty']" is not assignable to declared type "int"`

**A3.** Which of these will `ruff check . --fix` repair on its own, without asking you?

- A. `F401` — an imported module that is never used
- B. `B006` — a list used as a function's default argument
- C. `F841` — a variable assigned but never used
- D. `E501` — a line longer than the configured limit

**A4.** Where should the project's line length and rule selection be written, so that every student who clones the repository gets the same rules?

- A. in each student's editor settings
- B. as flags typed after `ruff check` every time
- C. in `pyproject.toml`, committed to the repository
- D. in a comment at the top of each `.py` file

**A5.** In `select = ["E", "F", "I", "B"]`, what does `E` bring in?

- A. unused imports and unused variables
- B. layout — line length and spacing
- C. the order of the import lines
- D. legal code that is almost certainly a bug

**A6.** You run `ruff check .` inside a script and it prints `All checks passed!`. What exit code did it return?

- A. `0`
- B. `1`
- C. `88`
- D. it depends on how many files were checked

**A7.** A project contains this function and nothing else:

```python
def shout(word):
    return word.upper()
```

`pyright .` is run twice — once with `typeCheckingMode = "basic"`, once with `"strict"`. What happens?

- A. both runs report the same errors
- B. `basic` reports errors, `strict` reports none
- C. `basic` reports none, `strict` reports several
- D. neither run reports anything; the code is valid Python

**A8.** You see this message in your terminal:

```text
marks.py:8:15 - error: Argument of type "Literal[85]" cannot be assigned to parameter "marks" of type "list[int]" in function "average" (reportArgumentType)
```

Which tool printed it?

- A. `ruff format`
- B. `ruff check`
- C. `pyright`
- D. Python itself, when the program ran

---

## Part B — Fill in the Blanks

**B1.** The command that repairs the problems Ruff is willing to repair by itself is
`ruff check . __________`.

**B2.** The command that type-checks every file in the project is `__________ .`

**B3.** The rule code for a module that is imported but never used is `__________`.

**B4.** The rule code for a line longer than the configured limit is `__________`.

**B5.** Ruff and Pyright both read their settings from a file named `__________`, which sits at the top of the project.

**B6.** To see what the formatter *would* change without changing anything, run
`ruff format __________ .`

**B7.** Both tools return exit code `__________` when they find nothing to report.

---

## Part C — Read the Program

For each program below, answer three things:

1. Which tool reports something — **Ruff**, **Pyright**, **both**, or **neither**?
2. The **rule code** it prints (if any).
3. What you would **change**, in one line.

Assume each program is the whole file, and that the project contract above is in force.

**C1.**

```python
import math
import random


def roll() -> int:
    return random.randint(1, 6)
```

**C2.**

```python
def add_tag(tag: str, tags: list[str] = []) -> list[str]:
    tags.append(tag)
    return tags
```

**C3.**

```python
count: int = 0
count = count + 1
total: int = count * 2
print(total)
```

**C4.**

```python
price: float = 99.5
price = "ninety nine"
print(price)
```

**C5.**

```python
def greet(name: str) -> str:
    return "Hello, " + name


print(greet(42))
```

**C6.**

```python
import json


def save(data: dict[str, int]) -> None:
    text = json.dumps(data)
```

**C7.**

```python
import os

total: int = 100
total = "one hundred"
print(total)
```
