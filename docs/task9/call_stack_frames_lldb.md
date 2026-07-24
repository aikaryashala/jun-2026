# The Call Stack — Frames, Return Addresses, and Leftovers

**Goal.** In Task 6 you used LLDB to walk through a program one line at a time. Today you will look *underneath* the walk: every function call gets its own little workspace in memory called a **stack frame**, and the frames pile up into the **call stack**. You will stop the program mid-call and see, with your own eyes, exactly three things inside each frame:

1. the **argument variables** (values the caller sent in),
2. the **local variables** (the function's own scratch space),
3. the **return address** (the exact place in the caller where work continues after `return`).

Only these three. Nothing else on this sheet.

**You need:** your Linux VM (WSL on Windows) with `clang` and `lldb` working (you set this up in Task 6), a notebook, and a pencil — you will be writing down addresses and comparing them.

> **The golden picture**
> A frame is **born** when a function is called and **dies** when it returns. But dying only means "the program stops using that memory" — nothing is wiped. The next frame built on the same spot finds whatever the last one left behind.

---

## Your paper tool — the memory ladder

You already know this diagram from class: memory drawn as a **ladder**, one variable per rung — the variable's **name on one side**, its current value inside the box, and its **memory address on the other side**. Every frame you draw on this sheet uses exactly this format. For `main`'s frame just after line 15 it looks like:

```
              ┌─────────┐
   num1       │   234   │   0x7fffffffe0a8
              ├─────────┤
   num2       │   97    │   0x7fffffffe0ac
              ├─────────┤
   result1    │    ?    │   0x7fffffffe0b0
              ├─────────┤
   result2    │    ?    │   0x7fffffffe0b4
              └─────────┘
                  main
```

Write `?` on a rung that has not been assigned yet — and when the debugger later shows you what that `?` actually holds, write the real number beside it. The addresses are never invented: copy them from the debugger — `frame variable -L` prints every variable's address (the sample addresses above only show the shape). When several functions are alive at the same time, draw one ladder **per frame** and stack the ladders: the running function on top, `main` at the bottom — the same order `bt` prints them.

---

## The program: `lsds_compute.c`

Create a file named exactly `lsds_compute.c` and type this program **exactly as shown** — the line numbers below matter for the whole worksheet:

```c
#include <stdio.h>

int cube(int n);
int sum_of_lsds(int a, int b);
int sum_of_cubes_lsds(int a, int b);

int main()
{
    int num1;
    int num2;
    int result1;
    int result2;

    num1 = 234;
    num2 = 97;

    result1 = sum_of_lsds(num1, num2);
    result2 = sum_of_cubes_lsds(num1, num2);

    printf("Sum of LSDs = %i\n", result1);
    printf("Sum of cubes of LSDs = %i\n", result2);

    return 0;
}

int cube(int n)
{
    int result;

    result = n * n * n;

    return result;
}

int sum_of_lsds(int a, int b)
{
    int lsd1;
    int lsd2;
    int sum;

    lsd1 = a % 10;
    lsd2 = b % 10;

    sum = lsd1 + lsd2;

    return sum;
}

int sum_of_cubes_lsds(int x, int y)
{
    int lsd1;
    int lsd2;
    int cube_sum;
    int cube1;
    int cube2;

    lsd1 = x % 10;
    lsd2 = y % 10;

    cube1 = cube(lsd1);
    cube2 = cube(lsd2);

    cube_sum = cube1 + cube2;

    return cube_sum;
}
```

(LSD = least significant digit — the last digit. `234 % 10` is `4`.)

Before touching the compiler, predict on paper: what two lines will this program print? Trace it with a state table like always. Keep the prediction — you will check it in a minute.

---

## Iteration 1 — Build with the debugger's eyes: `-g`

**a. What we set up**

```
clang -g lsds_compute.c -o lsds_compute
./lsds_compute
```

**b. Task**

Compile and run. Compare the output with your paper prediction.

**c. Observation (what you should find)**

```
Sum of LSDs = 11
Sum of cubes of LSDs = 407
```

`4 + 7 = 11`, and `4³ + 7³ = 64 + 343 = 407`. The `-g` flag changes nothing about what the program *does* — it packs a map into the executable so LLDB can connect machine instructions back to your source lines and variable names. Without `-g`, the debugger sees addresses; with `-g`, it sees *your* names.

**Takeaway to say out loud:** "`-g` puts a map in the executable so the debugger can speak my language."

---

## Iteration 2 — Stop inside a call and meet its frame

**a. What we set up**

Start the debugger, plant a breakpoint on `sum_of_lsds`, and run:

```
lldb lsds_compute
(lldb) breakpoint set --name sum_of_lsds
(lldb) run
```

The program stops at line 41 — the first statement inside `sum_of_lsds`, **before** it has executed.

**b. Task**

Look inside the frame:

```
(lldb) frame variable
```

Write all five values in your notebook, in two groups: *arguments* (`a`, `b`) and *locals* (`lsd1`, `lsd2`, `sum`).

**c. Observation (what you should find)**

Something like (your locals' values will differ):

```
(int) a = 234
(int) b = 97
(int) lsd1 = 0
(int) lsd2 = 48059
(int) sum = 1
```

The two groups arrived in different states. The **arguments are already filled** — the caller copied `num1` and `num2` into them as part of making the call. The **locals are junk** — no line of this function has run yet, so they hold whatever was lying in that memory. Junk values are not errors; they are simply *uninitialized*.

Now also note down **where** these variables live:

```
(lldb) frame variable -L
```

The long hex numbers on the left (like `0x00007fffffffe080`) are memory addresses. Draw this frame as a memory ladder — names on one side, addresses on the other — and label the `sum` rung "`&sum`, 1st run". You will meet this neighbourhood again.

**Takeaway to say out loud:** "Arguments arrive filled by the caller; locals arrive as whatever was left in memory."

---

## Iteration 3 — The call stack and the return address

**a. What we set up**

Still stopped inside `sum_of_lsds`. Ask for the whole stack:

```
(lldb) bt
```

(`bt` = backtrace.)

**b. Task**

Read the frames from top to bottom and copy the important parts:

```
* thread #1, name = 'lsds_compute', stop reason = breakpoint 1.1
  * frame #0: 0x00005555555551b4 lsds_compute`sum_of_lsds(a=234, b=97) at lsds_compute.c:41
    frame #1: 0x000055555555514c lsds_compute`main at lsds_compute.c:17
    frame #2: ... libc.so.6`__libc_start_call_main ...
```

(Your hex addresses will differ; the line numbers should match.)

Then visit `main`'s frame while `sum_of_lsds` is still alive:

```
(lldb) frame select 1
(lldb) frame variable
(lldb) frame select 0
```

**c. Observation (what you should find)**

- `frame #0` is the function running *right now*. `frame #1` is who called it. The stack reads like a "how did I get here" story.
- The address printed on `frame #1`'s line, pointing at **`lsds_compute.c:17`**, is the **return address**: the exact spot in `main` where execution will continue when `sum_of_lsds` returns. Line 17 is `result1 = sum_of_lsds(num1, num2);` — the function returns *into the middle of that assignment*, to deliver `sum` into `result1`. Write it down: "return address of this call → main, line 17".
- In frame #1, `num1 = 234` and `num2 = 97` are alive and well, but `result1` and `result2` are still junk — line 17 hasn't finished. Both frames exist **at the same time**; only frame #0 is running.

Now watch the return happen:

```
(lldb) finish
(lldb) next
(lldb) frame variable result1
```

`finish` runs until the current function returns — right back to that return address on line 17. `next` completes the assignment, and `result1` is now `11`. The `sum_of_lsds` frame is gone.

**Takeaway to say out loud:** "The return address is the caller's line where work continues — every frame carries one."

---

## Iteration 4 — A ghost in the next frame

**a. What we set up**

`sum_of_lsds` has returned; its frame is dead. The very next call, `sum_of_cubes_lsds`, is about to build its frame — largely on the **same memory**. Plant the next breakpoint and continue:

```
(lldb) breakpoint set --name sum_of_cubes_lsds
(lldb) continue
```

The program stops at line 57, before any line of `sum_of_cubes_lsds` has run.

**b. Task**

Look at the brand-new frame's variables — especially the ones no line has touched yet:

```
(lldb) frame variable
(lldb) frame variable -L
```

Hunt through the locals for familiar numbers. Compare the address of `cube_sum` with the "`&sum`, 1st run" address in your notebook.

**c. Observation (what you should find)**

`x = 234` and `y = 97` are filled (arguments always are). But among the "junk" locals you should spot a **ghost**: `cube_sum` holding `11` — the exact value `sum` computed in the *previous* function. And its address is in the same neighbourhood you wrote down (on many runs, exactly the same address). You may spot `4` and `7` haunting `lsd1` and `lsd2` too.

This is the golden picture in action: the old frame died, nothing wiped its memory, and the new frame's uninitialized locals are simply *reading the past*. (If your compiler laid the frame out differently and the `11` sits in a different local — or doesn't appear — write down what you *do* see; the lesson is that the junk is leftover history, not random noise.)

**Takeaway to say out loud:** "Uninitialized is not random — it is whatever the last frame left behind."

---

## Iteration 5 — One function, two calls, two return addresses

**a. What we set up**

`sum_of_cubes_lsds` calls `cube` twice — line 60 and line 61. Plant a breakpoint in `cube` and run to the **first** call:

```
(lldb) breakpoint set --name cube
(lldb) continue
```

**b. Task**

You are now inside `cube` for the call from line 60 (`cube(lsd1)`, so `n = 4`). Record two things:

```
(lldb) bt
(lldb) frame variable
(lldb) frame variable -L result
```

From `bt`, write down frame #1's address **and** line number. From `frame variable`, note `n` and the junk in `result`. From `-L`, write down "`&result`, 1st call".

**Now the big drawing of the day — for this first call only.** While stopped right here, draw the **entire call stack as memory ladders**: every variable of every function that is alive — `cube` on top, `sum_of_cubes_lsds` below it, `main` at the bottom. Names on one side, addresses on the other. Collect each frame's values **and addresses** frame by frame:

```
(lldb) frame variable -L
(lldb) frame select 1
(lldb) frame variable -L
(lldb) frame select 2
(lldb) frame variable -L
(lldb) frame select 0
```

Mark every ghost you spot on its rung. Only after the drawing is complete, go to the **second** call:

```
(lldb) continue
(lldb) bt
```

**c. Observation (what you should find)**

First stop — `bt` shows the call stack three deep, and frame #1 points at **line 60**:

```
  * frame #0: ... cube(n=4) at lsds_compute.c:30
    frame #1: 0x0000555555555230 lsds_compute`sum_of_cubes_lsds(x=234, y=97) at lsds_compute.c:60
    frame #2: ... main at lsds_compute.c:18
```

And your full-stack ladder drawing should look like this (the addresses here are samples — copy your real ones; junk values will differ; ghosts marked):

```
              ┌─────────┐
   n          │    4    │   0x7fffffffe058
              ├─────────┤
   result     │    ?    │   0x7fffffffe05c
              └─────────┘
                  cube                        ← running now

              ┌─────────┐
   x          │   234   │   0x7fffffffe070
              ├─────────┤
   y          │   97    │   0x7fffffffe074
              ├─────────┤
   lsd1       │    4    │   0x7fffffffe078
              ├─────────┤
   lsd2       │    7    │   0x7fffffffe07c
              ├─────────┤
   cube_sum   │   11    │   0x7fffffffe080   ← ghost of sum
              ├─────────┤
   cube1      │    ?    │   0x7fffffffe084
              ├─────────┤
   cube2      │    ?    │   0x7fffffffe088
              └─────────┘
              sum_of_cubes_lsds

              ┌─────────┐
   num1       │   234   │   0x7fffffffe0a8
              ├─────────┤
   num2       │   97    │   0x7fffffffe0ac
              ├─────────┤
   result1    │   11    │   0x7fffffffe0b0
              ├─────────┤
   result2    │    ?    │   0x7fffffffe0b4
              └─────────┘
                  main
```

Three functions alive at once, three ladders. Check two things against your notebook: the ghost of `sum` sits in the middle ladder, and `cube_sum`'s address is the same neighbourhood as the "`&sum`, 1st run" address you wrote in Iteration 2.

Second stop — same function, same breakpoint, but frame #1 now shows a **different address**, pointing at **line 61**:

```
  * frame #0: ... cube(n=7) at lsds_compute.c:30
    frame #1: 0x0000555555555241 lsds_compute`sum_of_cubes_lsds(x=234, y=97) at lsds_compute.c:61
    frame #2: ... main at lsds_compute.c:18
```

Same function `cube`, two different return addresses — because the return address belongs to the **call site**, not to the function. Each call must go back to *its own* line. Also notice frame #2: while `cube` runs, `sum_of_cubes_lsds`'s own return address (main, line 18) is still patiently waiting two frames down.

**Takeaway to say out loud:** "The return address belongs to the call, not the function — two calls, two return addresses."

---

## Iteration 6 — The second call finds the first call's leftovers

**a. What we set up**

You are still at the second stop inside `cube`, with `n = 7`, **before** line 30 has executed. The first call's frame (which computed `cube(4) = 64` in `result`) died when it returned — on this very spot.

**b. Task**

```
(lldb) frame variable
(lldb) frame variable -L result
```

Compare `result`'s value and address with the "1st call" notes in your notebook.

**c. Observation (what you should find)**

```
(int) n = 7
(int) result = 64
```

In the **first** call, `result` started as junk — that memory hadn't held anything meaningful yet. In the **second** call, `result` starts as `64`: the cube of 4, computed by the previous call, still sitting there. And `-L` shows the **same address** both times — the second frame was built exactly where the first one died.

This is the cleanest proof of the whole sheet: same function, same variable, same address — the "initial value" of `result` is just history.

**Takeaway to say out loud:** "The second call moves into the first call's house — furniture included."

---

## Iteration 7 — Read the memory yourself

**a. What we set up**

Variables are just labelled spots in memory. Skip the labels and read the raw spots. Still stopped inside `cube` (second call):

```
(lldb) p &result
(lldb) memory read -f d -s 4 -c 1 &result
```

`-f d` = format as decimal, `-s 4` = each item is 4 bytes (one `int`), `-c 1` = one item.

**b. Task**

Read `result` raw, then climb one frame up and read a whole stretch of `sum_of_cubes_lsds`'s neighbourhood:

```
(lldb) frame select 1
(lldb) memory read -f d -s 4 -c 8 &lsd1
```

Scan the eight numbers for values you recognise. Then let the program finish:

```
(lldb) continue
```

**c. Observation (what you should find)**

- The single read shows `64` at `result`'s address — the same number `frame variable` showed, because `frame variable` *is* a labelled memory read.
- In the eight-int stretch you should spot old friends: `4`, `7`, `64`, maybe `11`, sitting side by side. A frame is not a magic box — it is a few dozen bytes of ordinary memory, and `-g`'s map is what lets LLDB put names on them.
- After `continue`, the program prints its two lines and exits. `quit` leaves LLDB.

**Takeaway to say out loud:** "A frame is just bytes; the debugger's names are labels on addresses."

---

## Practice on paper — draw the stack

Close the laptop. From your notes, draw the **full call stack at the moment of the second stop inside `cube`** — three frames, and for each frame: its argument variables with values, its local variables with values (mark leftovers as *ghosts*), and its return address (function + line).

Check yourself against this:

| Frame | Arguments | Locals at that instant | Return address |
|---|---|---|---|
| #0 `cube` | `n = 7` | `result = 64` *(ghost of 1st call)* | `sum_of_cubes_lsds`, line 61 |
| #1 `sum_of_cubes_lsds` | `x = 234`, `y = 97` | `lsd1 = 4`, `lsd2 = 7`, `cube1 = 64`, `cube2 = ?` *(junk)*, `cube_sum = ?` *(likely ghost `11`)* | `main`, line 18 |
| #2 `main` | — | `num1 = 234`, `num2 = 97`, `result1 = 11`, `result2 = ?` *(junk — line 18 not finished)* | — |

If any cell surprised you, redo the iteration it came from.

---

## One-page reference — LLDB commands used today

| Command | Short form | What it does |
|---|---|---|
| `breakpoint set --name F` | `b F` | stop whenever function `F` is entered |
| `run` | `r` | start the program under the debugger |
| `continue` | `c` | run until the next breakpoint |
| `next` | `n` | execute one line, stepping *over* calls |
| `finish` | | run until the current function returns (to its return address) |
| `bt` | | show the call stack: frame #0 = running now, below it the callers |
| `frame select N` | `f N` | move your view to frame N (`up` / `down` also work) |
| `frame variable` | `v` | show this frame's arguments and locals |
| `frame variable -L` | `v -L` | same, with each variable's memory address |
| `print EXPR` | `p` | evaluate, e.g. `p &result` for an address |
| `memory read -f d -s 4 -c N ADDR` | | read N raw 4-byte ints at ADDR, in decimal |
| `quit` | `q` | leave LLDB |

**The three things in every frame:** argument variables (filled by the caller) · local variables (uninitialized until assigned) · return address (the caller's line where work continues).

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| call stack | పిలుపుల దొంతర | ఫంక్షన్ పిలుపుల ఫ్రేమ్‌ల పోగు — పైన ఉన్నది ఇప్పుడు నడుస్తున్నది |
| stack frame | ఫ్రేమ్ / చట్రం | ఒక్క ఫంక్షన్ పిలుపు కోసం ఏర్పడే పని స్థలం |
| argument variable | ఆర్గ్యుమెంట్ చరరాశి | పిలిచేవాడు నింపి పంపిన విలువ |
| local variable | స్థానిక చరరాశి | ఫంక్షన్ లోపలి సొంత చరరాశి |
| return address | తిరుగు చిరునామా | ఫంక్షన్ ముగిశాక కొనసాగవలసిన పిలిచినవాడి లైన్ |
| caller | పిలిచినవాడు | ఫంక్షన్‌ను పిలిచిన ఫంక్షన్ |
| uninitialized | విలువ ఇవ్వనిది | ఇంకా ఏ విలువా పెట్టని చరరాశి |
| garbage / leftover value | మిగిలిపోయిన విలువ | పాత ఫ్రేమ్ వదిలి వెళ్ళిన విలువ |
| memory address | మెమరీ చిరునామా | మెమరీలో ఒక స్థానం సంఖ్య |
| breakpoint | ఆపు స్థానం | డీబగ్గర్ ప్రోగ్రామ్‌ను ఆపే చోటు |
| backtrace (`bt`) | వెనుకటి జాడ | ఇక్కడికి ఎలా వచ్చామో చూపే ఫ్రేమ్‌ల జాబితా |
| LSD (least significant digit) | చివరి అంకె | సంఖ్యలో కుడి చివరి అంకె (`% 10`) |
