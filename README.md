# C & Linux Worksheets

A static educational site of hands-on programming worksheets with question banks, for
beginner students (Telugu speakers learning in English). Most tasks are C and Linux:
students compile with `clang`, explore the shell, and inspect running programs with
`lldb` on a Linux VM (WSL), keeping notes — state tables and memory-ladder diagrams —
in their notebooks. Later tasks branch into Python.

Everything servable lives under `docs/` (GitHub Pages style).

## Tasks

| Task | Topic | Pages |
|---|---|---|
| 3 | Relative vs absolute paths | W · Q · A |
| 4 | Compilation intermediate files (preprocessor → compiler → linker) | W · Q · A |
| 5 | Exploring Linux commands — the shell toolbox | W · Q · A |
| 6 | Debugging C with LLDB — level 1 | W · Q · A |
| 7 | Pipes and redirections — streams in the shell | W · Q · A |
| 8 | Functions in C — declare, define, call | W |
| 9 | The call stack with LLDB — frames, return addresses, leftovers | W · Q · A |
| 10 | Bitwise operators — working the bits | W · Q · A (+ extra Q · A) |
| 11 | Adding to an address — discover the rule | W · Q · A |
| 12 | Setting up and using the CS50 tools | reference (screenshots) |
| 13 | Check, style & submit — every program | W |
| 14 | Address variables — store an address, reach the box | W · Q · A |
| 15 | Bytes become meaning — colours, characters & encodings | W · Q · A |
| 16 | Reading & writing files — raw bytes vs readable text | W · Q · A |
| 30 | Python `input()` and types — strings, numbers & how `print` lays them out | W · Q · A |

**W** = worksheet · **Q** = question bank (MCQ, fill in the blanks, scenarios — no
answers) · **A** = answer key with reasoning. A few tasks are worksheet-only, and task 12
is a screenshot-based reference page rather than the usual three. One slide deck,
`june_overview_slides/`, accompanies the tasks.

Numbering follows the order the tasks were set for students, so there is a gap between
16 and 30.

## TODO — planned tasks

### 1. Git internals

- [ ] **What `git init` creates.** Walk through the `.git/` folder right after `git init`:
      `HEAD`, `config`, `description`, `objects/`, `refs/heads/`, `refs/tags/`, `hooks/`,
      `info/exclude`. What each one is for, and which are empty at this point.
- [ ] **Where configuration lives.** The three levels and their files — system
      (`/etc/gitconfig`), global (`~/.gitconfig` or `~/.config/git/config`, written by
      `git config --global ...`), and local (`.git/config`, per repository). Show
      precedence, and `git config --list --show-origin` to prove which file a setting
      came from.
- [ ] **What the first commit writes.** Before/after listing of `.git/objects/` — how one
      commit produces a **blob** per file, a **tree** per directory, and one **commit**
      object; how the SHA-1 hash becomes the `objects/ab/cdef...` path; and what changes
      in `.git/refs/heads/master`, `HEAD`, `.git/index`, and `.git/logs/`. Inspect with
      `git cat-file -t` / `-p` and `git hash-object`.
- [ ] **Hooks — what they are and when they fire.** `.git/hooks/` and its `.sample`
      files; that a hook is just an executable script; and the points in the flow where
      each one runs: `pre-commit`, `prepare-commit-msg`, `commit-msg`, `post-commit`,
      `pre-rebase`, `pre-push`, and the server-side `pre-receive`, `update`,
      `post-receive`. Which ones can **block** the operation by exiting non-zero.
- [ ] **Real industry uses of hooks.** Concrete examples students will meet: `pre-commit`
      running a formatter/linter or blocking committed secrets and API keys;
      `commit-msg` enforcing a message convention or an issue-ID prefix; `pre-push`
      running the test suite before code leaves the machine; server-side `pre-receive`
      rejecting force-pushes to `main`; `post-receive` triggering a deploy or CI run.
      Mention the `pre-commit` framework and why hooks aren't committed with the repo.

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
