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
├── index.html            ← site root; one line per task
├── assets/
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
16 (reading & writing files), 21 (Git internals), 30 (Python `input()` and types).

Numbering follows the order tasks were set for students, not creation order (21 was
written after 30) — the next task is **not** necessarily "highest + 1"; ask which
number to use.

Not every task is the full six-file shape: **8** and **13** are worksheet-only, **12** is
a screenshot-based reference page (no markdown), and **10** carries an extra
`_extra_questions` / `_extra_answers` bank alongside its normal one.

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

**Legacy exceptions (leave alone):** `task3/relative-vs-absolute-paths.html` and
`task3/relative-vs-absolute-paths-key.html` are an older standalone design with their
own inline CSS/JS; they are not on the shared assets. Task3's question-bank answer key
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

## index.html format

One `<li>` per task — worksheet link, then small `[Questions]` and `[ans]` links
(class `q`) on the same line:

```html
<li><a href="taskN/<base>.html">Task N - <Title></a>
    <a class="q" href="taskN/<base>_questions.html">[Questions]</a>
    <a class="q" href="taskN/<base>_answers.html">[ans]</a></li>
```

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
4. Add the task's `<li>` to `docs/index.html`.
5. Serve `docs/` and click all three links; confirm each renders and the browser-tab
   title matches the md's `# Title`.

For the guided version of this — propose an outline, review with the user, then
create everything on confirmation — use the **`create-task`** skill
(`.claude/skills/create-task/SKILL.md`), triggered by "create task on <topic>".
