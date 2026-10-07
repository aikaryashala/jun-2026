# Project: C & Linux Worksheets (june1st2026)

A static educational site (GitHub Pages style — everything servable lives under `docs/`)
of programming worksheets with question banks, for beginner students who are Telugu
speakers learning in English. Most tasks are C and Linux; later tasks branch into Python.
Tasks are hands-on: students run commands, compile programs, use LLDB, and keep notes
(state tables, memory ladders) in their notebooks.

**Scope rule:** this project is only `june1st2026`. The related paper-first project lives
in `aikaryashala/foundations` and is handled in its own sessions — never edit it from here.

## Directory layout

```
docs/
├── index.html            ← batch landing page (hero, learning path, hub cards)
├── tasks.html            ← the worksheet index; one row per task, grouped by topic
├── assets/
│   ├── site.css          ← shared stylesheet for the HUB pages (index, tasks, slides)
│   ├── viewer.css        ← shared stylesheet for ALL viewers
│   └── viewer.js         ← shared fetch-and-render logic for ALL viewers
├── taskN/
│   ├── <base>.md         ← worksheet (some tasks name it <base>_worksheet.md)
│   ├── <base>.html       ← worksheet viewer
│   ├── <base>_questions.md / .html
│   └── <base>_answers.md / .html
└── june_overview_slides/  ← presentations (own style, not viewers)
```

Current tasks: 3 (paths), 4 (compilation intermediate files), 5 (Linux commands),
6 (LLDB level-1), 7 (pipes & redirections), 8 (C functions), 9 (call stack with LLDB),
10 (bitwise operators), 11 (adding to an address), 12 (CS50 tools setup),
13 (check, style & submit), 14 (address variables), 15 (bytes & encodings),
16 (reading & writing files), 21 (Git internals), 22 (pull requests),
30 (Python `input()` and types), 31 (Python collections & `for … in`),
32 (why we follow type hints), 33 (slicing & ranges),
35 (bits-to-internet reference), 36 (signed bytes, two's complement & gates),
37 (networking & routing), 38 (Bottle algo web server, reference),
39 (APIs & HTTP), 40 (API authentication), 41 (REST API design), 42 (LLM limits),
43 (JavaScript DOM — appending vs replacing `<body>`),
44 (JavaScript colours — hex `#RRGGBB` to 24-bit RGB),
45 (Think OS ch. 1, compilation), 46 (Think OS ch. 2, processes) — each a redirect
page only, to `aikaryashala.com/pusthakam/thinkos/chapNN/...`; listed as `reference`. Tasks 39–41 form an API series
built from Zapier's "An Introduction to APIs", all extending task 38's Bottle server with
curl as the client.

Task **43** is run with no server: students open the HTML files directly (`file:///`)
in the **Ulaa** browser (Chromium-based) and paste snippets into the DevTools Console.
Its scope is deliberately just the four ways in its opening guide (`createElement` +
`appendChild` with `textContent` / `innerHTML`, `innerHTML =`, `innerHTML +=`), plus
the browser's auto-built html/head/body skeleton (empty file, content-only file).
Task **44** runs the same way (Ulaa, Console, content-only files) and adds iterations
beyond its guide to block colour misconceptions: additive light mixing, brightness/grey,
short hex forms (3 digits doubled, 4th = transparency, 2/5 ignored), `toString(16)`,
`padStart`, and why a `load` listener typed into the Console never fires.

Planned next in the Python strand: **strings** (methods and formatting) and **JSON**
(general use across systems, then how Python handles it) — numbers to be decided when
they are set. Task 31's last iteration deliberately ends on a list-of-dictionaries —
the JSON shape — as the bridge into the JSON task.

Numbering follows the order tasks were set for students, not creation order (21 was
written after 30) — the next task is **not** necessarily "highest + 1"; ask which
number to use.

Not every task is the full six-file shape: **8** and **13** are worksheet-only, **12** is
a screenshot-based reference page (no markdown), and **10** and **31** each carry an
extra `_extra_questions` / `_extra_answers` bank alongside the normal one. An extra bank
**continues the main bank's part letters** rather than restarting at A (task 10's main
bank ends at C so its extra bank is Part D; task 31's ends at D so its extra bank runs
E–J).

Task **31**'s extra bank is context-driven rather than syntax-driven: it applies the
containers to real backend and AI-native scenarios (database rows, orders, permissions,
chat conversations, tool definitions, token usage, eval results, RAG chunks), organised
around seven recurring **shapes** — list of dicts, dict, dict of lists, dict of ints,
dict of dicts, set, tuple. Students already know `if`/`elif`/`else` by this point, so
counting, grouping and filtering are fair game there even though the worksheet itself
uses none.

**22** is a different *kind* of page: reading material, not a worksheet. The students had
already done pull requests by hand, so it explains the mechanism instead of driving
practice. Its main file is `<base>_reference.md` (not `_worksheet.md`), it has sections
rather than a/b/c iterations — each closing with a **"Say it out loud"** line instead of
"Takeaway" — and it carries no practice/self-check section, because the question bank
does that job. It still uses `<body class="worksheet">` and still ends with New Words.
Commands in it are illustrations shown with their real output, never "now run this".

**32** is also reading material rather than a worksheet — a short statement of the
project's Python coding standard. Its main file is plain `<base>.md` (no `_worksheet`
suffix), it has `##` sections rather than iterations, and it carries no New Words table.
Its question bank is deliberately unlike the others: **no MCQs at all** — twelve code
samples, each to be rewritten with the type hints added, in two parts (A collections,
B function signatures). From **32** onward, sample code across the site carries type
hints (`marks: list[int] = [...]`).

**33** returns to the full worksheet shape. Note its topic order, which the user set:
list slicing is the main body (iterations 1–8), then the *same* notation on strings
(9–11), then `range` as a third parallel (12–14). Task 31 had taught only the
one-argument `range(n)`, so 33 teaches the two- and three-argument forms but treats the
half-open rule as already known, referring back rather than re-teaching it. The question
bank runs A–E, with **D string slicing** and **E range** at the end.

## The shared viewer assets (refactored 2026-07-21)

Every viewer HTML is a slim ~40-line page; all styling and logic live in `docs/assets/`.

- **`viewer.css`** — the whole look. The page kind comes from the body class:
  - `<body class="worksheet">` → `hr` divider mark `›`, plus worksheet-only table rules
    (first column nowrap, last column wraps for Telugu)
  - `<body class="questions">` → divider mark `?`
  - `<body class="answers">` → divider mark `✓`
- **`viewer.js`** — fetches the page's markdown and renders it with marked.js +
  highlight.js (only fences with a known language tag get highlighted; ASCII diagrams
  use plain fences). **No per-page config:** the markdown name is derived from the page
  URL (`<name>.html` → `<name>.md`), so a viewer's `.html` basename MUST exactly equal
  its `.md` basename. Error wording comes from the body class. The `file://` fallback
  tells students to serve the `docs` folder (serving a task folder alone would 404
  `../assets/`).

A viewer page contains only: `<title>`, font + highlight-theme links, the
`../assets/viewer.css` link, the topbar (`.brand` text + stage chips), the status line
("Rendering worksheet…/questions…/answers…"), the marked/hljs CDN scripts, and
`../assets/viewer.js`.

**Creating a new viewer:** copy any existing slim viewer and change only: `<title>`,
body class, brand text, chips, status-line text. Nothing else.

**Standalone pages (leave alone):** `task3/relative-vs-absolute-paths.html` and
`task3/relative-vs-absolute-paths-key.html` are an older design with their own inline
CSS/JS. `task35/bits-to-internet.html` is likewise self-contained — a single 200 KB
narrative page ("From a Bit to the Internet", 18 sections) that is the **reading
companion to tasks 36 and 37**, covering the same ground as one continuous story;
both worksheets link to it and it links to nothing. None of these are on the shared
assets — do not convert them. Task3's question-bank answer key
is named `-questions-answers.*` because a worksheet-level `-answers.md` already existed.

## Content formats

- **Worksheet:** `# Title`, goal + what's needed, a "golden rule/picture" blockquote,
  iterations separated by `---`, each with `**a. What we set up**`, `**b. Task**`,
  `**c. Observation (what you should find)**`, and `**Takeaway to say out loud:**`;
  then practice with self-check, a one-page reference table, and always last:
  `## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)` (English | తెలుగు | simple meaning).
- **Question bank** (`_questions.md`): Part A Multiple Choice (~12–14, options A–D),
  Part B Fill in the Blanks (~10), Part C Scenario Questions (~8). Questions only —
  never any answers in this file. Question titles may name a scenario but must never
  reveal what the student should observe.
- **Answer key** (`_answers.md`): mirrors the numbering; every answer explains the
  *why* (line-by-line traces where applicable); tell students to check the reasoning,
  not just the letter.
- Design constraints (e.g. "int only" in task8) are author-side: they shape the
  examples silently and are never announced to students.
- Verify every claim by hand before shipping: arithmetic, source line numbers referred
  to by debugger output, expected program output. Students run on a Linux VM
  (WSL on Windows) — use Linux-style paths/addresses in sample transcripts, not macOS.

## The hub pages (index.html, tasks.html, slides)

Three hand-written pages — not viewers — sharing `assets/site.css`, which reuses the
same tokens as `viewer.css` so the front door looks like the worksheets:

- **`index.html`** — the batch landing page served at the site root. Hero (batch label,
  student/team/worksheet counts; the "Now on" banner was removed 2026-09-29), a
  **learning path** of topic chips linking to `tasks.html`
  anchors, and three hub cards. The `BRANDING` lines (marked with a comment) and the
  worksheet count in `.facts` go stale — keep the count equal to the `<li>`s in
  `tasks.html`.
- **`tasks.html`** — the worksheet index (this was the old `index.html`; renamed
  2026-08-06 so the root could become the batch page). Since 2026-10-06 it is a
  **single list in task-number order** (not grouped); each task shows its topic as a
  `.cat` subtitle under the title. There are eleven topics, each with a stable anchor
  id that the `index.html` path chips link to: `#linux`, `#c`, `#thinkos`, `#memory`,
  `#debug`, `#tools`, `#network`, `#python`, `#web`, `#frontend`, `#ai`. The id sits on
  the `<li>` of the **lowest-numbered** task in that topic, so a chip jumps to where
  the topic starts. The `.facts` "Topics" count in `index.html` must match the number
  of topics.
- **`june_overview_slides/index.html`** — the team decks.

One `<li>` per task, inserted at its place in number order. The task number lives in
its own `.num` span, so the link text is the title alone (no "Task N -" prefix); the
title and its topic subtitle are wrapped together in `.entry`:

```html
<li><span class="num">Task N</span>
    <span class="entry"><a class="title" href="taskN/<base>.html"><Title></a>
      <span class="cat"><Topic name></span></span>
    <a class="q" href="taskN/<base>_questions.html">Questions</a>
    <a class="q" href="taskN/<base>_answers.html">ans</a></li>
```

If the new task is now the lowest-numbered one in its topic, move that topic's `id`
onto it. A brand-new topic needs a new `id`, a new path chip in `index.html`, and a
bump to the Topics count.

For a worksheet-only or reference task, replace the two `.q` links with
`<span class="only">worksheet only</span>` (or `reference`).

## Serving locally

Viewers `fetch()` their sibling `.md`, so `file://` won't work. Always serve the
`docs` root:

```
cd docs
python3 -m http.server 8000
```

## Checklist — adding task N

1. `mkdir docs/taskN`; pick the snake_case `<base>`.
2. Write `<base>.md` (worksheet), `<base>_questions.md`, `<base>_answers.md` —
   re-verify all facts/arithmetic by hand.
3. Copy one slim viewer HTML per file; update title, body class, brand, chips,
   status text. Keep `.html` and `.md` basenames identical.
4. Add the task's `<li>` in number order in `docs/tasks.html` with its topic subtitle, and update the
   group's task-number list in the `docs/index.html` path chip (e.g. `tasks 3, 5, 7`).
   Bump the worksheet count in `.facts`.
5. Serve `docs/` and click all three links; confirm each renders and the browser-tab
   title matches the md's `# Title`.

For the guided version of this — propose an outline, review with the user, then
create everything on confirmation — use the **`create-task`** skill
(`.claude/skills/create-task/SKILL.md`), triggered by "create task on <topic>".
