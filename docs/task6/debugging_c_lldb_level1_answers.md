# Debugging C with LLDB (Level-1) — Answers with Reasoning

Answer key for `debugging_c_lldb_level1_questions.md`. Every answer includes
the reasoning — when checking a student's work, check the **why**, not just
the letter. A correct letter with a wrong reason is a lucky guess.

---

# Part A — Multiple Choice Questions

**Q1. Answer: (b) — packs a map into the executable.**
Machine code by itself has no idea what `int num1` or "line 7" means. `-g`
adds debug information: which instruction came from which source line, and
where each variable lives. Without it, LLDB is blind. Always compile with
`-g` before debugging.

**Q2. Answer: (c) — no behavioural difference at all.**
Both print `biggest = 12`. The only visible differences are at rest: the
`-g` file is bigger (`ls -l`), and `file` reports `with debug_info, not
stripped`. Debug info is baggage for the debugger, invisible during a normal
run.

**Q3. Answer: (d) — loaded but not started.**
The prompt changes to `(lldb)`, but your program has not begun executing —
the arrow is on the bow, not released. Only `run` starts it. This is why you
can set breakpoints "before the program exists": you're instructing the
debugger about a launch that hasn't happened yet.

**Q4. Answer: (b) — plants a freeze-point at `main`.**
`b main` is the short form of `breakpoint set --name main`. A breakpoint
means "when the running program reaches this place, freeze" — it doesn't run
anything by itself.

**Q5. Answer: (c) — about to run, has NOT run yet.**
The `->` arrow always marks the *next* line to execute. The program is
frozen *before* line 6, not after it. Getting this wrong inverts every
observation you make afterwards — it's the single most important reading
rule in the debugger.

**Q6. Answer: (d) — garbage.**
Before `int num1 = 7;` executes, the variables hold whatever random leftover
numbers happened to be in that memory — `32767`, `-1899491328`, anything. A
variable has no meaningful value until its line runs. (Follows directly from
Q5: the arrow's line hasn't run yet.)

**Q7. Answer: (a) — one source line, then freeze (step-over).**
`next` executes exactly the one line at the arrow and stops again. It's the
only stepping command Level-1 needs; `step`/`finish` become relevant when
your programs have their own functions (Level-2).

**Q8. Answer: (b) — the whole call runs; output appears mid-session.**
Step-**over** means: treat the function call as one step, don't dive inside
it. So the entire printf executes and `biggest = 12` appears right in the
middle of your LLDB session — a surprise the first time, perfectly logical
after.

**Q9. Answer: (a).**
`frame variable` (short `v`) is the wide X-ray: every local variable and its
current value. `print big` (short `p big`) is the narrow question: one
variable. Neither changes anything — both only read.

**Q10. Answer: (b) — into the block.**
The condition `num2 (12) > big (7)` is true, so the arrow moves *into* the
`{ }`, onto `big = num2;`. One more `next` and `frame variable` shows `big`
jump from 7 to 12 — you watched an `if` decide. (With `num2 = 3`, the arrow
would skip the block entirely — see Q27.)

**Q11. Answer: (c) — full speed until the next breakpoint or the end.**
`continue` says "stop stepping." It's the exit from the slow-motion replay
when you've seen what you needed.

**Q12. Answer: (b) — no executable was written.**
The tools work on different files: `clang` reads source, `lldb` reads the
executable. A syntax error makes the compiler refuse to translate — it
prints the error and writes **no output file**. The debugger is downstream
of the compiler; a failed compile means its input never got created.
Debugging is for programs that *compile but behave wrongly*.

**Q13. Answer: (d) — a stale executable.**
The failed compile wrote nothing but also **deleted nothing**, so the old
`./biggest` from the last successful compile is still on disk, and LLDB
happily loads it. She is stepping through the 10:00 program while reading
10:05 source — two different programs. That mismatch is why stale
executables are genuinely dangerous.

**Q14. Answer: (c) — an ordinary program.**
Prove it with tools you already know: `which lldb` (a path like
`/usr/bin/lldb`), `file $(which lldb)` (an ELF executable — the same kind of
file as your own `./biggest`). Its special talent is that its job is to run
*other* programs in slow motion: puppeteer process and puppet process, with
the OS's permission.

---

# Part B — Fill in the Blanks

**Q15.** ( **`next`** → **`frame variable`** ) × many.
The rhythm: look at the arrow, X-ray the variables, execute one line,
repeat. Prediction before each look makes it stick.

**Q16.** `next` = **`n`**, `frame variable` = **`v`**,
`print total` = **`p total`**, `continue` = **`c`**.

**Q17.** plain **Enter**.
The empty command repeats the previous one — which turns the
`next`/`frame variable` loop into hammering two keys.

**Q18.** `with **debug_info**, not **stripped**`.
`file`'s way of saying the `-g` map is on board. "Stripped" would mean the
debug/symbol information was removed.

**Q19.** **`help`** (as in `help next`).
Every LLDB command documents itself — the debugger has its own man-page
habit built in.

**Q20.** `count = **0**, total = **15**`; the program prints
`total = **15**`.
The table runs 5→0: total 0, 5, 9, 12, 14, 15 — the sum 5+4+3+2+1.

**Q21.** straight to the **`printf`** line.
When `count > 0` is false, execution continues after the loop's closing
brace. Watching that jump is watching the loop end — the exact moment, not a
deduction.

**Q22.** `clang` reads your **source** code (`biggest.c`); `lldb` reads
your **executable** (`./biggest`).
Keeping this straight instantly explains both class questions Q1 and Q2.

**Q23.** the **executable** must be newer than the **source file**.
If `biggest.c` has a later timestamp than `biggest`, the executable is stale
— you'd be debugging code older than what's in your editor.

**Q24.** **`step`** (step-in) and **`finish`** (step-out).
They only matter once there are functions other than `main` to dive into
and climb out of — Level-2 material.

---

# Part C — Scenario Questions

**Q25. Kavya's "crash".**
Nothing crashed — the message says so itself: `stop reason =
breakpoint 1.1` means the program froze because of *her* breakpoint 1, the
one she planted with `b main`. Frozen-on-request is the debugger working
exactly as designed: `run` starts the program and the breakpoint catches it
at the first line of `main`. The `4821` is the **PID** — her program runs as
its own process, separate from lldb, and this is its number (the same kind
of number Task-5's `kill` uses). The habit to build: always read the stop
reason before reacting — it distinguishes "you asked for this stop" from a
real crash.

**Q26. Sandeep's "faulty RAM".**
His RAM is fine — he is looking at a **garbage value**. The arrow is *before*
`int big = num1;` (indeed before all three declarations), and a variable has
no meaningful value until its line executes; `frame variable` is showing raw
leftover memory, and strange numbers there are completely normal. `big`
becomes meaningful the moment its line runs: after stepping over
`int big = num1;`, `frame variable` will show `big = 7` (copied from
`num1`), while nothing else changes. Each `next` changes exactly one thing —
that's the proof the machine is behaving perfectly.

**Q27. Anusha's prediction.**
With `num2 = 3` and `big = 7`, the condition `num2 > big` is `3 > 7` —
false — so the arrow **skips the `{ }` block entirely** and lands straight
on the `printf` line; `big` stays 7 and the program prints `biggest = 7`.
The reasoning is the same `if` logic as before, with the opposite outcome.
What the exercise teaches: re-reading code shows you the decision you
*assume*; the debugger shows the decision that *actually happened* — the
arrow's path is the ground truth, and predicting-then-looking is how you
find the places where your assumption and the program disagree.

**Q28. Countdown from 3.**
Trace table, one row per visit to `while (count > 0)`:

| lap | `count` | `total` | enters? |
|-----|---------|---------|---------|
| 1   | 3       | 0       | yes     |
| 2   | 2       | 3       | yes     |
| 3   | 1       | 5       | yes     |
| 4   | 0       | 6       | **no** — arrow jumps to `printf` |

The loop body runs **3 times**, and the program prints `total = 6`
(3+2+1). The final visit with `count = 0` is a visit to the *condition*,
not a lap of the body — a distinction the trace table makes impossible to
blur.

**Q29. Vamsi's ghost.**
This is a **stale executable** (Class Q2). Timeline: an earlier compile
succeeded and wrote `./program`; he then edited the source; the next compile
*failed*, writing nothing — but deleting nothing either — so LLDB is
stepping the old binary while his editor shows the new source. Two
different programs, hence values that make no sense and lines that "don't
exist." The two habits: (1) **read the compiler's output every single
time** — if you saw `error:`, the executable was not rebuilt, so don't touch
the debugger; (2) when in doubt, **compare timestamps** with
`ls -l program.c program` — the executable must be newer than the source.
(Paranoid version: delete the executable before recompiling, so a failed
compile leaves nothing to debug and you get Q1's clean, honest error
instead.)

**Q30. Why `quit` double-checks.**
Because two processes are involved: lldb (the puppeteer) and her frozen
program (the puppet, a separate process — the `Process NNNN stopped` line
named it). Quitting the puppeteer means abandoning a program that is still
alive-but-frozen mid-run; lldb will have to terminate it on the way out, so
it confirms she meant it. The prompt is a small window into the real
architecture: the debugger is not "inside" her program — it is another
ordinary process that controls hers, with the operating system's permission.

**Q31. Investigating a hanging loop.**
Compile with the map: `clang -g program.c -o program`. Then
`lldb ./program`, `b main`, `run`, and `next` until the arrow first reaches
the `while` line. Now do the Level-1 rhythm around the loop — `next`, then
`frame variable` — and keep a trace table: one row per visit to the `while`
line, recording every variable the condition depends on. The evidence that
the loop can never end: the condition's variables stop changing (or change
in the wrong direction) from lap to lap — e.g. the counter that should
decrease never does, because the decrement line is never reached or updates
the wrong variable. The table turns "it seems to hang" into "here is the
lap where the values stopped moving, and here is the line that should have
moved them."

**Q32. Defending the rhythm.**
One look at the end shows you only the final state — it cannot tell you
*which path* produced it. In `biggest.c`, the X-ray at the `if` is what let
you watch the arrow choose the true-branch and see `big` jump 7→12 at that
exact step; from the final value alone you could not tell whether the `if`
entered its block or `big` was 12 all along. In `countdown.c`, stepping
every lap is what showed the loop's shape — the same three lines cycling
with different values — and caught the precise moment `count = 0` made the
arrow jump out. Debugging is about *where the run and your expectation
diverge*; you can only see the divergence at the step where it happens, and
`frame variable` after every `next` is what keeps your eyes open at every
step.
