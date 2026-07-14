# Debugging C with LLDB (Level-1) — Question Bank

Answer these **after** finishing the Task-6 worksheet (Debugging C Code
Using Clang Debugger (LLDB) — Level-1). Write your answers in your notebook
first. The worksheet's rule applies to these questions too: **don't guess —
predict, then verify** by stepping through the programs yourself, but only
*after* writing your prediction down.

The programs referred to below are the worksheet's `biggest.c` (numbers 7
and 12, an `if`) and `countdown.c` (`count = 5`, `total = 0`, a `while`
loop).

---

# Part A — Multiple Choice Questions

Choose the one best option.

**Q1.** What does the `-g` flag in `clang -g biggest.c -o biggest` do?

- (a) Makes the program run faster
- (b) Packs debug information into the executable — a map from machine code back to your source lines and variable names
- (c) Turns on warnings
- (d) Runs the program immediately after compiling

**Q2.** You compile the same program with and without `-g` and run both
normally. What difference do you observe in their *behaviour*?

- (a) The `-g` version prints extra debugging lines
- (b) The `-g` version runs slower
- (c) None — both behave identically; debug info is invisible during a normal run
- (d) The `-g` version asks for a breakpoint before starting

**Q3.** Immediately after `lldb ./biggest`, what state is your program in?

- (a) Running in the background
- (b) Running, but paused at line 1
- (c) Already finished — LLDB shows you a recording
- (d) Loaded but **not started** — like an arrow on the bow, not yet released

**Q4.** What does `b main` do?

- (a) Runs the program up to `main`
- (b) Sets a breakpoint: "when the running program reaches `main`, freeze"
- (c) Prints the source code of `main`
- (d) Renames the program to `main`

**Q5.** After `run`, LLDB prints `stop reason = breakpoint 1.1` and an
arrow `->` next to `int num1 = 7;`. What does the arrow mean?

- (a) This line has just finished executing
- (b) This line contains the bug
- (c) This line is **about to run and has NOT run yet**
- (d) This is the line where the program will end

**Q6.** At that first stop (arrow on `int num1 = 7;`), `frame variable`
shows `num1 = 32767` or some other strange number. What are you looking at?

- (a) A debugger bug
- (b) The value 7, displayed in a different number system
- (c) An overflow caused by the breakpoint
- (d) Garbage — raw leftover memory; a variable has no meaningful value until its line executes

**Q7.** What exactly does `next` (short `n`) do?

- (a) Executes exactly one of your source lines, then freezes again (step-over)
- (b) Jumps to the next breakpoint
- (c) Executes one machine instruction
- (d) Moves the arrow without executing anything

**Q8.** The arrow is on `printf("biggest = %d\n", big);` and you press
`next`. What happens?

- (a) The debugger steps into printf's own source code
- (b) The whole printf call runs — `biggest = 12` appears — and the arrow moves to the next line; that's why it is called step-**over**
- (c) Nothing prints until you quit LLDB
- (d) LLDB refuses — printf cannot be stepped

**Q9.** What is the difference between `frame variable` and `print big`?

- (a) `frame variable` shows every local variable and its value; `print big` shows just the one you name
- (b) `frame variable` changes values; `print` only reads them
- (c) They are unrelated commands from different debuggers
- (d) `print big` sets a breakpoint on `big`

**Q10.** In `biggest.c`, `num2` is `12` and `big` is `7`. The arrow is on
`if (num2 > big)`. You press `next`. Where does the arrow go?

- (a) It skips past the `{ }` block to the printf
- (b) Into the block, onto `big = num2;` — the condition is true
- (c) Directly to `return 0;`
- (d) Back to the top of `main`

**Q11.** What does `continue` (short `c`) do?

- (a) Executes one more line, like `next`
- (b) Restarts the program from the beginning
- (c) Stops stepping and runs at full speed until the next breakpoint or the end
- (d) Continues to the next function

**Q12.** You remove a semicolon from `biggest.c`, run `clang -g biggest.c
-o biggest`, see an error — and there is now nothing new to debug. Why?
(Class Q1)

- (a) LLDB is blocked whenever any file has an error
- (b) The compile failed, so no executable was written — and the debugger debugs the executable, not the `.c` file
- (c) Debuggers cannot handle semicolons
- (d) The breakpoint was deleted by the error

**Q13.** Same broken source as Q12 — but your friend *can* still debug
"successfully". What is actually happening? (Class Q2)

- (a) Her LLDB is a newer version that tolerates errors
- (b) The compiler fixed the semicolon automatically
- (c) LLDB is reading the `.c` file directly
- (d) She is debugging a stale executable — the old binary from the last successful compile, which the failed compile did not delete

**Q14.** What kind of thing is `lldb` itself? (Class Q3)

- (a) A special mode of the Linux kernel
- (b) A part of the clang compiler
- (c) An ordinary executable program — a process whose special talent is running and controlling *other* processes
- (d) A hardware feature of the CPU

---

# Part B — Fill in the Blanks

Write the exact missing word, command, or value.

**Q15.** The Level-1 rhythm: `b main` → `run` → ( __________ →
__________ ) × many → `continue` → `quit`.

**Q16.** The short forms: `next` = __________, `frame variable` =
__________, `print total` = __________, `continue` = __________.

**Q17.** Pressing plain __________ at the `(lldb)` prompt repeats the
previous command — so you can hammer it to keep stepping.

**Q18.** `file biggest` reports the `-g` version as
`with __________, not __________`.

**Q19.** Every LLDB command documents itself: type __________ `next` at the
`(lldb)` prompt to read about `next`.

**Q20.** In `countdown.c`, the completed trace table ends with
`count = ______, total = ______` on the final visit to the `while` line —
and the program prints `total = ______`.

**Q21.** On the lap where `count` is `0`, pressing `next` on the `while`
line makes the arrow jump past the `}` straight to the __________ line —
that jump *is* the loop ending.

**Q22.** `clang` reads your __________ code; `lldb` reads your __________.
(Fill each blank with the kind of file.)

**Q23.** To detect a stale executable, compare timestamps with
`ls -l biggest.c biggest`: the __________ must be **newer** than the
__________.

**Q24.** The two stepping commands saved for Level-2, once your programs
have functions other than `main`: __________ (step-in) and __________
(step-out).

---

# Part C — Scenario Questions

Answer in 2–4 sentences each. Name the LLDB commands involved.

**Q25.** The moment LLDB prints `Process 4821 stopped`, Kavya panics: "My
program crashed!" Read the rest of the message with her — 
`stop reason = breakpoint 1.1` — and explain what actually happened, why it
is exactly what she asked for, and what the number `4821` is.

**Q26.** Sandeep steps to the first line of `main`, runs `frame variable`,
and sees `big = -1899491328`. He concludes his RAM is faulty and wants to
reinstall Ubuntu. Talk him down: what is this value, when will `big` get a
meaningful value, and what should he observe after stepping over
`int big = num1;`?

**Q27.** Anusha edits `biggest.c` to `int num2 = 3;`, recompiles with `-g`,
and steps to the `if (num2 > big)` line. Ask her the worksheet's question:
predict where the arrow goes on the next `next`. Give the answer, the
reasoning, and what this exercise teaches that re-reading the code cannot.

**Q28.** Fill in the countdown prediction: with `count = 3` instead of 5,
write the full trace table (each visit to the `while` line: `count`,
`total`, enters or not), and state the final printed total. How many times
does the loop body run?

**Q29.** Vamsi's edited program still shows the *old* behaviour in LLDB —
values that don't match the source on his screen, the arrow landing on lines
that "don't exist any more." Diagnose it (Class Q2 has the name), explain
the timeline that causes it, and give the two habits — one about reading,
one about timestamps — that prevent it.

**Q30.** During a session, Ramya types `quit` and LLDB asks: "Do you really
want to proceed?" Her program is still frozen at a breakpoint. Why does LLDB
double-check here, and what does this reveal about the relationship between
the lldb process and her program's process? (Class Q3's picture helps.)

**Q31.** A program with a `while` loop seems to "hang" when run normally —
suspicion: the loop never ends. Describe, step by step, how to investigate
with the Level-1 toolkit: which compile flag, which LLDB commands to reach
the loop, what to record each lap, and what evidence would confirm the loop
condition can never become false.

**Q32.** Bhargav asks: "Why do we bother with `frame variable` after every
single `next`? The program only changes one variable per line anyway."
Defend the rhythm: what did watching-every-step reveal in `biggest.c` (think
of the `if`) and in `countdown.c` (think of the final lap) that a single
look at the end could never show?
