# Pipes and Redirections — Streams in the Shell

A lab built around one idea: **every program has a reading end and a writing
end, and the shell lets you re-plumb them.** The rule for the whole sheet:
**before pressing Enter, say out loud where the input comes from and where the
output goes.**

All commands are for the **Ubuntu shell**. You will also need your compiled
`sum` program (from `sum.c`) — Sub-tasks 5 and 6 are built around it.

## The plumbing map (keep this in front of you)

Every program you run is a box with two hoses attached:

```
                    ┌───────────────┐
  stdin  ─────────► │    program    │ ─────────►  stdout
  (input stream)    └───────────────┘             (output stream)

  DEFAULT: stdin  = your keyboard
  DEFAULT: stdout = your screen
```

Four operators re-connect the hoses **without changing the program at all**:

| Operator | Reads as | What it re-plumbs |
|----------|----------|-------------------|
| `>`  | "send output to file (start fresh)" | stdout → file, **overwrites** |
| `>>` | "send output to file (add at end)"  | stdout → file, **appends** |
| `<`  | "take input from file"              | file → stdin |
| `\|` | "pipe into"                         | stdout of left command → stdin of right command |

The program never knows. `echo`, `head`, your own `sum` — they just read their
stdin and write their stdout; the *shell* decides what those are connected to.

---

# Sub-task 1 — `>` and `>>`: aiming stdout at a file

### a. What we set up

Make a practice area and use `echo` — the simplest program that writes to
stdout:

```
cd ~
mkdir stream_lab
cd stream_lab
```

### b. Task

1. First, stdout in its default position (the screen):
   ```
   echo "hello streams"
   ```
2. Now bend the hose into a file, and look at what reached the screen:
   ```
   echo "line one" > diary.txt
   ```
   *Nothing* printed! Where did it go?
   ```
   more diary.txt
   ```
3. Use `>` again on the same file, then look again:
   ```
   echo "line two" > diary.txt
   more diary.txt
   ```
   **Where is line one?** Read the operator table again.
4. Rebuild the diary with `>>` (append) and check after each command:
   ```
   echo "line one"   > diary.txt
   echo "line two"  >> diary.txt
   echo "line three" >> diary.txt
   more diary.txt
   ```
5. One more experiment: what does `>` do to a file even *before* the command
   writes anything?
   ```
   echo "precious data" > precious.txt
   ls -l precious.txt
   > precious.txt
   ls -l precious.txt
   more precious.txt
   ```

### c. Observation (what you should find)

- With `>`, the screen shows nothing — the *same characters* that would have
  been printed went into the file instead. Output was not copied; it was
  **redirected**.
- `>` **overwrites**: `line two` replaced the whole file, and `line one` is
  gone — permanently. There is no undo.
- `>>` **appends**: the diary grew line by line, nothing lost.
- Step 5 is the scary one: a bare `> precious.txt` (no command at all!)
  emptied the file to **0 bytes**. `>` truncates the file *first*, before any
  output arrives. Respect it.

**Takeaway to say out loud:** `>` = aim stdout at a file, wiping it first;
`>>` = aim stdout at a file, adding at the end. The program prints exactly the
same thing either way — only the destination changed.

---

# Sub-task 2 — A long file, tamed by `more`, `head`, `tail`

### a. What we set up

We need a file too long to read in one screen. Task-5's Mission 2 gives us
one instantly — the list of every command on the machine:

```
ls /usr/bin > commands.txt
```

(That's redirection doing real work already: `ls` printed to stdout, `>`
caught it in a file.)

Three viewers:

- `more commands.txt` — page through (space = next page, `q` = quit)
- `head commands.txt` — only the **first** lines
- `tail commands.txt` — only the **last** lines

### b. Task

1. How big is the catch?
   ```
   wc -l commands.txt
   ```
2. Page through it with `more commands.txt` — a few spaces, then `q`.
3. Peek at the two ends:
   ```
   head commands.txt
   tail commands.txt
   ```
   Count the lines each printed (don't trust — count).
4. Ask for exact amounts, and check `man head` for `-n`:
   ```
   head -n 3 commands.txt
   head -3 commands.txt
   tail -5 commands.txt
   ```
5. Predict, then verify: what will `head -1 commands.txt` and
   `tail -1 commands.txt` show? Which command names on your system are
   alphabetically first and last?

### c. Observation (what you should find)

- `head` and `tail` print **10 lines** by default; `-n N` (or just `-N`)
  changes that.
- `more` doesn't cut anything — it shows *everything*, one screen at a time.
  `head`/`tail` genuinely **select a slice** and ignore the rest.
- All three never modified `commands.txt` — they are readers, not editors.

**Takeaway to say out loud:** `more` = read it all in pages; `head` = the top
slice; `tail` = the bottom slice. Three ways to *consume* a stream that is too
big for one screen.

---

# Sub-task 3 — `<`: aiming stdin at a file (and what stdin is when you don't)

### a. What we set up

`>` bent the *output* hose. `<` bends the *input* hose: the program reads from
a **file** while believing it is reading the keyboard.

### b. Task

1. First feel the default. Run `head` with **no file name at all**:
   ```
   head -2
   ```
   The cursor just sits there — `head` is reading its stdin, which is your
   **keyboard**. Type a few short lines, pressing Enter after each. After the
   2nd line, `head -2` prints its slice and exits.
   Run it once more and this time press **Ctrl-D** (end of input) after one
   line — Ctrl-D is how the keyboard says "the stream is finished."
2. Now feed it a file through stdin instead:
   ```
   head -2 < commands.txt
   ```
3. Compare with the way you used it in Sub-task 2:
   ```
   head -2 commands.txt
   ```
   Same output? Yes — but the mechanism is different. In one, `head` was
   *given a file name* and opened it itself. In the other, `head` was given
   *nothing* and just read stdin, which the **shell** had already connected to
   the file.
4. Both hoses at once — input from one file, output into another:
   ```
   head -2 < commands.txt > first_two.txt
   more first_two.txt
   ```

### c. Observation (what you should find)

- A program that reads stdin **waits silently** when stdin is the keyboard —
  it isn't hanging, it's *listening*. (If you're ever stuck there: Ctrl-D
  ends the stream.)
- `< file` makes the program's reading end come from the file. The program
  can't tell the difference — keyboard and file arrive through the same hose.
- Step 4 shows a full re-plumbing: `head` read a file it never named and
  wrote a file it never named. The command itself (`head -2`) stayed
  untouched in the middle.

**Takeaway to say out loud:** stdin is the reading hose; keyboard is only its
*default* attachment. `<` swaps the keyboard for a file, and the program is
none the wiser.

---

# Sub-task 4 — `|`: pipe one program's stdout into another's stdin

### a. What we set up

The pipe `|` is the star of the shell: it connects the **stdout of the left
command directly to the stdin of the right command** — no file in between.

```
   ls /usr/bin ──stdout──►│ pipe │──stdin──► head -5
```

### b. Task

1. Sub-task 2 needed a temporary file (`commands.txt`). The pipe makes it
   unnecessary — same results, zero files:
   ```
   ls /usr/bin | head -5
   ls /usr/bin | tail -5
   ls /usr/bin | more
   ls /usr/bin | wc -l
   ```
2. `echo` can feed a pipe too:
   ```
   echo "hello pipe" | head -1
   echo -e "a\nb\nc\nd" | tail -2
   ```
   (Remember `-e` from Task-5: `\n` becomes a real newline — so that `echo`
   emits **four lines**.)
3. Chain more than two. Predict first, run second: which lines of
   `commands.txt` does this show?
   ```
   head -20 commands.txt | tail -5
   ```
4. Build a no-file version of the same thing:
   ```
   ls /usr/bin | head -20 | tail -5
   ```

### c. Observation (what you should find)

- Every pipeline result matches what you got via `commands.txt` — the pipe is
  a redirection **with no file**, data flowing left to right while both
  programs run.
- `head -20 | tail -5` is a *window*: first keep the top 20 lines, then keep
  the bottom 5 of those — so you see **lines 16–20**. Two simple slicers
  combine into a tool neither is alone.
- Programs like `head`, `tail`, `more`, `wc` are called **filters** — they
  read stdin, transform, write stdout — *designed* to be pipeline segments.

**Takeaway to say out loud:** `cmd1 | cmd2` plugs cmd1's writing hose into
cmd2's reading hose. Small programs + pipes = big abilities; that is the
Unix design in one line.

---

# Sub-task 5 — stdin and stdout inside a C program: your `sum`

### a. What we set up

Everything above applies *unchanged* to programs **you** write, because
`printf` and `scanf` are already stream functions:

- **`printf(...)` writes to stdout.**
- **`scanf(...)` reads from stdin.**

The C library gives every program the two open streams `stdin` and `stdout`
the moment `main` starts; `printf`/`scanf` are just convenient ways to use
them. Your program never mentions the keyboard or the screen — it only ever
talks to the two hoses, and the shell decides what's attached.

Make sure you have this program (your `sum.c`) compiled in `~/stream_lab`:

```c
#include <stdio.h>

int main()
{
    int number1, number2, sum;

    printf("To add two numbers.\n");
    printf("Enter the first number: ");
    scanf("%i", &number1);
    printf("Enter the second number: ");
    scanf("%i", &number2);
    sum = number1 + number2;
    printf("The sum of %i and %i is %i.\n", number1, number2, sum);

    return 0;
}
```

```
clang sum.c -o sum
```

### b. Task

1. Run it the normal way and type `3`, then `4`:
   ```
   ./sum
   ```
   Copy the screen into your notebook, then mark **every character** as one
   of: (P) printed by the program, (K) typed by you on the keyboard.
2. Now feed the numbers through a pipe — nothing typed at all:
   ```
   echo -e "3\n4" | ./sum
   ```
   Look hard at the output. The prompts run together
   (`Enter the first number: Enter the second number: ...`) — and the `3` and
   `4` are **nowhere on the screen**. Why? Hold the question.
3. Same, but from a file. Create the input file — it contains **only what you
   would have typed**, nothing else:
   ```
   echo -e "3\n4" > input.txt
   more input.txt
   ./sum < input.txt
   ```
4. Both hoses re-plumbed — and now the screen shows *nothing*:
   ```
   ./sum < input.txt > output.txt
   more output.txt
   ```

### c. Observation (what you should find)

- Your notebook marking of the interactive run should look like this — the
  screen is **two streams interleaved**:
  ```
   To add two numbers.              ← P  (program's stdout)
   Enter the first number: 3        ← P up to the colon+space, K for the "3"
   Enter the second number: 4       ← P ... K
   The sum of 3 and 4 is 7.         ← P
  ```
  The `3` and `4` you "see" during a normal run are **not program output** —
  they are the terminal echoing your keystrokes.
- That solves step 2's puzzle: when stdin comes from a pipe or file, **there
  are no keystrokes to echo**, so the numbers vanish from the screen and the
  two prompts (which end with no newline) butt up against each other. The
  program behaved *identically* — only the keyboard echo disappeared.
- `input.txt` holds exactly `3` and `4` on two lines. **The prompts do NOT
  belong in the input file** — `Enter the first number:` is *output* (stdout),
  not input. The input stream contains only what travels *into* `scanf`.
- In step 4, `output.txt` holds everything `printf` produced:
  ```
  To add two numbers.
  Enter the first number: Enter the second number: The sum of 3 and 4 is 7.
  ```
  Prompts included — because prompts are stdout, and stdout went to the file.
- `scanf` never noticed any of this. Keyboard, pipe, file — one reading hose.

**Takeaway to say out loud:** in C, `printf` → stdout and `scanf` → stdin —
the program only knows its two streams. What you see during an interactive
run is stdout *mixed with* the echo of your own typing; redirection separates
them cleanly.

---

# Sub-task 6 — Testing `sum` with files: input, expected output, actual output

### a. What we set up

Sub-task 5 gives us a superpower: we can run `./sum` **without a human**. That
is the foundation of automated testing. One test case = three files:

```
 input.txt             what the user WOULD have typed        (goes to stdin)
 expected_output.txt   what the program SHOULD print          (correct stdout)
 output.txt            what the program ACTUALLY printed      (captured stdout)
```

```
                          ┌────────┐
 input.txt ──── < ──────► │ ./sum  │ ──── > ────► output.txt
                          └────────┘
                                                  output.txt  ==  expected_output.txt ?
                                                       │
                                             same → TEST PASSED
                                             different → BUG (or bad test)
```

The comparer is `diff file1 file2`: it prints the differences — and prints
**nothing at all** when the files are identical. Silence = pass.

### b. Task — build TWO test cases

**Test case 1: 3 + 4**

1. The input file (only the keystrokes!):
   ```
   echo -e "3\n4" > input1.txt
   ```
2. The expected output. Run the program once, *read the result with your own
   eyes*, and only if it is correct, bless it as the expectation:
   ```
   ./sum < input1.txt
   ```
   Read it. Is `The sum of 3 and 4 is 7.` right? Yes → capture that same run:
   ```
   ./sum < input1.txt > expected_output1.txt
   more expected_output1.txt
   ```
3. Now pretend time has passed and you want to re-check the program:
   ```
   ./sum < input1.txt > output1.txt
   diff expected_output1.txt output1.txt
   ```
   Silence — test 1 passes.

**Test case 2: 10 + 32**

4. Do the whole cycle yourself:
   ```
   echo -e "10\n32" > input2.txt
   ./sum < input2.txt                              # eyeball: is 42 right?
   ./sum < input2.txt > expected_output2.txt
   ./sum < input2.txt > output2.txt
   diff expected_output2.txt output2.txt
   ```

**Watch a test FAIL (the most important step)**

5. Sabotage the program — in `sum.c`, change `number1 + number2` to
   `number1 - number2`, recompile (`clang sum.c -o sum`), and re-run **only**
   the test commands:
   ```
   ./sum < input1.txt > output1.txt
   diff expected_output1.txt output1.txt
   ```
   `diff` now *speaks*: it shows the expected line (`...is 7.`) against the
   actual line (`...is -1.`). Your files caught the bug — no human typed a
   thing.
6. Fix `sum.c` back, recompile, re-run both tests, get silence again.
7. Bonus (uses Task-5's script skills) — put the whole checkup in
   `run_tests.sh`:
   ```bash
   #!/bin/bash
   ./sum < input1.txt > output1.txt
   diff expected_output1.txt output1.txt
   ./sum < input2.txt > output2.txt
   diff expected_output2.txt output2.txt
   echo "If nothing printed above this line: ALL TESTS PASSED"
   ```
   `chmod +x run_tests.sh`, then `./run_tests.sh` after *every* change to
   `sum.c`.

### c. Observation (what you should find)

- The three files split testing into three clean roles: **input** (frozen
  keystrokes), **expected output** (the blessed correct answer), **actual
  output** (regenerated on every run). Only `output*.txt` ever changes.
- Expected-output files were *born from a verified run* — the eyeball check in
  step 2 is the one moment a human vouches for correctness; after that, `diff`
  does the vouching forever.
- The sabotage run shows the whole value: a wrong program **cannot pass** —
  `diff` compares every character, including ones your eye would skim past.
- Never type into a test. If a test needs a human, it will be run rarely;
  if it's `./run_tests.sh`, it will be run always.

**Takeaway to say out loud:** `< input.txt` replaces the typing human,
`> output.txt` replaces the watching human, `diff` replaces the judging
human. That trio — input, expected output, actual output — is how real
software is tested, and you just built it from three shell operators.

---

# Sub-task 7 — `test_sum.sh`: one script that compiles, runs, and judges

### a. What we set up

Sub-task 6 still needed a human to *read* diff's output. We remove that last
human job using something every command has been quietly giving you all along:
an **exit status**.

Every command, when it finishes, hands the shell a number:

- **`0` = success** ("all fine")
- **non-zero = something to report** (for `diff`: `1` = the files differ)

The shell keeps the *last* command's exit status in the special variable
`$?`. And bash has an `if` — just like C, but with its own spelling:

```bash
if [ $? -eq 0 ]
then
    echo "the previous command succeeded"
else
    echo "the previous command failed"
fi
```

Read `[ $? -eq 0 ]` as: "is the last exit status **eq**ual to 0?" Two rules of
bash grammar: the **spaces around the brackets are required**, and the block
ends with `fi` (`if` spelled backwards) instead of `}`.

Careful — this is *opposite* to C's habit: in C, `0` means false; in the
shell, **`0` means success**. There is one success but many different
failures, so success got the one special number.

### b. Task

1. First *feel* exit statuses by hand, before scripting them:
   ```
   ls
   echo $?
   ls no_such_file
   echo $?
   diff expected_output1.txt output1.txt
   echo $?
   ```
   (Run the `diff` once when the files match and — after a sabotage — once
   when they don't. Watch `0` become `1`.)
2. Write the full test runner. Create `test_sum.sh` in `~/stream_lab`:
   ```bash
   #!/bin/bash
   # test_sum.sh — compile sum.c, run both test cases, judge with diff

   clang sum.c -o sum
   if [ $? -ne 0 ]
   then
       echo "COMPILATION FAILED — tests not run"
       exit 1
   fi

   ./sum < input1.txt > output1.txt
   diff expected_output1.txt output1.txt
   if [ $? -eq 0 ]
   then
       echo "TEST 1 PASSED"
   else
       echo "TEST 1 FAILED"
   fi

   ./sum < input2.txt > output2.txt
   diff expected_output2.txt output2.txt
   if [ $? -eq 0 ]
   then
       echo "TEST 2 PASSED"
   else
       echo "TEST 2 FAILED"
   fi
   ```
   Read it top to bottom and say what each block does: **compile** (with a
   guard — `-ne` means *not equal*, and `exit 1` abandons the script, itself
   reporting failure), then per test case: **run** (`<` feeds the input file,
   `>` catches the output), **compare** (`diff`), **judge** (`if` on `$?`).
3. Make it runnable and run it (Task-5 skills):
   ```
   chmod +x test_sum.sh
   ./test_sum.sh
   ```
   Expect two green lines: `TEST 1 PASSED`, `TEST 2 PASSED`.
4. Sabotage `sum.c` again (`+` → `-`) — and *only* run the script:
   ```
   ./test_sum.sh
   ```
   No manual recompile needed — the script compiles for you. Read the output:
   diff's complaints followed by `TEST 1 FAILED`, `TEST 2 FAILED`.
5. Break the *C code's grammar* instead — remove a semicolon in `sum.c` and
   run `./test_sum.sh`. Watch the compile guard fire: clang's error, then
   `COMPILATION FAILED — tests not run`, and **no** test lines at all.
6. Repair `sum.c` fully, run `./test_sum.sh` one last time, and enjoy the two
   PASSED lines.

### c. Observation (what you should find)

- Exit statuses were always there — `echo $?` just made them visible. `ls` of
  a real folder gives `0`; of a missing file gives non-zero; `diff` gives `0`
  for identical files and `1` for different ones. **`man diff`** documents
  this under EXIT STATUS — most man pages have that section; start noticing it.
- The `if` turned diff's *number* into a human verdict. Nobody has to read
  diff output to know the result anymore — the script prints `PASSED` or
  `FAILED` in plain words.
- Because the script **recompiles every time**, you can never accidentally
  test a stale executable — the exact trap from Task-6's Q2 is designed away.
- Step 5 shows why the compile guard matters: with a broken compile there is
  no fresh `sum`, and without the guard the script would happily "test"
  whatever old `sum` was lying around — Q2's ghost again. `exit 1` stops the
  lie before it starts.
- One command — `./test_sum.sh` — now does compile, run, capture, compare,
  judge. This is a real **test runner**, the same shape as the ones used on
  million-line codebases.

**Takeaway to say out loud:** every command reports a number; `0` means
success; `$?` reads it; `if [ $? -eq 0 ]` acts on it. Chain compile → run →
`diff` → `if`, and the entire testing ceremony collapses into one word:
`./test_sum.sh`.

---

## One-page reference

| Goal | Command |
|------|---------|
| stdout → file (overwrite!) | `command > file` |
| stdout → file (append) | `command >> file` |
| file → stdin | `command < file` |
| stdout of A → stdin of B | `A \| B` |
| Both hoses at once | `command < in.txt > out.txt` |
| Page through a long file | `more file` (space, `q`) |
| First N lines | `head -N file` (default 10) |
| Last N lines | `tail -N file` (default 10) |
| Lines 16–20 of a stream | `head -20 \| tail -5` |
| End keyboard input (EOF) | **Ctrl-D** |
| Compare two files (silence = same) | `diff file1 file2` |
| Run one test on `sum` | `./sum < input1.txt > output1.txt` then `diff expected_output1.txt output1.txt` |
| See the last command's exit status | `echo $?` (0 = success) |
| Branch on it in a script | `if [ $? -eq 0 ]` … `then` … `else` … `fi` |
| Run all tests with one word | `./test_sum.sh` |

**The picture to remember:**

```
 keyboard ──┐                       ┌── screen
            ├──► stdin ─ program ─ stdout ──┤
 < file  ───┘        ▲          │           ├──► > file   (wipe & write)
                     │          │           └──► >> file  (append)
            pipe ────┘          └──── pipe
```

**In C:** `scanf` reads the stdin hose, `printf` writes the stdout hose — the
program never knows what the shell attached to either end.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English word | తెలుగు అర్థం |
|--------------|--------------|
| **stream** | ప్రవాహం — అక్షరాలు వరుసగా ప్రవహించే దారి; ప్రతి ప్రోగ్రామ్‌కు చదివే ప్రవాహం, రాసే ప్రవాహం ఉంటాయి. |
| **stdin (standard input)** | ప్రామాణిక ఇన్‌పుట్ — ప్రోగ్రామ్ చదివే గొట్టం; సాధారణంగా కీబోర్డ్, కానీ `<` తో ఫైల్ కూడా కావచ్చు. |
| **stdout (standard output)** | ప్రామాణిక అవుట్‌పుట్ — ప్రోగ్రామ్ రాసే గొట్టం; సాధారణంగా తెర, కానీ `>` తో ఫైల్ కావచ్చు. |
| **redirection** | దారి మళ్ళింపు — ప్రోగ్రామ్ మారకుండా, దాని ఇన్‌పుట్/అవుట్‌పుట్ గొట్టాలను వేరే చోటికి తిప్పడం (`>`, `>>`, `<`). |
| **overwrite / truncate** | పైన రాయడం / ఖాళీ చేయడం — `>` ఫైల్‌ను ముందుగా పూర్తిగా ఖాళీ చేసి, కొత్తది రాస్తుంది; పాతది పోతుంది. |
| **append** | చివర చేర్చడం — `>>` పాతది చెరపకుండా, ఫైల్ చివర కొత్త పంక్తులు కలుపుతుంది. |
| **pipe** (`\|`) | గొట్టం — ఎడమ కమాండ్ యొక్క stdout ను కుడి కమాండ్ యొక్క stdin కు నేరుగా కలిపే కలుపుదల; మధ్యలో ఫైల్ అవసరం లేదు. |
| **filter** | వడపోత — stdin చదివి, మార్చి, stdout కు రాసే చిన్న ప్రోగ్రామ్ (`head`, `tail`, `wc`); పైప్‌లైన్ ముక్కలుగా పనిచేయడానికే పుట్టాయి. |
| **EOF / end of input** (Ctrl-D) | ప్రవాహం ముగింపు — "ఇక ఇన్‌పుట్ లేదు" అని చెప్పే సంకేతం; కీబోర్డ్ నుండి Ctrl-D తో పంపుతాం. |
| **keyboard echo** | కీబోర్డ్ ప్రతిధ్వని — మీరు నొక్కిన అక్షరాలను టెర్మినల్ తెరపై చూపించడం; ఇది ప్రోగ్రామ్ అవుట్‌పుట్ **కాదు**. |
| **interleave** | పెనవేయడం — రెండు ప్రవాహాలు (ప్రోగ్రామ్ stdout + మీ టైపింగ్ echo) తెరపై కలిసిపోయి కనిపించడం. |
| **prompt (in a program)** | అడిగే వాక్యం — `Enter the first number:` లాంటి పంక్తి; ఇది **అవుట్‌పుట్** ప్రవాహానికి చెందుతుంది, ఇన్‌పుట్ ఫైల్‌లో ఉండకూడదు. |
| **test case** | పరీక్షా సందర్భం — ఒక నిర్దిష్ట ఇన్‌పుట్ + దానికి రావాల్సిన సరైన అవుట్‌పుట్ జత. |
| **input file** | ఇన్‌పుట్ ఫైల్ — మనిషి టైప్ చేయాల్సినవి మాత్రమే ఉండే ఫైల్; `<` ద్వారా ప్రోగ్రామ్‌కు అందుతుంది. |
| **expected output** | ఆశించిన అవుట్‌పుట్ — సరైనదని మనిషి ఒకసారి ధృవీకరించి దాచిన ఫలితం; ఇకపై పోలికకు ప్రమాణం. |
| **actual output** | వాస్తవ అవుట్‌పుట్ — ప్రతి పరీక్షలో ప్రోగ్రామ్ నిజంగా ఇచ్చిన ఫలితం (`output.txt`); ప్రతిసారీ కొత్తగా తయారవుతుంది. |
| **diff** | తేడా చూపేది — రెండు ఫైళ్ళను అక్షరం-అక్షరం పోల్చే కమాండ్; ఒకేలా ఉంటే *ఏమీ* ముద్రించదు — నిశ్శబ్దం = పరీక్ష గెలిచింది. |
| **automated testing** | స్వయంచాలక పరీక్ష — మనిషి టైప్ చేయకుండా, చూడకుండా, తీర్పు చెప్పకుండా — ఫైళ్ళు, `diff` ద్వారా ప్రోగ్రామ్‌ను పరీక్షించడం. |
| **sabotage (a test)** | కావాలని చెడగొట్టడం — పరీక్ష నిజంగా పట్టుకుంటుందో చూడడానికి ప్రోగ్రామ్‌లో కావాలని తప్పు పెట్టడం. |
| **exit status** | నిష్క్రమణ స్థితి — ప్రతి కమాండ్ ముగిసేటప్పుడు shell కు ఇచ్చే సంఖ్య; `0` = విజయం, సున్నా-కానిది = ఏదో లోపం. |
| **`$?`** | చివరి ఫలిత సంఖ్య — ఇప్పుడే నడిచిన కమాండ్ యొక్క exit status ను దాచి ఉంచే ప్రత్యేక చరరాశి; `echo $?` తో చూస్తాం. |
| **`if` condition (bash)** | షరతు (bash లో) — C లోని `if` లాగే, కానీ `then` తో మొదలై `fi` తో ముగుస్తుంది; `[ ]` లోపల ఖాళీలు తప్పనిసరి. |
| **`-eq` / `-ne`** | సమానం / సమానం-కాదు — bash షరతుల్లో సంఖ్యలను పోల్చే గుర్తులు (**eq**ual, **n**ot **e**qual). |
| **`exit`** | నిష్క్రమించు — స్క్రిప్ట్‌ను అక్కడికక్కడే ఆపే కమాండ్; `exit 1` అంటే "విఫలమై బయటకు" అని అర్థం. |
| **guard** | కాపలా షరతు — ముందుకు వెళ్ళడం అర్థవంతం కానప్పుడు (ఉదా: కంపైల్ విఫలమైతే) పనిని ఆపే ప్రారంభ తనిఖీ. |
| **test runner** | పరీక్షల నడిపేది — కంపైల్ → నడపడం → పోల్చడం → తీర్పు; అన్నీ ఒకే కమాండ్‌తో చేసే స్క్రిప్ట్ (`./test_sum.sh`). |
