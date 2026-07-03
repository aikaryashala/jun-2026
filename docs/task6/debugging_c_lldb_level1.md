# Debugging C Code Using Clang Debugger (LLDB) — Level-1

A lab built around *watching your program run one line at a time*. The rule for
the whole sheet: **don't guess what the code does — stop it, step it, and look
at the variables.**

All commands are for the **Ubuntu shell**. The debugger we use is **LLDB**, the
debugger that comes from the same project as `clang`.

## What is debugging?

When a program gives a wrong answer, the bug is somewhere in the lines you
wrote — but *which* line? Reading the code again and again is guessing.
A **debugger** removes the guessing: it runs your program in slow motion,
**pausing before every line**, and lets you ask *"what is inside each variable
right now?"*

Think of it like watching a cricket replay frame by frame instead of at full
speed — you see exactly the moment things went wrong.

## The debug loop map (keep this in front of you)

```
 write main.c
   │
   ▼
 clang -g main.c -o main      ← compile WITH the -g flag (debug info)
   │
   ▼
 lldb ./main                  ← start the debugger, program NOT running yet
   │
   ▼
 b main                       ← set a breakpoint at the start of main
   │
   ▼
 run                          ← program starts, then FREEZES at main
   │
   ▼
 next  →  next  →  next ...   ← execute ONE line at a time (step-over)
   │        (after every next: frame variable — look at the values)
   ▼
 program ends → quit          ← leave the debugger
```

One rule for this whole task: we only use **`next`** (step-over). LLDB also has
`step` (step-in) and `finish` (step-out) — those only matter once your programs
have *functions other than `main`*. We will meet them in **Level-2**, after you
learn to write your own functions.

## Setup (one time)

Check that both tools exist:

```
clang --version
lldb --version
```

If either is missing, install them:

```
sudo apt update
sudo apt install clang lldb
```

---

# Concept 1 — The `-g` flag: giving the debugger a map

### a. What we set up / save in a file

A compiled executable is machine code — it has no idea what `int num1` or
"line 7" means. The `-g` flag tells clang to pack **debug information** into
the executable: which machine instruction came from which source line, and
where each variable lives. Without `-g`, LLDB is blind.

Create this file:

```c
/* biggest.c */
#include <stdio.h>

int main()
{
    int num1 = 7;
    int num2 = 12;
    int big = num1;

    if (num2 > big)
    {
        big = num2;
    }

    printf("biggest = %d\n", big);
    return 0;
}
```

Compile it **twice** — once without `-g`, once with:

```
clang biggest.c -o biggest_blind
clang -g biggest.c -o biggest
```

### b. Task

1. Compare the sizes of the two executables:
   ```
   ls -l biggest_blind biggest
   ```
2. Ask `file` what it thinks of each one:
   ```
   file biggest_blind biggest
   ```
   **Note:** `file` determines a file's type. Check its man page using
   `man file`.
3. Run both programs normally — confirm they behave *identically*:
   ```
   ./biggest_blind
   ./biggest
   ```

### c. Observation (what you should find)

- `biggest` (built with `-g`) is **bigger** than `biggest_blind` — the extra
  bytes are the debug information, the "map" from machine code back to your
  `.c` lines and variable names.
- `file` reports the `-g` version as `with debug_info, not stripped`.
- Both print `biggest = 12`. Debug info changes **nothing** about what the
  program does — it is baggage for the debugger, invisible during a normal run.

**Takeaway to say out loud:** `-g` doesn't change the program; it packs a map
into the executable so the debugger can point at *your* source lines and *your*
variable names. **Always compile with `-g` before debugging.**

---

# Concept 2 — Starting LLDB and freezing the program at `main`

### a. What we set up / save in a file

Start the debugger with your program:

```
lldb ./biggest
```

The shell prompt changes to `(lldb)` — you are now talking to the debugger,
not the shell. **Important: your program has not started yet.** LLDB has only
loaded it, like an arrow placed on the bow but not released.

A **breakpoint** is a marker meaning *"when the running program reaches this
place, freeze."* We put one at the start of `main`:

```
(lldb) breakpoint set --name main
```

Everyone uses the short form:

```
(lldb) b main
```

### b. Task

1. Start LLDB with `./biggest` and set a breakpoint at `main` using `b main`.
2. Confirm the breakpoint exists:
   ```
   (lldb) breakpoint list
   ```
3. Now release the arrow:
   ```
   (lldb) run
   ```
4. Read the screen slowly. Find: the reason it stopped, the file name, the
   line number, and the small arrow `->` in the source listing.
5. Quit and start again — practice the cycle once more:
   ```
   (lldb) quit
   ```
   (LLDB may ask *"Do you really want to proceed"* if the program is still
   frozen mid-run — answer `Y`.)

### c. Observation (what you should find)

- After `run`, LLDB prints something like:
  ```
  Process 4821 stopped
  * thread #1, name = 'biggest', stop reason = breakpoint 1.1
      frame #0: ... main at biggest.c:6
     3    int main()
     4    {
  -> 6        int num1 = 7;
     7        int num2 = 12;
  ```
- `stop reason = breakpoint 1.1` — it froze because of *your* breakpoint,
  not a crash.
- The arrow `->` points at `int num1 = 7;`. Read this carefully: the arrow marks
  the line that is **about to run, and has NOT run yet**. The program is frozen
  *before* line 6, not after it.

**Takeaway to say out loud:** `lldb ./program` loads but does not start;
`b main` plants the freeze-point; `run` starts the program, which immediately
freezes at the first line of `main`. The `->` arrow always means "next line to
execute."

---

# Concept 3 — `next` and `frame variable`: one line at a time, eyes on the variables

### a. What we set up / save in a file

Two commands do almost all Level-1 debugging:

| Command | Short | Meaning |
|---------|-------|---------|
| `next` | `n` | Execute exactly the ONE line at the arrow, then freeze again (**step-over**) |
| `frame variable` | `v` | Show **every local variable** and its current value |
| `print big` | `p big` | Show **one** variable's current value |

The rhythm of debugging is a loop you do with your hands:

```
look at the arrow  →  frame variable  →  next  →  (repeat)
```

We keep using `biggest.c` from Concept 1 — it has an `if`.

### b. Task

1. Start fresh: `lldb ./biggest`, then `b main`, then `run`.
2. Before pressing anything else, look at the variables:
   ```
   (lldb) frame variable
   ```
   Write down the values of `num1`, `num2`, `big` — yes, *before* any line has run.
3. Now do the rhythm. After **every** `next`, run `frame variable` and note
   which value changed:
   ```
   (lldb) next
   (lldb) frame variable
   ```
   **Note:** pressing plain **Enter** at the `(lldb)` prompt repeats the
   previous command — so you can hammer Enter to keep stepping.
4. When the arrow reaches the `if (num2 > big)` line, **stop and predict**:
   will the arrow jump *into* the `{ }` block or *over* it? Then press `next`
   once and check.
5. Keep stepping until the program prints `biggest = 12` and exits. Then `quit`.

### c. Observation (what you should find)

- In step 2, **before** `int num1 = 7;` has run, `frame variable` shows `num1`,
  `num2`, `big` holding **garbage** — random leftover numbers like `32767` or
  `-1899491328`. A variable has no meaningful value until its line *executes*.
  You are looking at raw memory as it happened to be.
- Each `next` changes exactly **one** thing: after stepping over line 6,
  `num1` becomes `7` (while `num2`, `big` are still garbage); after line 7,
  `num2` becomes `12`; after line 8, `big` becomes `7`.
- At the `if`: since `num2 (12) > big (7)` is true, the arrow moves **into** the
  block, onto `big = num2;`. One more `next` and `frame variable` shows `big`
  jumping from `7` to `12`. You just *watched* an `if` decide.
- Change the experiment: edit `int num2 = 3;`, recompile with `-g`, and step
  again — this time the arrow **skips the block entirely** and lands straight
  on the `printf` line. The `if` chose the other path, and you saw it.
- On the `printf` line, `next` runs the *whole* printf (that's why it is called
  step-**over**) and `biggest = 12` appears in the middle of your LLDB session.

**Takeaway to say out loud:** `next` executes exactly one of *your* lines;
`frame variable` is the X-ray you take after every step. Watching the arrow at
an `if` shows you the *actual decision*, not the one you assumed.

---

# Concept 4 — Tracing a `while` loop: watch the variables cycle

### a. What we set up / save in a file

Loops are where eyes-only debugging fails — the same lines run again and again
with *different* values. The debugger shows every lap. Create this file:

```c
/* countdown.c */
#include <stdio.h>

int main()
{
    int count = 5;
    int total = 0;

    while (count > 0)
    {
        total = total + count;
        count = count - 1;
    }

    printf("total = %d\n", total);
    return 0;
}
```

Compile with debug info and load it:

```
clang -g countdown.c -o countdown
lldb ./countdown
```

### b. Task

1. `b main`, then `run`.
2. Step with `next` until the arrow first reaches `while (count > 0)`. Check
   `frame variable` — write down `count` and `total`.
3. Keep the rhythm going (`next`, then `frame variable`) around the loop.
   On paper, fill a **trace table** — one row every time the arrow lands on
   the `while` line:

   | lap | `count` when at `while` | `total` when at `while` | will it enter the loop? |
   |-----|-------------------------|--------------------------|-------------------------|
   | 1   | 5                       | 0                        | yes / no                |
   | 2   |                         |                          |                         |
   | 3   |                         |                          |                         |
   | ... |                         |                          |                         |

4. **Before each lap**, predict the two values, *then* look. Prediction first,
   `frame variable` second.
5. The important moment: the lap where `count` is `0`. Predict where the arrow
   will go when you press `next` on the `while` line. Then look.
6. If you stepped too far and just want the program to finish normally:
   ```
   (lldb) continue
   ```
   **Note:** `continue` (short `c`) means "stop stepping — run at full speed
   until the next breakpoint or the end."
7. Type `help next` at the `(lldb)` prompt and skim what it says. Every LLDB
   command documents itself this way.

### c. Observation (what you should find)

- The arrow cycles: `while` → `total = total + count;` → `count = count - 1;`
  → back to `while`. The *same three lines*, visited again and again — a loop
  made visible.
- Your trace table fills up as: `count = 5, total = 0` → `count = 4, total = 5`
  → `count = 3, total = 9` → `count = 2, total = 12` → `count = 1, total = 14`
  → `count = 0, total = 15`.
- On the lap where `count` is `0`, the condition `count > 0` is false — `next` makes
  the arrow **jump past the `}` straight to the `printf` line**. That jump *is*
  the loop ending; you watched the exact moment.
- `continue` lets the rest of the program fly by at full speed —
  `total = 15` prints and the process exits.

**Takeaway to say out loud:** a loop is just the arrow travelling in a circle
while the variables change each lap. The trace table you filled by hand *is*
what the debugger shows for free — and when a loop misbehaves (runs forever,
runs one time too many), this is exactly how you catch it.

---

## One-page command reference

| Goal                                   | Command                        | Short |
|----------------------------------------|--------------------------------|-------|
| Compile with debug info                | `clang -g file.c -o file`      | —     |
| Start the debugger                     | `lldb ./file`                  | —     |
| Set breakpoint at start of `main`      | `breakpoint set --name main`   | `b main` |
| List all breakpoints                   | `breakpoint list`              | `br list` |
| Start the program (freezes at breakpoint) | `run`                       | `r`   |
| Execute one line, step-over            | `next`                         | `n`   |
| Show all local variables               | `frame variable`               | `v`   |
| Show one variable                      | `print total`                  | `p total` |
| Run at full speed until next stop      | `continue`                     | `c`   |
| Repeat the previous command            | *(just press Enter)*           | —     |
| Help on any command                    | `help next`                    | —     |
| Leave the debugger                     | `quit`                         | `q`   |

**The Level-1 rhythm to remember:** `b main` → `run` → (`next` → `frame variable`) × many → `continue` → `quit`.

**Coming in Level-2** (after you learn to write functions): `step` (step-in)
and `finish` (step-out) — how to dive into a function call and climb back out.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English word | తెలుగు అర్థం |
|--------------|--------------|
| **debugging** | దోష నివారణ — ప్రోగ్రామ్‌లో దాగిన తప్పు (bug)ను వెతికి, సరిచేసే ప్రక్రియ. |
| **debugger** | డీబగ్గర్ — ప్రోగ్రామ్‌ను నెమ్మదిగా, పంక్తి-పంక్తిగా నడిపిస్తూ లోపలి విలువలను చూపించే సాధనం (ఇక్కడ LLDB). |
| **LLDB** | ఎల్‌ఎల్‌డీబీ — clang తో పాటు వచ్చే LLVM ప్రాజెక్ట్ యొక్క డీబగ్గర్. |
| **debug information** (`-g`) | డీబగ్ సమాచారం — ఏ యంత్ర సూచన ఏ C పంక్తి నుండి వచ్చిందో, ఏ చరరాశి ఎక్కడ ఉందో చెప్పే "మ్యాప్"; `-g` ఫ్లాగ్ దీన్ని executable లో చేరుస్తుంది. |
| **breakpoint** | విరామ బిందువు — "ప్రోగ్రామ్ ఇక్కడికి చేరగానే ఆగిపో" అని పెట్టే గుర్తు. |
| **run** | నడిపించు — ఆగి ఉన్న ప్రోగ్రామ్‌ను డీబగ్గర్ లోపల మొదలుపెట్టడం. |
| **freeze / pause** | స్తంభింపజేయడం — ప్రోగ్రామ్‌ను చంపకుండా, ఉన్నచోటే ఆపి ఉంచడం. |
| **step-over** (`next`) | పంక్తి దాటు — బాణం గుర్తు ఉన్న ఒక్క పంక్తిని మాత్రమే నడిపి, మళ్ళీ ఆగడం. |
| **step-in / step-out** | లోపలికి అడుగు / బయటికి అడుగు — ఫంక్షన్ లోపలికి వెళ్ళడం / బయటికి రావడం. (ఇవి Level-2 లో నేర్చుకుంటాం.) |
| **frame variable** | చరరాశుల పట్టిక — ప్రస్తుతం ఉన్న అన్ని local చరరాశులు, వాటి విలువలు చూపించే ఆదేశం. |
| **variable** | చరరాశి — విలువను దాచుకునే పేరు గల పెట్టె (ఉదా: `count`, `total`). |
| **value** | విలువ — చరరాశిలో ప్రస్తుతం ఉన్న సంఖ్య/సమాచారం. |
| **garbage value** | చెత్త విలువ — చరరాశికి విలువ ఇచ్చే పంక్తి నడవక *ముందు* అందులో కనిపించే అర్థంలేని, యాదృచ్ఛిక సంఖ్య. |
| **trace / tracing** | జాడ పట్టడం — ప్రోగ్రామ్ ఏ పంక్తి తర్వాత ఏ పంక్తి నడిచిందో, విలువలు ఎలా మారాయో అనుసరించడం. |
| **trace table** | జాడ పట్టిక — ప్రతి మలుపు (lap) వద్ద చరరాశుల విలువలను రాసుకునే పట్టిక. |
| **condition** | షరతు — `if`/`while` లో నిజమా, అబద్ధమా అని తేల్చే ప్రశ్న (ఉదా: `count > 0`). |
| **loop / lap** | మలుపు / చుట్టు — `while` బ్లాక్ లోని పంక్తులు ఒకసారి పూర్తిగా నడవడం. |
| **prompt** | ప్రాంప్ట్ — ఆదేశం కోసం ఎదురుచూసే గుర్తు (`(lldb)` అనేది డీబగ్గర్ ప్రాంప్ట్; shell ప్రాంప్ట్ వేరు). |
| **process** | ప్రక్రియ — నడుస్తున్న ప్రోగ్రామ్; LLDB `Process 4821 stopped` అని దీన్నే చూపిస్తుంది. |
| **stop reason** | ఆగిన కారణం — ప్రోగ్రామ్ ఎందుకు స్తంభించిందో LLDB చెప్పే వివరణ (ఉదా: breakpoint వల్ల). |
| **continue** | కొనసాగించు — పంక్తి-పంక్తి ఆపడం మాని, పూర్తి వేగంతో ముందుకు నడిపించడం. |
| **executable** | నిర్వహించదగిన ఫైల్ — నేరుగా నడిపించగల తుది ప్రోగ్రామ్ (ఉదా: `./biggest`). |
| **predict** | అంచనా వేయడం — చూడక ముందే "ఇలా జరుగుతుంది" అని ఊహించి రాయడం; తర్వాత డీబగ్గర్‌తో సరిచూసుకోవడం. |
