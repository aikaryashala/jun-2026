# Build a Mini HTML Parser — From HTML to DOM

*An AI-Assisted Software Development assignment — OpenCode + Entire + GitHub*

**Goal.** Build the **same command-line program twice** — once in **C**, once in **Python**. The program reads an HTML file, builds a **DOM tree** in memory, and prints that tree the way the Linux `tree` command prints folders.

The parser is the *what*. The real lesson is the *how*: you will build it with an **AI coding agent** (OpenCode), keep the **story of your work** with Entire, and keep your code on **GitHub**. By the end you should be comfortable using these three tools **together**.

**You need:** your Ubuntu machine (WSL), `clang`, Python with `uv`, Git, a GitHub account, **OpenCode**, **Entire**, and a notebook.

> **The golden rule.** The AI can write the code. **You** must own the design, the tests, and the reasons.
> Do not complete everything in one step. Work incrementally and keep the development process visible.

---

## Important Rules

1. **Do not use an existing HTML or DOM parser library.** (No `html.parser`, `BeautifulSoup`, `lxml`, `libxml2`, …)
2. **Do not copy an implementation from the Internet.**
3. **AI-generated code must be understood and verified** before you commit it.
4. **You must be able to explain your own implementation** — any line, any time.
5. **OpenCode is a development assistant, not a substitute for understanding.**
6. **Git commits must reflect actual development** — small steps, honest messages.
7. **Entire must be used throughout development**, from the first commit — not added at the end.
8. **The C and Python implementations must be developed independently.** Do not translate one into the other.

---

## What you will learn

- How an HTML parser works at a basic level, and how a **DOM** is a **tree**
- Tree data structures, **recursion**, and **traversal**
- Strings and parsing, file handling, command-line arguments, error handling
- In C: `struct`, pointers, dynamic memory (`malloc` / `free`)
- In Python: classes, lists, dictionaries, exceptions, modules
- Modular design, testing, debugging, and Git-based development
- Working with an AI coding agent (**OpenCode**) — and checking its work
- Keeping engineering context (**Entire**) — the *why*, not only the *what*
- The difference between **generating code** and **engineering software**

---

## 1. The problem

Given an HTML file such as `page.html`:

```html
<html>
  <body>
    <h1>Hello</h1>
    <p class="intro" id="first">Welcome to AI Karyashala</p>
    <p class="note">Learn by doing</p>
  </body>
</html>
```

your program must:

1. **Read** the HTML file.
2. **Parse** the HTML.
3. **Build** a DOM tree in memory.
4. **Traverse** the DOM tree.
5. **Display** the tree like the Linux `tree` command:

```text
.
└── html
    └── body
        ├── h1
        │   └── "Hello"
        ├── p [class="intro", id="first"]
        │   └── "Welcome to AI Karyashala"
        └── p [class="note"]
            └── "Learn by doing"
```

**The tree must be printed by walking the DOM in memory** — never by re-formatting the original HTML text. If your program would still print the right picture with no tree in memory at all, it does not meet the requirement.

---

## 2. The HTML you must support

You are **not** writing a browser. You support a small, clear subset.

**In scope**

| Feature | Example |
|---|---|
| Opening tags | `<div>` |
| Closing tags | `</div>` |
| Text | `Hello` |
| Nested elements | `<div><p>Hi</p></div>` |
| Sibling elements | `<li>One</li><li>Two</li>` |
| Attributes (double-quoted values) | `<p class="intro" id="first">` |
| Whitespace between elements | spaces, tabs, newlines used for indenting |

Tag and attribute names are lowercase letters and digits (attribute names may also contain `-`).

**Out of scope** — you do not need to handle these:

- JavaScript, `<script>` and `<style>` contents, CSS
- HTML entities such as `&amp;` or `&lt;`
- Rendering, layout, or anything a browser *draws*
- Full HTML5 error recovery (browsers fix broken HTML silently — you will **report** it instead)
- Self-closing and void tags (`<br>`, `<img />`) — *optional extension*
- Comments `<!-- … -->` — *optional extension*
- `<!DOCTYPE html>` — *optional extension*

---

## 3. The DOM you must design

Your DOM needs **two kinds of node**:

| Node | Must hold |
|---|---|
| **Element node** | tag name · attributes (name and value, in source order) · children (in order) |
| **Text node** | the text content |

**How** you represent them is your design decision — and part of your marks.

- In **C** you might use `struct`s with pointers, linked lists of children, or growable arrays.
- In **Python** you might use classes, lists, dictionaries — or something else you can justify.

Write down **why** you chose your design in your development notes. Discuss alternatives with OpenCode *before* you write the code.

---

## 4. Exact output rules

These rules make your output testable, character for character.

1. The first line is always `.` (the document itself).
2. Every node is printed on its own line after a branch: `├── ` for a child that has more siblings after it, `└── ` for the last child.
3. Under a `├── ` child, deeper lines start with `│   `. Under a `└── ` child, they start with four spaces.
4. An element prints its **tag name**. If it has attributes, a space and then `[name="value", name="value"]` in **source order**.
5. A text node prints its text **inside double quotes**: `"Hello"`.
6. Text is **trimmed** at both ends. Text that is **only whitespace** (the indenting between tags) is **ignored** — it never becomes a node.
7. An element with no children (`<p></p>`) is printed with no lines under it.
8. A file may have **several top-level elements**; they are all children of `.`.

---

## 5. The command-line interface

The command is called `domparser` here; you may choose your own name, but document it in your README.

| Command | What it does |
|---|---|
| `domparser FILE` | Print the DOM tree (same as `--tree`) |
| `domparser FILE --tree` | Print the DOM tree |
| `domparser FILE --text` | Print every text node's text, one per line, in document order, **without** quotes |
| `domparser FILE --find-tag TAG` | Print every element with that tag name |
| `domparser FILE --find-id ID` | Print the element whose `id` attribute equals ID |
| `domparser FILE --find-class CLASS` | Print every element whose `class` attribute contains CLASS (a `class` value may hold several names separated by spaces) |

**Query output.** A query prints `.` and then each matching element — **with its whole subtree** — as a child of `.`, in document order, using the same tree printer. If nothing matches, it prints only `.`. If one match sits inside another, both are printed.

**Errors.** Any error is printed on **stderr** as one line, and the program **exits with status 1**. Stop at the **first** error.

| Situation | Message |
|---|---|
| File cannot be opened | `Cannot open file: FILE` |
| Wrong arguments | `Usage: domparser FILE [--tree \| --text \| --find-tag TAG \| --find-id ID \| --find-class CLASS]` |
| Closing tag when nothing is open | `Unexpected closing tag: </section>` |
| Closing tag that is not the innermost open one | `Unexpected closing tag: </b> (expected </i>)` |
| End of file with an element still open | `Unclosed tag: <div>` (the innermost one still open) |

---

## 6. Two repositories

Create **two separate GitHub repositories**:

```text
dom-parser-c/                 dom-parser-python/
├── src/                      ├── src/
├── tests/                    ├── tests/
├── samples/                  ├── samples/
├── expected/                 ├── expected/
├── development-notes/        ├── development-notes/
└── README.md                 └── README.md
```

You may adjust the inside of each repository — explain your layout in its README. Both programs must accept the **same** sample files and produce the **same** output.

Build them **independently**. Python is not "C with fewer semicolons" and C is not "Python with pointers": make the decisions each language is good at, and write down where they differed.

---

## 7. How to work: the loop

Do **not** type *"Build the complete HTML parser for me."* An agent that writes everything at once leaves you with code you cannot explain, tests you never saw fail, and a history that tells no story.

Go round this loop **once per phase**:

```text
Problem → Understand → Plan → Design → Implement → Run → Test → Debug → Refactor → Commit
```

Use OpenCode at every step, not only at *Implement*:

| Step | Ask OpenCode to help you… |
|---|---|
| Understand | restate the requirement in its own words; list edge cases you missed |
| Plan / Design | compare two or three designs and their trade-offs — **you** choose |
| Implement | write **one** small feature you have already designed |
| Test | propose test cases — then check each expected output **yourself** |
| Debug | explain an error message or a wrong output before changing code |
| Refactor | suggest a cleaner structure for code that already works and is tested |
| Review | read your code and point out bugs, leaks, or unclear names |

**A weak prompt and a stronger one:**

```text
Weak:    make the parser work
Strong:  In src/tokenizer.c, the function that reads a tag stops at the first space,
         so attributes are lost. Here is the input and the wrong output.
         Explain why before suggesting a change.
```

**Keep a prompt log.** In `development-notes/`, record the important prompts, what the agent proposed, what you **accepted**, what you **rejected**, and **why**. This log is part of your marks.

---

## 8. Entire: keeping the story

Git keeps **what** changed. When an AI agent writes part of the code, much of the **why** lives in the conversation: the prompt, the alternatives, the wrong first attempt, the reason for the final choice. Entire captures that context and links it to your commits.

- Turn Entire on in **both** repositories **before** your first real commit, connected to OpenCode.
- Keep it on for the whole assignment.
- Look at what it recorded after each phase — do not wait until the end.

You are **not** told exactly what Entire records. Find out by using it, and write what you found in your notes.

**The comparison exercise.** Pick **three** important commits. For each one:

1. Look at it with **Git alone** (the commit message and the diff).
2. Look at the **same commit through Entire**.
3. Write in your notes: what could you learn from Entire that Git alone could not tell you?

---

## 9. Phase 0 — Set up your tools

There are **no setup commands in this assignment.** Setting up your tools from their documentation is part of the exercise — it is what engineers do with every new tool.

| Tool | Documentation |
|---|---|
| OpenCode | [opencode.ai/docs](https://opencode.ai/docs) |
| Entire | [docs.entire.io](https://docs.entire.io) |
| GitHub | [docs.github.com](https://docs.github.com) |

**Done when:**

- [ ] OpenCode runs in your project folder and is connected to a model.
- [ ] OpenCode has generated an `AGENTS.md` for the project, and you have read it and corrected anything wrong.
- [ ] Entire is enabled in the repository, set up for OpenCode.
- [ ] The repository is on GitHub.
- [ ] Your first commit exists, and you can find what Entire recorded for it.

Write in your notes: which documentation pages you used, and anything that went wrong while setting up.

---

## 10. The phases

Build each repository through these phases **in order**. Finish one phase — tested and committed — before starting the next. Each phase should end with **at least one** meaningful commit.

### Phase 1 — Read the HTML

- Read the file named on the command line; print its contents unchanged.
- Report `Cannot open file: FILE` and the usage message as described.

**Done when:** you can run your program on every sample, and on a missing file.

> **Say it out loud:** *Before I parse anything, I can read the input and fail clearly.*

### Phase 2 — Break the HTML into pieces (tokenizing)

- Split the input into a sequence of **opening tags**, **closing tags**, and **text**.
- For now, simply print each piece and its kind, one per line, to check yourself.

**Done when:** for `nested.html` you can account for every piece, including the whitespace-only text you will later ignore.

> **Say it out loud:** *A parser first sees a stream of pieces, not a tree.*

### Phase 3 — Build the DOM

- Design your element and text nodes (Section 3). Discuss alternatives with OpenCode in **Plan** mode first.
- Turn the stream of pieces into parent–child links. Nested elements must work.
- In C, decide **who frees** each node — and free them.

**Done when:** you can explain, using a drawing in your notebook, how `nested.html` becomes a tree — and your code builds exactly that.

> **Say it out loud:** *An opening tag goes down one level; the matching closing tag comes back up.*

### Phase 4 — Print the tree

- Write a **recursive** function that walks the DOM and prints it by the rules in Section 4.
- The `│`, `├──`, `└──` decisions come from the tree's shape — whether a child is the last one — not from the HTML text.

**Done when:** `simple.html`, `nested.html`, `siblings.html`, `empty.html`, and `deep.html` all match their expected output exactly.

> **Say it out loud:** *The picture is drawn from the tree, so a wrong picture means a wrong tree or a wrong walk.*

### Phase 5 — Attributes

- Read attributes from opening tags and store them on the element, in order.
- Print them as `[name="value", …]`.

**Done when:** `attributes.html` and `page.html` match.

> **Say it out loud:** *Attributes belong to the element node, not to the text inside it.*

### Phase 6 — Validation

- Detect the three malformed cases in Section 5 and print the exact messages.

**Done when:** `unclosed.html`, `unexpected_close.html`, and `wrong_nesting.html` each print their one error line on stderr and exit with status 1.

> **Say it out loud:** *The innermost open element is the only one that may close next.*

### Phase 7 — Queries

- Add `--text`, `--find-tag`, `--find-id`, `--find-class`. Reuse your tree printer for the results.

**Done when:** every query example in Section 11 matches.

> **Say it out loud:** *A query is a walk that keeps some nodes.*

### Phase 8 — Testing and refactoring

- Automate everything you checked by hand (Section 12).
- Only now, with tests passing, refactor — and run the tests after every change.

**Done when:** one command runs all tests and reports pass or fail.

> **Say it out loud:** *I refactor only on green tests.*

---

## 11. Sample files and expected output

Put these files in `samples/` in **both** repositories, and the expected output of each in `expected/`. Type them exactly — trailing spaces matter.

### simple.html

```html
<html>
  <body>
    <h1>Hello</h1>
  </body>
</html>
```

```text
.
└── html
    └── body
        └── h1
            └── "Hello"
```

### nested.html

```html
<div>
  <section>
    <h1>Title</h1>
    <p>Hello</p>
  </section>
</div>
```

```text
.
└── div
    └── section
        ├── h1
        │   └── "Title"
        └── p
            └── "Hello"
```

### attributes.html

```html
<div id="main" class="container">
  <p class="intro">Hello</p>
  <p>World</p>
</div>
```

```text
.
└── div [id="main", class="container"]
    ├── p [class="intro"]
    │   └── "Hello"
    └── p
        └── "World"
```

### siblings.html

```html
<ul>
  <li>One</li>
  <li>Two</li>
  <li>Three</li>
</ul>
```

```text
.
└── ul
    ├── li
    │   └── "One"
    ├── li
    │   └── "Two"
    └── li
        └── "Three"
```

### empty.html

```html
<div>
  <p></p>
  <span>Hi</span>
</div>
```

```text
.
└── div
    ├── p
    └── span
        └── "Hi"
```

### deep.html

```html
<div>
  <div>
    <div>
      <p>Deep</p>
    </div>
  </div>
  <p>Shallow</p>
</div>
```

```text
.
└── div
    ├── div
    │   └── div
    │       └── p
    │           └── "Deep"
    └── p
        └── "Shallow"
```

### page.html

The file from Section 1. Expected output: the tree shown in Section 1.

### Malformed files

| File | Contents | Expected (stderr, exit status 1) |
|---|---|---|
| `unclosed.html` | `<div>` then `  <p>Hello</p>` — and nothing else | `Unclosed tag: <div>` |
| `unexpected_close.html` | `<div>`, `  <p>Hello</p>`, `</div>`, `</section>` | `Unexpected closing tag: </section>` |
| `wrong_nesting.html` | `<p>`, `  <b><i>Bold italic</b></i>`, `</p>` | `Unexpected closing tag: </b> (expected </i>)` |

Each item in *Contents* is one line of the file.

### Query examples

`domparser page.html --text`

```text
Hello
Welcome to AI Karyashala
Learn by doing
```

`domparser page.html --find-tag p`

```text
.
├── p [class="intro", id="first"]
│   └── "Welcome to AI Karyashala"
└── p [class="note"]
    └── "Learn by doing"
```

`domparser page.html --find-id first`

```text
.
└── p [class="intro", id="first"]
    └── "Welcome to AI Karyashala"
```

`domparser attributes.html --find-class intro`

```text
.
└── p [class="intro"]
    └── "Hello"
```

`domparser attributes.html --find-tag h1`

```text
.
```

`domparser deep.html --find-tag div` — a match inside a match is printed again:

```text
.
├── div
│   ├── div
│   │   └── div
│   │       └── p
│   │           └── "Deep"
│   └── p
│       └── "Shallow"
├── div
│   └── div
│       └── p
│           └── "Deep"
└── div
    └── p
        └── "Deep"
```

---

## 12. Testing

**Automated tests** — required in both repositories:

- **Golden-file tests:** a script that runs your program on every file in `samples/` and compares the result with `expected/` using `diff`. Any difference is a failure.
- **Unit tests** for the parts on their own: tokenizing, building the DOM, printing, attributes, text nodes, error detection, queries. In Python use a test framework; in C, write a small test program of your own.
- Include at least: simple elements, nesting, several siblings, text nodes, attributes, empty elements, each malformed case, deep nesting.

**Manual CLI testing** — also required. Try things the samples do not cover (an empty file, a file with only text, a missing argument, an unknown option) and record in your notes what happened and whether it was right.

When OpenCode writes a test, **check its expected output by hand**. A test that expects the wrong answer is worse than no test.

---

## 13. The C version and the Python version

| | C | Python |
|---|---|---|
| Practise | `struct`, pointers, `malloc`/`free`, strings as `char` arrays, arrays or linked lists, recursion, file I/O, `argc`/`argv`, error handling | classes and objects, lists, dictionaries, strings, file handling, exceptions, recursion, command-line arguments, modules |
| Must also | compile with no warnings; free every node you allocate | carry type hints everywhere (Task 32); pass `ruff` and `mypy` (Task 34) |

No implementation is given here. Designing the data structures is your job.

---

## 14. Git

- **At least 5 meaningful commits in each repository** — one or more per phase is normal.
- A commit message says **what changed**, in a few words, in the imperative:

```text
Good:  Create DOM node representation      Bad:  update
       Add recursive tree printer                 changes
       Add attribute parsing                      final
       Detect unexpected closing tags             done
```

- Commit when a phase works and its tests pass — not once at the end.
- You must be able to explain what changed in each important commit, and why.

---

## 15. Final README

Each repository's `README.md` documents:

1. **What you built** — and how to build, run, and test it.
2. **How OpenCode was used** — in which phases, for what kinds of tasks.
3. **How Entire was used** — what it recorded, and when you looked at it.
4. **Important prompts and decisions** made during development.
5. **Bugs you encountered** and how you found and fixed them.
6. **What was different** between the C and Python implementations.
7. **What information would be lost** if we kept only the final source code and the Git commits.

---

## 16. Final comparison

In `development-notes/` of either repository, answer:

1. Which implementation was easier to develop? Why?
2. How did memory management affect the C implementation?
3. What did Python make easier?
4. What was difficult for the AI agent?
5. Where did the AI generate incorrect or poor code?
6. How did you verify AI-generated code?
7. Which design decisions did **you** make yourself?
8. How did OpenCode change your development workflow?
9. What did Entire preserve that Git alone did not?
10. What would another developer need to understand this project six months from now?
11. Is source code alone a complete record of software development when AI agents are involved?

---

## 17. Deliverables

Submit the GitHub links to **both** repositories. Each must contain:

- [ ] the implementation
- [ ] `samples/` and `expected/`
- [ ] automated tests, runnable with one command
- [ ] the Final README (Section 15)
- [ ] a Git history of at least 5 meaningful commits
- [ ] OpenCode evidence — your prompt log in `development-notes/`
- [ ] Entire development context, captured from the first commit onward
- [ ] the comparison (Section 16) and reflection (Section 19), in at least one of the two

---

## 18. Evaluation (100 marks)

Engineering quality carries most of the marks; the process is marked too, because it is half of what this assignment teaches.

| Area | Marks | What earns them |
|---|---|---|
| DOM design | 10 | a clear node design you can justify; alternatives considered |
| HTML parsing | 10 | correct tokenizing of tags, text, and whitespace |
| Tree construction | 10 | correct parent–child links for nesting and siblings |
| Tree-style output | 8 | exact match with every expected file; printed from the DOM |
| Attributes and queries | 7 | attributes stored and printed in order; all four queries correct |
| Error handling | 7 | exact messages on stderr, exit status 1, missing files and bad arguments handled |
| Testing | 10 | golden-file and unit tests, run with one command; expected outputs checked by hand |
| C implementation quality | 4 | no warnings, no leaks, readable functions |
| Python implementation quality | 4 | type hints, `ruff` and `mypy` clean, idiomatic structure |
| Git history | 8 | at least 5 meaningful commits per repository that follow the phases |
| OpenCode usage | 8 | a prompt log showing staged, scoped prompts and your accept/reject decisions |
| Entire usage | 8 | context captured throughout; the three-commit comparison done |
| Final README | 6 | all seven sections, specific and honest |
| **Total** | **100** | |

---

## 19. What Does Software Engineering Look Like When AI Writes Code?

Write a short reflection (one page) on this question:

> If an AI coding agent can generate a large portion of the source code, what becomes more important for a software engineer?

Use your own experience from this assignment. Think about:

- **Problem understanding** — did you understand the requirement before the agent wrote code?
- **System design** — which design choices were yours?
- **Decomposition** — how did splitting the work into phases change what the agent produced?
- **Asking good questions** — which prompt worked best, and why?
- **Verification and testing** — how did you know the code was right?
- **Debugging** — what did you find that the agent missed?
- **Reviewing AI output** — what did you reject, and why?
- **Making engineering decisions** — where did you overrule the agent?
- **Preserving development context** — what would a stranger need, beyond your code, to continue your work?

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Simple meaning |
|---|---|---|
| parser | పార్సర్ (విశ్లేషకం) | వచనాన్ని చదివి దాని నిర్మాణాన్ని అర్థం చేసుకునే ప్రోగ్రామ్ |
| token | టోకెన్ (ముక్క) | ఇన్‌పుట్‌లోని ఒక చిన్న భాగం — ఒక ట్యాగ్ లేదా కొంత వచనం |
| DOM | డామ్ (డాక్యుమెంట్ వృక్షం) | HTML పేజీని మెమరీలో చెట్టు రూపంలో ఉంచడం |
| node | నోడ్ (కణుపు) | చెట్టులో ఒక స్థానం — ఎలిమెంట్ లేదా వచనం |
| tree | వృక్షం | ఒక మూలం నుంచి పిల్లలుగా విస్తరించే నిర్మాణం |
| traversal | సంచారం | చెట్టులోని ప్రతి నోడ్‌ను ఒక క్రమంలో సందర్శించడం |
| recursion | పునరావృతం | ఒక ఫంక్షన్ తనను తానే పిలుచుకోవడం |
| attribute | లక్షణం | ట్యాగ్‌లో రాసే పేరు = విలువ జత, ఉదా. `class="intro"` |
| malformed | తప్పుగా ఏర్పడిన | నియమాల ప్రకారం సరిగా మూయని లేదా అమర్చని HTML |
| query | ప్రశ్న (శోధన) | చెట్టులో కొన్ని నోడ్‌లను మాత్రమే వెతికి తీసుకోవడం |
| golden file | ప్రామాణిక ఫలిత ఫైల్ | సరైన అవుట్‌పుట్ ఏమిటో ముందే రాసి ఉంచిన ఫైల్ |
| refactor | పునర్నిర్మించు | పని మారకుండా కోడ్ నిర్మాణాన్ని మెరుగుపరచడం |
| AI coding agent | AI కోడింగ్ సహాయకుడు | మన సూచనలతో కోడ్ రాసే, మార్చే, నడిపే AI ప్రోగ్రామ్ |
| prompt | సూచన | AI కి మనం ఇచ్చే అభ్యర్థన లేదా ఆదేశం |
| checkpoint | తనిఖీ కేంద్రం | ఒక మార్పుతో పాటు దాని వెనుక ఉన్న సంభాషణను కలిపి దాచిన రికార్డు |
| context | సందర్భం | కోడ్ ఎందుకు ఇలా ఉందో చెప్పే నేపథ్య సమాచారం |
| commit | కమిట్ | Git లో దాచిన ఒక మార్పుల సమూహం, ఒక సందేశంతో |
