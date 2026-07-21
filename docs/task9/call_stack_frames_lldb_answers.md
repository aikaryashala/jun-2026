# The Call Stack — Answers with Reasoning

Check the **why**, not just the letter. Every answer below leans on the three frame contents — argument variables, local variables, return address — and the golden picture: a frame is born at the call, dies at the return, and nothing wipes the memory in between.

---

# Part A — Multiple Choice

**A1. C) called** — A frame is a workspace for one *call*. Compiling and declaring produce no frames at all; returning is when the frame *dies*, not when it is born.

**A2. B) argument variables, local variables, return address** — The three things this whole task is about. Globals live elsewhere, and code/output are not stored per-call.

**A3. C) only `a` and `b`** — Filling the arguments is part of *making the call*: the caller copies `num1` and `num2` into `a` and `b` before the function's first line runs. The locals `lsd1`, `lsd2`, `sum` are untouched memory — junk until a line assigns them.

**A4. B) the return address** — Frame #1's location is the exact spot in the caller where execution continues after `return`. Line 17 is `result1 = sum_of_lsds(num1, num2);` — the function returns into the middle of that assignment to deliver its value.

**A5. A) the return address belongs to the call site** — Line 60 and line 61 are two different places to go back to. The function is the same; the *calls* are different, so each carries its own return address. (Code never moves during a run, so C is out.)

**A6. B) left exactly as it was** — "The frame dies" only means the program stops using that memory. Nothing wipes it — which is exactly why the next function built there finds ghosts.

**A7. C) leftover from `cube(4) = 64`** — The first call's frame computed 64 in `result` and died. The second call built its frame on the same spot, so its uninitialized `result` starts as the past. Same variable, same address, history included.

**A8. C) `frame variable -L`** — The `-L` adds the location (address) of each variable. `frame variable` gives names and values only; `bt` shows frames, not variables; `memory read` needs you to already know an address.

**A9. B) runs until the current function returns** — `finish` lets the function complete and stops back at the return address in the caller. It doesn't exit LLDB (`quit` does) or run the program to the end (`continue` tries that).

**A10. B) the function running right now** — `bt` reads like "how did I get here": #0 is *here*, #1 called it, #2 called that one, down to `main` and below.

**A11. B) the leftover `sum` from the dead frame** — `sum_of_lsds` computed `sum = 11`, returned, and its frame died in place. `sum_of_cubes_lsds` built its frame on the same memory, and its untouched `cube_sum` landed on the old `sum`'s spot. They are *not* the same variable (C is wrong) — they are two variables that lived at the same address at different times.

**A12. A) 1 decimal number — the 4 bytes at `result`'s address as one `int`** — `-f d` = print in decimal, `-s 4` = group 4 bytes per item, `-c 1` = one item. It is `frame variable result` without the label.

**A13. C) 3** — `main` called `sum_of_cubes_lsds`, which called `cube`. All three are alive at once; only `cube` (frame #0) is running. `sum_of_lsds` is *not* on the stack — it already returned. (D confuses "functions in the file" with "calls on the stack".)

**A14. A) nothing — the program stays stopped** — `frame select`, `up`, `down` move your *viewpoint* so you can inspect a caller's variables. Execution doesn't move until `next`, `finish`, or `continue`.

---

# Part B — Fill in the Blanks

**B1.** arguments arrive **filled** (copied by the caller); locals arrive holding **junk / leftover** values.

**B2.** first call → line **60**, second call → line **61** — `cube1 = cube(lsd1);` and `cube2 = cube(lsd2);`. Two calls, two return addresses.

**B3.** `clang **-g** lsds_compute.c -o lsds_compute` — `-g` packs the map from machine code back to source lines and variable names.

**B4.** **backtrace** — the trail of frames from "running now" back to `main`.

**B5.** `a = **234**`, `b = **97**`, returned `sum` = **11** — LSDs are 4 and 7; `4 + 7 = 11`.

**B6.** the ghost sits in **`cube_sum`**, left behind by **`sum`** — the new frame was built where the old one died.

**B7.** `**breakpoint set** --name cube` (short form `b cube`).

**B8.** frame #0 is the function **running right now**; frame #1 is its **caller**.

**B9.** `finish` runs until the current function **returns**, landing at its **return address**.

**B10.** `cube(7) = **343**`, and `Sum of cubes of LSDs = **407**` — `64 + 343 = 407`.

---

# Part C — Scenario Questions

**C1.** The program is fine. `lsd2` has not been assigned yet — line 42 hasn't run — so it shows whatever bytes were already lying at that address: junk, i.e. leftover history, not an error. The moment line 42 executes, `32764` is overwritten by `b % 10 = 7` and is gone forever. Junk in a local *before* its first assignment is normal; using a local before assigning it is the actual bug to fear.

**C2.** (a) The **second** call — frame #1 points at line **61** (`cube2 = cube(lsd2);`); the first call would show line 60. (b) `n = 7` — printed right in frame #0: `cube(n=7)`. (c) Back into **line 61 of `sum_of_cubes_lsds`**, to finish the assignment into `cube2`. (d) **Three** of our frames: `cube`, `sum_of_cubes_lsds`, `main` (the rows below that belong to the C library that started `main`).

**C3.** Four stops, in call order:
1. `sum_of_lsds` at line **41** (called from main, line 17)
2. `sum_of_cubes_lsds` at line **57** (called from main, line 18)
3. `cube` at line **30** — first call, from line 60, `n = 4`
4. `cube` at line **30** — second call, from line 61, `n = 7`

Then the program prints its two lines and exits. Breakpoints fire in *call* order, and `sum_of_lsds` fires only once because it is called only once.

**C4.** LLDB ran nothing early. The first call's **frame** computed `result = 64` and then **died** when `cube(4)` returned — with the memory left as-is. The second call built its frame on the **same spot**, so its brand-new, *uninitialized* `result` starts out showing the old 64. The student is looking at the past, not the future: once line 30 runs, `result` becomes `7 * 7 * 7 = 343`.

**C5.** The `result2 = 407` rung is wrong. At the first `cube` stop, line 18 of `main` is still *unfinished* — `sum_of_cubes_lsds` hasn't returned (it's frame #1, mid-work: even `cube1` has no value yet, since the first `cube` call is the one running right now). No `407` exists anywhere in the program at this instant. The rung should show `result2 = ?` (junk). `result1 = 11` is correct — line 17 completed long ago.

**C6.** Expect the ghost `9`. Trace: `sum_of_lsds(234, 45)` → `lsd1 = 4`, `lsd2 = 5`, `sum = 9`. The ghost in `cube_sum` is whatever the dead frame's `sum` held — change the inputs and the ghost changes with them. That is the proof it's history, not coincidence.

**C7.** `finish` lets `cube(4)` run to its `return result;` and stops back at the return address — **line 60 of `sum_of_cubes_lsds`**, mid-assignment. `cube`'s frame **dies** (its memory, including the 64 in `result`, is simply left behind — which is exactly the ghost the second call will find). When line 60's assignment completes, `cube1 = 64`.

**C8.** They must agree because they read the **same bytes at the same address**. `frame variable result` *is* a labelled memory read: take `result`'s address, read 4 bytes, print as an `int`. The `memory read` does it by hand. The label — knowing that `result` means "these 4 bytes at this address" — is exactly what `-g` put into the executable: without it, LLDB would still read memory, but couldn't connect the name `result` to the address.

---

**Pattern to notice across your mistakes:** if you missed questions, it was almost always one of three confusions — thinking junk is random instead of leftover history, attaching the return address to the *function* instead of the *call*, or thinking a frame's memory is cleaned when it dies. All three are the same lesson: the stack is reused, never wiped.
