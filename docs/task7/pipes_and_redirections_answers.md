# Pipes and Redirections — Answers with Reasoning

Answer key for `pipes_and_redirections_questions.md`. Every answer includes
the reasoning — when checking a student's work, check the **why**, not just
the letter. A correct letter with a wrong reason is a lucky guess.

---

# Part A — Multiple Choice Questions

**Q1. Answer: (b) — the screen (terminal).**
stdout's *default* attachment is the screen, just as stdin's default is the
keyboard. That word "default" is the whole point: `>`, `>>`, and `|` exist
precisely to re-attach these hoses somewhere else without changing the
program. (c) confuses the two hoses — the keyboard is stdin's default, the
reading end, not the writing end.

**Q2. Answer: (b) — `>` overwrites, `>>` appends.**
The worksheet's diary experiment shows it directly: writing `line two` with
`>` made `line one` vanish, while rebuilding with `>>` grew the file line by
line. Both operators aim stdout at a file; the only difference is what
happens to the file's existing content. (d) is dangerous nonsense — mixing
them up is how real data gets destroyed.

**Q3. Answer: (c) — the file is emptied to 0 bytes.**
`>` truncates the target file *first*, before any command output arrives —
that's part of the shell setting up the redirection, not part of running a
command. So even with no command at all, the truncation still happens. This
is the "scary step" of Sub-task 1: `ls -l` showed the size drop to 0. There
is no undo.

**Q4. Answer: (b).**
Same output, different plumbing. `head commands.txt` passes a *file name* as
an argument, and `head` opens that file itself. `head < commands.txt` passes
`head` nothing — the **shell** connects the file to stdin before `head`
starts, and `head` simply reads its stdin like it always does. This
distinction matters later: your `sum` program takes no file arguments at
all, yet `< input.txt` feeds it perfectly.

**Q5. Answer: (b) — 10 lines.**
Both `head` and `tail` default to 10; `-n N` (or the shorthand `-N`) changes
that. (d) describes `more`, which pages through *everything* one screen at a
time — it never cuts a slice.

**Q6. Answer: (a) — stdout of the left command to stdin of the right, no file in between.**
That is the definition of a pipe: a redirection with no file, data flowing
left to right while both programs run. (b) describes what Sub-task 2 did
manually with `commands.txt` — exactly the extra step the pipe removes.

**Q7. Answer: (c) — lines 16–20.**
Work it stage by stage. `head -20` keeps lines 1–20 of the file and writes
them to its stdout. `tail -5` receives *only those 20 lines* as its stdin
and keeps the bottom 5 of them: lines 16–20. The pipeline is a *window*: two
simple slicers combining into a tool neither is alone. (d) is the trap —
`tail` never sees the whole file, only what `head` passed on.

**Q8. Answer: (b) — keyboard echo.**
During an interactive run the screen shows two streams interleaved: the
program's stdout *and* the terminal echoing your keystrokes. The `3` and `4`
were never program output. When stdin comes from a pipe, no keys are
pressed, so there is nothing to echo — and the program's actual output is
unchanged, which is why the prompts (ending with no newline) now butt up
against each other.

**Q9. Answer: (b) — only what a human would have typed.**
The input stream contains only what travels *into* `scanf`: `3` and `4` on
two lines. The prompts (`Enter the first number:`) are `printf` output —
they belong to stdout, the opposite hose. Putting them in the input file is
the classic beginner error (see Q26).

**Q10. Answer: (c) — identical files; the test passed.**
`diff` prints the differences, and when there are none it prints nothing.
Silence = pass. This takes getting used to — many tools celebrate success
loudly, `diff` celebrates with silence. (That's also why Sub-task 7 wraps it
in an `if` that prints `PASSED` in plain words.)

**Q11. Answer: (b) — `0` means success; `echo $?` shows it.**
Every command hands the shell a number when it finishes: `0` = success,
non-zero = something to report. `$?` holds the *last* command's status. Note
the deliberate contrast with C, where `0` means false: in the shell there is
one way to succeed but many ways to fail, so success got the one special
number.

**Q12. Answer: (c) — without the guard, tests would run against a stale executable.**
If `clang` fails, no new `sum` is produced — but the *old* `sum` from the
previous successful compile is still sitting there. Without the guard, the
script would run the tests against that ghost and could even print PASSED
for code that doesn't compile. `if [ $? -ne 0 ] ... exit 1` stops the lie
before it starts. This is the same stale-executable trap as Task-6's Q2,
designed away.

---

# Part B — Fill in the Blanks

**Q13.** reads from **stdin**, writes to **stdout**.
The two hoses attached to every program. Defaults: keyboard and screen —
but only defaults.

**Q14.** `echo "note" **>>** diary.txt`
Append needs `>>`. A single `>` would wipe the diary first — the exact
mistake in scenario Q23.

**Q15.** a **file**.
`< file` attaches the file to the reading hose. The program can't tell the
difference — keyboard and file arrive through the same hose.

**Q16.** `**head -3** commands.txt` (also accepted: `head -n 3`) and
`**tail -5** commands.txt` (also accepted: `tail -n 5`).
`-N` is shorthand for `-n N`; both spellings appear in `man head`.

**Q17.** the **stdout** of `ls` is connected directly to the **stdin** of
`wc`.
Direction matters: left writes, right reads. Data flows left to right
through the pipe with no file in between.

**Q18.** **Ctrl-D**.
End of input (EOF) — the keyboard's way of saying "the stream is finished."
Not Ctrl-C, which kills the program rather than politely ending its input.

**Q19.** `printf` → **stdout**; `scanf` → **stdin**.
The C library opens both streams before `main` starts. The program only ever
talks to the two hoses; the shell decides what's attached to each end.

**Q20.** **input file** (e.g. `input1.txt`), **expected output** (e.g.
`expected_output1.txt`), **actual output** (e.g. `output1.txt`).
Roles: frozen keystrokes; the answer a human verified once and blessed; the
file regenerated on every run. Only the actual-output file ever changes.

**Q21.** **`$?`** and **`0`**.
`$?` always holds the last command's exit status. For `diff`, `0` means
identical files (and `1` means they differ — documented under EXIT STATUS in
`man diff`).

**Q22.** The blanks are **`-eq`**, **`then`**, **`fi`**:

```bash
if [ $? -eq 0 ]
then
    echo "PASSED"
else
    echo "FAILED"
fi
```

`-eq` compares numbers for equality (`-ne` is its opposite); bash's `if`
block opens with `then` and closes with `fi` — `if` spelled backwards.
Remember also the grammar rule that isn't in a blank: the spaces around the
`[` `]` are required.

---

# Part C — Scenario Questions

**Q23. Ravi's diary.**
`>` truncates `notes.txt` to empty **before** writing, so all previous notes
were destroyed the moment he pressed Enter; the file now holds only the new
line. He wanted `>>`, which appends at the end without erasing:
`echo "Day 4: learned pipes" >> notes.txt`. There is **no undo** —
redirection works on the file itself, not on a copy. This is why the
worksheet says to speak the destination out loud before pressing Enter:
"send output to file, *start fresh*" vs "*add at end*."

**Q24. The "hanging" `head`.**
Nothing crashed. With no file name, `head` reads its stdin, and stdin's
default is the keyboard — so `head` is silently *listening*, waiting for her
to type. She can (1) type lines and press Enter — after the 2nd line,
`head -2` prints its slice and exits — or (2) press **Ctrl-D** to end the
input stream. `head -2 commands.txt` never waits because it is given a file
name: the input arrives instantly from the file, no keyboard involved. Rule
of thumb: a stream program that seems stuck is usually just waiting on
stdin.

**Q25. File vs pipe.**
Student A created a real file, `commands.txt`, that persists on disk
(hundreds of lines of it); student B's data flowed straight from `ls`'s
stdout into `head`'s stdin and touched the disk not at all — nothing to
clean up afterwards. A's approach wins when you want to **reuse or keep**
the listing: run many different slices (`head -3`, `tail -5`, `wc -l`)
without re-running `ls`, or keep a snapshot of what was installed today to
`diff` against next month. One-shot question → pipe; data you'll return to →
file.

**Q26. Priya's input file.**
She copied the *interleaved screen*, which mixes two streams. `To add two
numbers.` and both `Enter the ... number:` prompts are the program's
**stdout** (written by `printf`) — they were never typed and never travel
into `scanf`. Even the `3` and `4` she saw on screen were keyboard echo
sitting next to the prompts. The input stream must contain **only the
keystrokes**: `3` on one line, `4` on the next — i.e.
`echo -e "3\n4" > input.txt`. Test: "would a human have *typed* this
character?" If not, it doesn't belong in the input file.

**Q27. Arun's two alarms.**
Both observations are the redirections working perfectly. First: `>`
re-plumbed **stdout** from the screen to `output.txt`, so *everything*
`printf` produces goes to the file — the screen is silent because the hose
no longer points at it, not because the program is broken. Second: prompts
are printed by `printf`, so they are stdout like any other output — stdout
went to the file, prompts included. That's why `output.txt` reads
`To add two numbers.` then
`Enter the first number: Enter the second number: The sum of 3 and 4 is 7.`
(prompts run together, since no keyboard echo separates them). The program
never knew anything changed; only the shell's plumbing did. And this exact
file is what makes automated testing possible — it's the "actual output"
that `diff` compares.

**Q28. Meena's sabotage.**
She should expect the script to recompile the sabotaged code (the script
compiles every time), run both tests, and print diff's complaints — expected
`...is 7.` vs actual `...is -1.` — followed by `TEST 1 FAILED` and
`TEST 2 FAILED`. The sabotage is valuable because it tests the *tests*: a
test suite that has never been seen to fail proves nothing — maybe it isn't
really comparing anything. Watching it catch a planted bug is the moment you
earn trust in the green PASSED lines. If the tests still said PASSED after
the sabotage, the tests themselves are broken — for example, they're
diffing the wrong files, or the sabotaged code never actually got compiled
and an old correct `sum` was run instead.

**Q29. Kiran's missing guard.**
The compile failed, so no new `sum` was produced — but the **old**
executable from the last successful compile is still on disk, and
`./sum < input1.txt` happily ran that ghost. Since the old version was
correct, `diff` stayed silent and the "test" passed while the source code
sitting in `sum.c` doesn't even compile. Dangerous because the pass verdict
is about yesterday's program, not today's code — the exact stale-executable
trap from Task-6. The missing lines are `test_sum.sh`'s guard, placed right
after `clang`:

```bash
if [ $? -ne 0 ]
then
    echo "COMPILATION FAILED — tests not run"
    exit 1
fi
```

`exit 1` abandons the script immediately (itself reporting failure), so no
test lines print at all when the compile breaks.

**Q30. The pipeline puzzle.**
`head -30 commands.txt | tail -1`: `head -30` passes lines 1–30; `tail -1`
keeps the last of those — **line 30** of the file.
`tail -30 commands.txt | head -1`: with 800 lines, `tail -30` passes lines
771–800; `head -1` keeps the first of those — **line 771** of the file.
The general trick: chain two slicers and reason about the *intermediate
stream* — `head -N | tail -1` picks exactly line N from the top, and
`tail -M | head -1` picks exactly line M from the bottom. Each filter only
ever sees what the previous one passed on.
