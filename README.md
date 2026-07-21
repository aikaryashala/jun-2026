# C & Linux Worksheets

A static educational site of hands-on C-programming and Linux worksheets with question
banks, for beginner students (Telugu speakers learning in English). Students compile
with `clang`, explore the shell, and inspect running programs with `lldb` on a Linux VM
(WSL), keeping notes — state tables and memory-ladder diagrams — in their notebooks.

Everything servable lives under `docs/` (GitHub Pages style).

## Tasks

| Task | Topic |
|---|---|
| 3 | Relative vs absolute paths |
| 4 | Compilation intermediate files (preprocessor → compiler → linker) |
| 5 | Exploring Linux commands — the shell toolbox |
| 6 | Debugging C with LLDB — level 1 |
| 7 | Pipes and redirections — streams in the shell |
| 8 | Functions in C — declare, define, call |
| 9 | The call stack with LLDB — frames, return addresses, leftovers |

Most tasks have three pages: the **worksheet**, a **question bank** (MCQ, fill in the
blanks, scenarios — no answers), and an **answer key** with reasoning. Two slide decks
(`june_overview_slides/`, `env_var_slides/`) accompany the tasks.

## How the site works

Each page is a markdown file rendered in the browser by a slim HTML viewer. Since the
2026-07 refactor, all viewers share two common files:

- `docs/assets/viewer.css` — the whole look; the body class (`worksheet` / `questions` /
  `answers`) switches the section-divider mark (`›` / `?` / `✓`) and small table rules.
- `docs/assets/viewer.js` — fetches the page's markdown (derived from the page's own
  filename: `<name>.html` → `<name>.md`) and renders it with marked.js + highlight.js.

A viewer HTML therefore contains only its title, topbar branding, and the shared
references — about 40 lines. (Two legacy task3 pages keep their own older design.)

## Viewing locally

The viewers fetch their markdown, so opening the HTML as a plain file won't work.
Serve the `docs` folder:

```
cd docs
python3 -m http.server 8000
```

Then open <http://localhost:8000/>.

## Contributing a new task

See [CLAUDE.md](CLAUDE.md) for the full conventions: file naming (a viewer's `.html`
basename must equal its `.md` basename), worksheet/question-bank formats, and the
add-a-task checklist.
