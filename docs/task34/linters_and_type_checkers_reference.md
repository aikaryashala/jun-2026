# Linters, Formatters and Type Checkers — Letting a Machine Read Your Code First

**This page is reading material, not a worksheet.** There are no iterations and no practice section. It explains the three tools that read your Python code *before you run it*, and how one small file — `pyproject.toml` — turns our coding standard into something a machine checks instead of something you have to remember.

**Assumed:** Task 32 (Why We Should Follow Type Hints in Python). That task gave us a rule: *every variable has a clearly intended type, and that type does not change*. Task 32 asked you to follow it. This task hands the rule to a machine.

**The commands here are real.** Every piece of output on this page came from an actual run — `ruff` 0.16.3 and `pyright` 1.1.411. You are welcome to type them yourself on your VM; nothing here will damage anything.

> **The golden rule of this page**
> Three tools, three different questions — and they are **not** interchangeable.
> A **formatter** answers *"how should my code look?"*
> A **linter** answers *"is my code written properly?"*
> A **type checker** answers *"are my types consistent?"*
> A linter will read `age: int = 20` followed by `age = "twenty"` and say **"All checks passed!"** — because that is not its question.

---

## 1. Three questions, three tools

Beginners meet all of these as "the thing that shows red squiggles in the editor" and assume they are one tool. They are not. Each one refuses to answer the others' question.

| Tool | The question it answers | Example of what it catches |
|---|---|---|
| **Formatter** (`ruff format`) | How should my code *look*? | inconsistent spacing, a line that runs off the screen |
| **Linter** (`ruff check`) | Is my code *written properly*? | an import you never used, a variable you assigned and forgot |
| **Type checker** (`pyright`) | Are my types *consistent*? | `age: int = 20` then `age = "twenty"` |

Two of these are the same program. **Ruff** is both the formatter and the linter — `ruff format` and `ruff check` are two different commands of one tool. **Pyright** is a separate program entirely, and it is the only one of the three that understands what `list[int]` *means*.

Drawn as one picture:

```text
                 Python Program
                       │
              ┌────────┴────────┐
              ↓                 ↓
            Ruff             Pyright
              │                 │
        Code quality        Type correctness
        Common errors       Type consistency
        Imports             Function contracts
        Style               Data structures
              │                 │
              └────────┬────────┘
                       ↓
                  Good Python
```

**Say it out loud:** "Ruff asks how it is written. Pyright asks whether the types agree. Neither one answers the other's question."

---

## 2. Installing them on your VM

Both tools are ordinary programs you install once. Inside your project's virtual environment:

```
$ pip install ruff pyright
```

Then check that they are really there. This is the output from the versions used throughout this page:

```
$ ruff --version
ruff 0.16.3

$ pyright --version
pyright 1.1.411
```

A note about Pyright: it is written in TypeScript, so the first time you run it, it quietly downloads a small copy of Node.js for itself. That first run is slow. Every run after it is fast. Nothing is wrong.

**Say it out loud:** "Two installs, two `--version` checks. If both print a version, I am ready."

---

## 3. `pyproject.toml` — the coding contract

Here is the project we will use for the rest of this page:

```text
my-project/
├── pyproject.toml
├── main.py
└── student.py
```

The rules live in **`pyproject.toml`** at the top of the project:

```toml
[tool.ruff]
line-length = 88

[tool.ruff.lint]
select = [
    "E",      # pycodestyle
    "F",      # Pyflakes
    "I",      # import sorting
    "B",      # bugbear
]
```

Those single letters each switch on a whole family of rules:

| Letter | Family | What it watches for |
|---|---|---|
| `E` | pycodestyle | layout: line too long, spacing around operators |
| `F` | Pyflakes | real mistakes: unused imports, unused variables, undefined names |
| `I` | isort | imports in a sensible, sorted order |
| `B` | flake8-bugbear | traps that are legal Python but almost always a bug |

This file is the important idea of the whole task. It is **checked into the repository**, so it is not *your* settings — it is **the project's** settings. Everyone who clones the repo gets the same rules, and the editor, the terminal and (later) the server all read this one file. Nobody argues about line length in a review, because the file already decided.

Ruff finds it by itself. You never pass it on the command line: run `ruff` anywhere inside the project and it walks up the folders until it finds `pyproject.toml`.

**Say it out loud:** "`pyproject.toml` is the coding contract for the whole project, not a setting on my laptop."

---

## 4. Ruff, on a file with real problems

Here is `student.py`. It runs. Python has no complaint about it at all:

```python
import sys
import json
import os


def add_student(name: str, marks: list[int] = []) -> dict[str, object]:
    total = 0
    for m in marks:
        total = total + m
    average = total / len(marks)
    label = "pass"
    return {"name": name, "marks": marks, "average": average}


def report(students: list[dict[str, object]]) -> None:
    for s in students:
        print(json.dumps(s))
        print("Student " + str(s["name"]) + " has an average of " + str(s["average"]) + " marks out of a hundred")
```

Now let the linter read it:

```
$ ruff check .
I001 [*] Import block is un-sorted or un-formatted
 --> student.py:1:1
  |
1 | / import sys
2 | | import json
3 | | import os
  | |_________^
help: Organize imports

F401 [*] `sys` imported but unused
 --> student.py:1:8
  |
1 | import sys
  |        ^^^
help: Remove unused import: `sys`

F401 [*] `os` imported but unused
 --> student.py:3:8
  |
3 | import os
  |        ^^
help: Remove unused import: `os`

B006 Do not use mutable data structures for argument defaults
 --> student.py:6:47
  |
6 | def add_student(name: str, marks: list[int] = []) -> dict[str, object]:
  |                                               ^^
help: Replace with `None`; initialize within function

F841 Local variable `label` is assigned to but never used
  --> student.py:11:5
   |
11 |     label = "pass"
   |     ^^^^^
help: Remove assignment to unused variable `label`

E501 Line too long (114 > 88)
  --> student.py:18:89

Found 6 errors.
[*] 3 fixable with the `--fix` option (2 hidden fixes can be enabled with the `--unsafe-fixes` option).
```

Six findings in a file Python was perfectly happy to run. Read them one at a time:

- **`I001` — imports not sorted.** `sys`, `json`, `os` is not alphabetical. Trivial, but a machine can keep it perfect forever, so why should a human think about it.
- **`F401` — imported but unused.** Twice. We imported `sys` and `os` and never used either. Harmless today; misleading to the next reader, who assumes the file touches the operating system.
- **`B006` — mutable default argument.** This is the serious one. `marks: list[int] = []` creates **one** list when the function is *defined*, not a fresh one per call — so every caller who leaves `marks` out shares the same list. This is legal Python, it runs, and it is almost never what anyone meant.
- **`F841` — assigned but never used.** `label = "pass"` was computed and thrown away. Usually the sign of an unfinished thought: someone meant to put it in the returned dictionary.
- **`E501` — line too long.** 114 characters where the contract said 88.

Notice the shape of every message: **rule code**, then `file:line:column`, then the offending line with a `^^^` pointing at exactly the spot, then a `help:` line saying what to do. That shape never changes, so once you can read one message you can read all of them.

**Say it out loud:** "Every finding is a rule code, a place, and a suggestion. The file ran fine — that was never the question."

---

## 5. What the machine may fix, and what it refuses to guess

Look again at the last line of that report: `3 fixable with the --fix option`. Ruff is telling you which of the six it is willing to repair by itself. Ask it to:

```
$ ruff check . --fix
B006 Do not use mutable data structures for argument defaults
 --> student.py:4:47
F841 Local variable `label` is assigned to but never used
  --> student.py:9:5
E501 Line too long (114 > 88)
  --> student.py:16:89

Found 5 errors (2 fixed, 3 remaining).
```

The two unused imports are gone from the file. And so is `I001` — once `sys` and `os` were deleted, only `import json` was left, and a single import is trivially sorted. One fix removed two findings.

Now the formatter takes its turn:

```
$ ruff format .
1 file reformatted, 1 file left unchanged
```

It rewrote the over-long `print` into something that fits:

```python
        print(
            "Student "
            + str(s["name"])
            + " has an average of "
            + str(s["average"])
            + " marks out of a hundred"
        )
```

`E501` is now gone too. Check once more:

```
$ ruff check .
B006 Do not use mutable data structures for argument defaults
F841 Local variable `label` is assigned to but never used

Found 2 errors.
```

Two left — and these two are left **on purpose**. Look at what fixing them would require:

- For `B006`, the machine would have to change the function's behaviour. Should the default be `None` and a new list made inside? Should the parameter be required instead? Those are different programs, and only you know which one you meant.
- For `F841`, deleting `label = "pass"` is one answer. Putting it into the returned dictionary is another — and more likely what the author intended. Deleting a line is not a safe guess when the line was probably meant to be *used*.

This is the boundary worth memorising: **a tool may rewrite what cannot change the meaning of your program.** Anything past that line, it points at and stops. (That is also what "unsafe fixes" means — repairs Ruff *can* make but which might change behaviour, so it will not make them unless you insist.)

Fixed by hand, `add_student` becomes:

```python
def add_student(name: str, marks: list[int] | None = None) -> dict[str, object]:
    if marks is None:
        marks = []
    total = 0
    for m in marks:
        total = total + m
    average = total / len(marks)
    return {"name": name, "marks": marks, "average": average}
```

And now:

```
$ ruff check .
All checks passed!
```

**Say it out loud:** "The machine fixes what cannot change the meaning. The rest it points at, because only I know what I meant."

---

## 6. The one thing Ruff will never tell you

Here is `main.py` — the exact thing Task 32 told us never to do:

```python
age: int = 20
age = "twenty"
print(age)
```

Ask the linter about it:

```
$ ruff check main.py
All checks passed!
```

**All checks passed.** Sit with that for a moment. The file breaks our single most important coding rule, and the linter approves it — not because Ruff is weak, but because this is not Ruff's question. Nothing here is badly *written*: no unused import, no long line, nothing out of order. The mistake is in the **types**, and a linter does not track types.

The same blindness in a more expensive form:

```python
def average(marks: list[int]) -> float:
    return sum(marks) / len(marks)


scores: list[int] = [80, 90, 85]
print(average(scores))

print(average(85))

result: int = average(scores)
```

```
$ ruff check .
All checks passed!
```

Two of those lines are wrong. `average(85)` passes a number where the function's contract clearly says `list[int]`, and it will crash when it runs. `result: int = average(scores)` stores a `float` in something declared `int`. Ruff sees no problem, and it is right not to — that is somebody else's job.

**Say it out loud:** "'All checks passed' means the linter's checks passed. It says nothing about my types."

---

## 7. Pyright answers the type question

Same files. Different tool:

```
$ pyright .
/home/student/my-project/main.py
  /home/student/my-project/main.py:2:7 - error: Type "Literal['twenty']" is not assignable to declared type "int"
    "Literal['twenty']" is not assignable to "int" (reportAssignmentType)
1 error, 0 warnings, 0 informations
```

There it is. Line 2, column 7 — the string. `Literal['twenty']` is Pyright being precise: it knows not just that this is a `str`, but *which* `str`. And `is not assignable to declared type "int"` is Pyright quoting **your own annotation** back at you. You declared `int`; you assigned a string; that is the whole complaint.

Now the `average` file:

```
$ pyright .
/home/student/my-project/marks.py
  /home/student/my-project/marks.py:8:15 - error: Argument of type "Literal[85]" cannot be assigned to parameter "marks" of type "list[int]" in function "average"
    "Literal[85]" is not assignable to "list[int]" (reportArgumentType)
  /home/student/my-project/marks.py:10:15 - error: Type "float" is not assignable to declared type "int"
    "float" is not assignable to "int" (reportAssignmentType)
2 errors, 0 warnings, 0 informations
```

Both mistakes, caught before the program ran once. And notice **how** they were caught: Pyright never executed `average`. It read the line `def average(marks: list[int]) -> float:` and treated it as a promise. The type hints you wrote in Task 32 are what made this possible — with them removed, Pyright would have nothing to check against and would fall silent, exactly like Ruff.

That is the sentence to carry out of this task: **a type hint is not a comment. It is a claim, and Pyright is the thing that tests the claim.**

**Say it out loud:** "The annotation is the promise. Pyright checks that the rest of the code keeps it."

---

## 8. Reading the messages

The two tools print in different shapes, but both say the same four things.

**Ruff:**

```text
F401 [*] `sys` imported but unused
 --> student.py:1:8
  ↑        ↑     ↑ ↑
  code   file  line column
```

**Pyright:**

```text
main.py:2:7 - error: Type "Literal['twenty']" is not assignable to declared type "int" (reportAssignmentType)
   ↑   ↑ ↑            ↑                                                                        ↑
 file line col     what it found                                                          rule name
```

In both cases: **where**, **what**, and **which rule**. The rule code is the useful part — `F401`, `E501`, `reportAssignmentType` are searchable. Paste one into a search engine and you get the rule's page, with why it exists and how to fix it. You are never stuck with a message you cannot look up.

Both tools also speak to the shell in the ordinary Unix way you already know from Task 5 and Task 13: **exit code 0 when clean, non-zero when they found something.** That is what lets these commands sit in a script, or in a check that runs automatically on every pull request.

**Say it out loud:** "Where, what, which rule — and the rule code is searchable."

---

## 9. `basic` and `strict`

Pyright's strictness is a setting, and it lives in the same contract file:

```toml
[tool.pyright]
typeCheckingMode = "basic"
```

Consider a file with no annotations at all:

```python
def shout(word):
    return word.upper()
```

In `basic`, Pyright says nothing about it. It cannot know what `word` is, so it declines to guess and moves on. Switch one word in `pyproject.toml`:

```toml
typeCheckingMode = "strict"
```

```
$ pyright .
/home/student/my-project/helper.py
  helper.py:1:5 - error: Return type is unknown (reportUnknownParameterType)
  helper.py:1:11 - error: Type of parameter "word" is unknown (reportUnknownParameterType)
  helper.py:1:11 - error: Type annotation is missing for parameter "word" (reportMissingParameterType)
  helper.py:2:12 - error: Type of "upper" is unknown (reportUnknownMemberType)
  helper.py:2:12 - error: Return type is unknown (reportUnknownVariableType)
```

Five complaints about a two-line function. In `strict`, "I don't know what this is" is itself an error. Same code, same tool — a different answer, because you changed what counts as acceptable.

For your own projects, start on `basic` and move to `strict` when the code is fully annotated. `strict` on an old unannotated project produces hundreds of errors and teaches nothing. `strict` on code written the Task 32 way produces almost none — which is rather the point.

**Say it out loud:** "Strictness is a choice written in the file, not a property of the tool."

---

## 10. Where each tool sits in the workflow

Put the pieces in the order you actually use them:

```text
AI generates code
       ↓
   ruff format      ← make it look right
       ↓
   ruff check       ← is it written properly?
       ↓
    pyright         ← do the types agree?
       ↓
     tests          ← does it do the right thing?
       ↓
    commit
```

The order is not decoration. Formatting first means the linter is not distracted by layout. The linter next, because an unused import is cheaper to find than a type error. Pyright after that. Only then tests, which are the slowest and the most valuable.

And they really are ordered by *what they can know*. Take our corrected `add_student` — it passes everything:

```
$ ruff check .
All checks passed!
$ pyright .
0 errors, 0 warnings, 0 informations
```

Then you call it with no marks at all:

```
>>> add_student("Ravi")
  File "/home/student/my-project/student.py", line 10, in add_student
    average = total / len(marks)
ZeroDivisionError: division by zero
```

Clean on both tools, and it still crashed. Neither tool ever claimed otherwise: an empty list is a perfectly valid `list[int]`, so there was nothing for Pyright to object to. **Dividing by its length is a mistake about meaning, not about types** — and mistakes about meaning are what tests are for. That is exactly why `tests` sits below `pyright` in the diagram and not above it.

This chain matters more, not less, when the code came from an AI. You cannot read every generated line as carefully as one you typed yourself — but the format, lint and type steps read all of it, every time, at the same standard. Your type hints become the specification the generated code is measured against.

**Say it out loud:** "Format, lint, type-check, test — cheapest question first, and each one only answers its own."

---

## 11. The same rules, inside the editor

Everything so far ran in the terminal, *after* the code was written. Both tools also have editor extensions — Ruff and Pylance/Pyright in VS Code — and they read the very same `pyproject.toml`. No second configuration, no chance of the editor and the terminal disagreeing.

With them on, the error arrives while you are still typing:

```text
student.py

age: int = 20
age = "twenty"
      ^^^^^^^^
      Type "Literal['twenty']" is not assignable to declared type "int"
```

Same message, same rule, seconds after the mistake instead of minutes. The terminal commands stay important — they are what a script or a server runs, and they are the truth — but the editor is where you want to *meet* these errors.

**Say it out loud:** "One config file, read by the editor and the terminal, so they can never disagree."

---

## 12. Back to Task 32

Task 32 ended with a coding standard. Here is what each part of it looks like now that a machine is watching:

| Task 32 said | Now enforced by |
|---|---|
| Use type hints for variables | Pyright (and `strict` if you want it required) |
| Keep a variable's intended type stable | Pyright — `reportAssignmentType` |
| Specify parameter and return types | Pyright — `reportArgumentType`, plus `strict` for the missing ones |
| Specify element types for lists and dicts | Pyright, comparing `list[int]` against what you pass |
| Use a type checker in larger projects | this task |

Nothing about the standard changed. What changed is who remembers it. A rule you have to hold in your head is broken on the day you are tired; a rule in `pyproject.toml` is checked on every file, every run, for everyone on the project, forever.

**Say it out loud:** "Task 32 chose the rules. Task 34 stopped me from having to remember them."

---

## One-page reference

**The three questions**

| Tool | Command | Question |
|---|---|---|
| Formatter | `ruff format .` | How should my code look? |
| Linter | `ruff check .` | Is my code written properly? |
| Type checker | `pyright .` | Are my types consistent? |

**Everyday commands**

| Command | What it does |
|---|---|
| `ruff check .` | lint the whole project |
| `ruff check main.py` | lint one file |
| `ruff check . --fix` | lint and repair what is safe to repair |
| `ruff format .` | reformat every file |
| `ruff format --check .` | say what *would* change; change nothing |
| `pyright .` | type-check the whole project |
| `pyright main.py` | type-check one file |

Exit code `0` = clean, non-zero = findings. Both tools search upward for `pyproject.toml`, so run them from anywhere inside the project.

**The rule families we switch on**

| Letter | Family | Watches for |
|---|---|---|
| `E` | pycodestyle | line length, spacing |
| `F` | Pyflakes | unused imports, unused variables, undefined names |
| `I` | isort | import order |
| `B` | bugbear | legal code that is almost certainly a bug |

**Rule codes seen in this task**

| Code | Meaning | Auto-fixable |
|---|---|---|
| `I001` | import block un-sorted | yes |
| `F401` | imported but unused | yes |
| `F841` | assigned but never used | no — you may have meant to use it |
| `E501` | line too long | by the **formatter**, not by `--fix` |
| `B006` | mutable default argument | no — the repair changes behaviour |
| `reportAssignmentType` | value does not match the declared type | Pyright |
| `reportArgumentType` | argument does not match the parameter type | Pyright |

**The contract file**

```toml
[tool.ruff]
line-length = 88

[tool.ruff.lint]
select = ["E", "F", "I", "B"]

[tool.pyright]
typeCheckingMode = "basic"    # "strict" once the code is fully annotated
```

**The workflow**

```text
format  →  lint  →  type-check  →  test  →  commit
```

**Three sentences worth remembering**

- A linter that says *All checks passed!* has said nothing about your types.
- A tool fixes only what cannot change the meaning of your program.
- Code that passes both tools can still be wrong — that is what tests are for.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | సులభమైన అర్థం |
|---|---|---|
| linter | లింటర్ | కోడ్ సరిగ్గా రాశారా అని తనిఖీ చేసే సాధనం |
| formatter | ఫార్మాటర్ | కోడ్‌ను ఒకే అమరికలో సర్దే సాధనం |
| type checker | టైప్ చెకర్ | టైప్‌లు ఒకదానికొకటి సరిపోతున్నాయా అని చూసే సాధనం |
| Ruff | రఫ్ | మన లింటర్ మరియు ఫార్మాటర్ — ఒకే ప్రోగ్రామ్ |
| Pyright | పైరైట్ | మన టైప్ చెకర్ — వేరే ప్రోగ్రామ్ |
| rule code | రూల్ కోడ్ | ప్రతి నియమానికి ఉన్న చిన్న పేరు (`F401`, `E501`) |
| diagnostic | డయాగ్నొస్టిక్ | సాధనం చూపే ఒక సందేశం — ఎక్కడ, ఏమిటి, ఏ నియమం |
| `pyproject.toml` | పైప్రాజెక్ట్ ఫైల్ | ప్రాజెక్ట్ నియమాలు రాసి ఉంచే ఫైల్ |
| configuration | కాన్ఫిగరేషన్ | సాధనం ఎలా పని చేయాలో చెప్పే అమరిక |
| coding contract | కోడింగ్ ఒప్పందం | జట్టు అందరూ పాటించే ఉమ్మడి నియమాలు |
| autofix (`--fix`) | ఆటోఫిక్స్ | సాధనం తనంతట తానే సరిచేయడం |
| unsafe fix | అన్‌సేఫ్ ఫిక్స్ | ప్రోగ్రామ్ అర్థం మారే అవకాశం ఉన్న సవరణ |
| unused import | అనవసర ఇంపోర్ట్ | తెచ్చి ఎక్కడా వాడని లైబ్రరీ |
| unused variable | వాడని వేరియబుల్ | విలువ ఇచ్చి ఎక్కడా వాడని పేరు |
| mutable default | మార్చగల డిఫాల్ట్ | ఫంక్షన్ డిఫాల్ట్‌గా పెట్టిన లిస్ట్/డిక్ట్ — ప్రమాదకరం |
| line length | లైన్ పొడవు | ఒక లైన్‌లో అనుమతించే గరిష్ఠ అక్షరాల సంఖ్య |
| import order | ఇంపోర్ట్ క్రమం | ఇంపోర్ట్‌లను ఒక క్రమంలో పేర్చడం |
| annotation | అనోటేషన్ | `age: int` లాంటి టైప్ గుర్తు — ఒక వాగ్దానం |
| function contract | ఫంక్షన్ ఒప్పందం | ఫంక్షన్ ఏమి తీసుకుంటుంది, ఏమి తిరిగి ఇస్తుంది |
| strict mode | స్ట్రిక్ట్ మోడ్ | కఠినమైన తనిఖీ — టైప్ తెలియకపోవడమే తప్పు |
| basic mode | బేసిక్ మోడ్ | సాధారణ తనిఖీ — తెలియనివి వదిలేస్తుంది |
| exit code | ఎగ్జిట్ కోడ్ | కమాండ్ ముగిసినప్పుడు ఇచ్చే సంఖ్య — `0` అంటే సరే |
| editor integration | ఎడిటర్ ఇంటిగ్రేషన్ | రాస్తుండగానే ఎడిటర్‌లో తప్పులు కనబడటం |
| runtime error | రన్‌టైమ్ ఎర్రర్ | ప్రోగ్రామ్ నడుస్తున్నప్పుడు వచ్చే తప్పు |
