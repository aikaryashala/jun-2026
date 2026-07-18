# The Call Stack — Question Bank

All questions are about `lsds_compute.c` from the worksheet (same code, same line numbers) and the LLDB session you ran on it. Answer on paper. Where a question asks about a frame, think in three things only: **argument variables, local variables, return address**.

Answers are **not** in this file.

---

# Part A — Multiple Choice

Choose one option for each question.

**A1.** A stack frame is born when a function is:

- A) compiled
- B) declared
- C) called
- D) returned from

**A2.** On this worksheet, the three things inside a frame are:

- A) source code, machine code, output
- B) argument variables, local variables, return address
- C) global variables, local variables, breakpoints
- D) the function name, its line numbers, its printf strings

**A3.** The debugger stops at line 41, before any line of `sum_of_lsds` has run. Which variables already hold meaningful values?

- A) all five: `a`, `b`, `lsd1`, `lsd2`, `sum`
- B) none — nothing has run yet
- C) only `a` and `b`
- D) only `lsd1`, `lsd2`, `sum`

**A4.** While stopped in `sum_of_lsds`, `bt` shows:

```
frame #1: 0x000055555555514c lsds_compute`main at lsds_compute.c:17
```

What is this line of output showing you?

- A) where `main` starts
- B) the return address — where execution continues when `sum_of_lsds` returns
- C) the line where `sum_of_lsds` is declared
- D) an error in `main`

**A5.** `cube` is called twice, and the two `bt` outputs show two *different* frame #1 addresses. Why?

- A) the return address belongs to the call site, and the two calls are on different lines
- B) LLDB prints random addresses each time
- C) `cube`'s code moved in memory between the calls
- D) the second call has more arguments

**A6.** When `sum_of_lsds` returns, the memory its frame used is:

- A) wiped to zeros immediately
- B) left exactly as it was — just no longer in use
- C) given back to the operating system
- D) locked so no function can ever use it again

**A7.** At the *second* stop inside `cube`, before line 30 runs, `result` is already `64`. Where did the `64` come from?

- A) LLDB pre-computed `7 * 7 * 7` incorrectly
- B) C initializes every `int` to the previous result
- C) the first call computed `cube(4) = 64` there, and the second frame was built on the same spot
- D) it is a random number that happens to be 64

**A8.** Which command shows this frame's variables **with their memory addresses**?

- A) `bt`
- B) `frame variable`
- C) `frame variable -L`
- D) `memory read`

**A9.** What does `finish` do?

- A) exits LLDB
- B) runs until the current function returns, landing at its return address
- C) finishes the whole program without stopping
- D) deletes all breakpoints

**A10.** In every `bt` output, frame #0 is always:

- A) `main`
- B) the function that is running right now
- C) the first function the program ever called
- D) the operating system

**A11.** At entry to `sum_of_cubes_lsds`, the untouched local `cube_sum` holds `11`. That `11` is:

- A) the value C chose to initialize it with
- B) the leftover `sum` from the dead `sum_of_lsds` frame that lived on the same spot
- C) proof that `cube_sum` and `sum` are the same variable
- D) the program's final answer arriving early

**A12.** `memory read -f d -s 4 -c 1 &result` prints:

- A) 1 decimal number: the 4 bytes at `result`'s address read as one `int`
- B) 4 numbers, one per byte of `result`
- C) the address of `result` in decimal
- D) the next 4 variables after `result`

**A13.** While `cube` runs (first call), how many frames of *our* functions are alive on the stack?

- A) 1 — only `cube`
- B) 2 — `cube` and `main`
- C) 3 — `cube`, `sum_of_cubes_lsds`, and `main`
- D) 4 — one per function in the file

**A14.** `frame select 1` moves your *view* to the caller's frame. What does it do to the program's execution?

- A) nothing — the program stays stopped exactly where it was
- B) it returns from the current function
- C) it restarts the caller
- D) it runs one line of the caller

---

# Part B — Fill in the Blanks

Copy each sentence into your notebook with the blanks filled.

**B1.** When a function is entered, its **argument** variables arrive already ________ by the caller, while its **local** variables arrive holding ________ values.

**B2.** The return address of the first `cube` call points into line ____ of `lsds_compute.c`; the second call's return address points into line ____.

**B3.** To pack the source-line and variable-name map into the executable, we compile with `clang ____ lsds_compute.c -o lsds_compute`.

**B4.** The LLDB command `bt` is short for ____________.

**B5.** In the call `sum_of_lsds(234, 97)`: `a = ____`, `b = ____`, and the returned `sum` is ____.

**B6.** At entry to `sum_of_cubes_lsds`, the ghost value `11` sits in the local `____________`, left behind by the variable `________` of the previous function.

**B7.** The command `____________ ____ --name cube` makes the debugger stop every time `cube` is entered.

**B8.** In `bt`, frame #0 is the function ________ ______ ______, and frame #1 is its ________.

**B9.** `finish` runs until the current function ____________, and control lands at its ____________ ____________.

**B10.** The second `cube` call computes `cube(7) = ____`, so the program finally prints `Sum of cubes of LSDs = ____`.

---

# Part C — Scenario Questions

**C1.** Stopped at line 41 (entry of `sum_of_lsds`), your neighbour sees:

```
(int) lsd2 = 32764
```

and says: "My program is broken — I never put 32764 anywhere!" Is the program broken? Explain what `32764` is and what will happen to it.

**C2.** You are stopped in `cube` and `bt` shows:

```
  * frame #0: 0x000055555555520c lsds_compute`cube(n=7) at lsds_compute.c:30
    frame #1: 0x0000555555555241 lsds_compute`sum_of_cubes_lsds(x=234, y=97) at lsds_compute.c:61
    frame #2: 0x000055555555514c lsds_compute`main at lsds_compute.c:18
```

Answer from this output alone: (a) Is this the first or the second call to `cube`, and how do you know? (b) What is `n`? (c) Exactly where will `cube` return to? (d) How many of our frames are alive right now?

**C3.** Before `run`, you set all three breakpoints: `b sum_of_lsds`, `b sum_of_cubes_lsds`, `b cube`. List, in order, every stop the debugger will make until the program ends — function name and the line it stops on each time.

**C4.** At the second `cube` stop, a student prints `result` *before* line 30 has executed, sees `64`, and concludes: "LLDB ran my multiplication early." What really happened? Use the words *frame*, *died*, and *same spot* in your answer.

**C5.** During the **first** stop inside `cube`, a student's memory-ladder drawing shows `main`'s frame as: `num1 = 234`, `num2 = 97`, `result1 = 11`, `result2 = 407`. One rung is wrong. Which one, why is it wrong, and what should it show instead?

**C6.** Suppose line 15 is changed to `num2 = 45;` and the program is rebuilt. At entry to `sum_of_cubes_lsds`, what ghost value would you now expect in `cube_sum`, and why? (Trace `sum_of_lsds(234, 45)` on paper first.)

**C7.** From the **first** stop inside `cube`, you type `finish`. Describe what happens: where control lands, what happens to `cube`'s frame, and what value `cube1` holds after the assignment on that line completes.

**C8.** Stopped in `cube` (second call), you run these two commands and both show `64`:

```
(lldb) frame variable result
(lldb) memory read -f d -s 4 -c 1 &result
```

Explain why they *must* agree — what is `frame variable` really doing, and what did `-g` contribute to it?

---

When you finish, check the answer key — reasoning first, numbers second. If your ladder drawings disagree with your answers here, one of them is lying; find out which.
