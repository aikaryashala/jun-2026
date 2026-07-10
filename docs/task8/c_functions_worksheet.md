# Functions in C — Declare, Define, Call

A lab built around **growing one program in small iterations** — from a lone
`main` to a clean multi-file project — and then **breaking it on purpose** to
meet the three kinds of errors. The rule for the whole sheet: **compile and
run after every iteration; read every message the tools print.**

All commands are for the **Ubuntu shell**, compiled with `clang`.

## The function map (keep this in front of you)

A function shows up in your code in **three roles** — learn to name them on
sight:

```
 declaration   int sum_of_digits(int number);          ← the PROMISE
               "a function with this name, taking an int, returning an int, exists"

 definition    int sum_of_digits(int number)           ← the WORK
               {
                   ...the body...
               }

 call          total = sum_of_digits(45);              ← the USE
```

One more secret hiding in plain sight: **`main` is itself a function** — 
`int main()` takes nothing, returns an `int` (`return 0;`). You have been
writing functions since day one; today you write your *second* one.

Our two functions for the whole sheet:

- `sum_of_digits` — sum of the digits of a **two-digit** number (45 → 9)
- `reverse_of_number` — reverse of a **two-digit** number (45 → 54)

Both carry this comment, and it matters:
`/* Assumption: only two-digit numbers (10 to 99) are given. */`

---

# Iteration 1 — Just `main`

### a. What we set up

Make a practice area and start with the smallest complete program — one
file, one function:

```
cd ~
mkdir func_lab
cd func_lab
```

Create `digits.c`:

```c
/* digits.c */
#include <stdio.h>

int main()
{
    int number = 45;

    printf("The number is %d.\n", number);
    return 0;
}
```

### b. Task

1. Compile and run:
   ```
   clang digits.c -o digits
   ./digits
   ```
2. Point at the three parts of `int main()` and say what each is: the return
   type (`int`), the name (`main`), the parameter list (`()` — empty).
3. Find the `return 0;` — which function is returning, and to whom?
   (Task-7 knows: `echo $?` after `./digits`.)

### c. Observation (what you should find)

- The program prints `The number is 45.` — baseline established.
- `main` already has the full function shape: return type, name, parameters,
  body in `{ }`, a `return`.
- `echo $?` prints `0` — `main`'s return value *is* the exit status the
  shell receives. A function's returned value always goes *somewhere*.

**Takeaway to say out loud:** a C program is functions all the way down —
and I've already been writing one: `main`.

---

# Iteration 2 — One new function, three places

### a. What we set up

Add `sum_of_digits`. The three roles go to three places in the file:
**declaration above `main`**, **definition below `main`**, **call inside
`main`**.

Two `int` tools do all the digit work:

- `number / 10` — integer division **throws away the remainder**: `45 / 10` is `4` (the tens digit)
- `number % 10` — the **remainder** after dividing: `45 % 10` is `5` (the units digit)

Edit `digits.c` to exactly this:

```c
/* digits.c */
#include <stdio.h>

/* Assumption: only two-digit numbers (10 to 99) are given. */
int sum_of_digits(int number);

int main()
{
    int number = 45;
    int total;

    total = sum_of_digits(number);
    printf("Sum of digits of %d is %d.\n", number, total);
    return 0;
}

/* Assumption: only two-digit numbers (10 to 99) are given. */
int sum_of_digits(int number)
{
    int tens = number / 10;
    int units = number % 10;
    return tens + units;
}
```

### b. Task

1. Before compiling, put your finger on each of the three roles and name
   them: the **declaration** (line ending in `;` above `main`), the
   **definition** (the body below `main`), the **call** (inside `main`).
2. Compile and run. Predict the output first.
3. Trace the call by hand, like Task-6 taught: at
   `total = sum_of_digits(number);`, the value `45` travels into the
   parameter `number` of the function; work out `tens`, `units`, and the
   returned value on paper.
4. A puzzle to *think about* (don't do it yet — Iteration 7 does it
   properly): `main` calls a function whose body lives *below* it. Why does
   the compiler, reading top to bottom, not complain at the call?

### c. Observation (what you should find)

- Output: `Sum of digits of 45 is 9.`
- The compiler reads top to bottom. At the call site it has *not yet seen*
  the body — but it has seen the **declaration**, which promised: name
  `sum_of_digits`, takes `int`, returns `int`. That promise is all it needs
  to compile the call. The body is checked against the same promise when
  the compiler reaches it later.
- The comment travels with both declaration and definition. The function
  does not *check* that the number has two digits — the comment records the
  **assumption** under which the code is correct. Feed it `456` and it
  quietly returns `4 + 5`... wrong? No — `456 / 10` is `45`, not a digit at
  all. Assumptions written down are assumptions you can check later.

**Takeaway to say out loud:** declaration = promise, definition = work,
call = use. The declaration above `main` is why `main` may use a function
whose body comes later.

---

# Iteration 3 — One definition, many calls

### a. What we set up

The whole point of a function: **write the work once, use it many times.**

### b. Task

1. In `main`, add a second number and call `sum_of_digits` **again**:
   ```c
   int main()
   {
       int number1 = 45;
       int number2 = 87;

       printf("Sum of digits of %d is %d.\n", number1, sum_of_digits(number1));
       printf("Sum of digits of %d is %d.\n", number2, sum_of_digits(number2));
       return 0;
   }
   ```
   (Notice: this version calls the function *directly inside* `printf`'s
   argument list — a call is an expression with a value, so it can sit
   anywhere a value can.)
2. Predict both output lines, then compile and run.
3. Count in your file: how many **definitions** of `sum_of_digits` are
   there? How many **calls**?

### c. Observation (what you should find)

- ```
  Sum of digits of 45 is 9.
  Sum of digits of 87 is 15.
  ```
- **One** definition, **two** calls. Each call sends a different argument
  (`45`, then `87`) into the same parameter (`number`), and each call comes
  back with its own return value.
- The call `sum_of_digits(number1)` acted as a plain `int` value right
  inside `printf` — no separate variable needed.

**Takeaway to say out loud:** define once, call many times — each call
feeds its own argument in and gets its own result back.

---

# Iteration 4 — A second function, body left empty (for you)

### a. What we set up

Add `reverse_of_number` — but this time the worksheet gives only the
**declaration** and the **call**. The definition's body is **empty, for you
to fill**.

Edit `digits.c` — new declaration below the first one, new calls in `main`,
and the hollow definition at the bottom:

```c
/* digits.c */
#include <stdio.h>

/* Assumption: only two-digit numbers (10 to 99) are given. */
int sum_of_digits(int number);

/* Assumption: only two-digit numbers (10 to 99) are given. */
int reverse_of_number(int number);

int main()
{
    int number1 = 45;
    int number2 = 87;

    printf("Sum of digits of %d is %d.\n", number1, sum_of_digits(number1));
    printf("Sum of digits of %d is %d.\n", number2, sum_of_digits(number2));
    printf("Reverse of %d is %d.\n", number1, reverse_of_number(number1));
    printf("Reverse of %d is %d.\n", number2, reverse_of_number(number2));
    return 0;
}

/* Assumption: only two-digit numbers (10 to 99) are given. */
int sum_of_digits(int number)
{
    int tens = number / 10;
    int units = number % 10;
    return tens + units;
}

/* Assumption: only two-digit numbers (10 to 99) are given. */
int reverse_of_number(int number)
{
    /* YOUR CODE HERE — build the reverse from tens and units */
}
```

### b. Task

1. Compile **with the body still empty** and read the compiler's message —
   don't skip this step, it is the lesson:
   ```
   clang digits.c -o digits
   ```
   You get a **warning** (not an error) about a non-void function not
   returning a value. Write the warning down.
2. Run it anyway. Look at the `Reverse of ...` lines. Where have you seen
   numbers like this before? (Task-6, Concept 3 — before a variable's line
   ran.)
3. Now fill in the body. You already own the two tools: `/ 10` gives the
   tens digit, `% 10` gives the units digit. Build the reversed number from
   them (hint: the units digit must become worth ten times more).
4. Recompile — the warning should be gone — and run. Check both reverse
   lines by hand.

### c. Observation (what you should find)

- The empty body **compiles with a warning** and the program runs — but the
  reverse lines print **garbage** (some meaningless leftover number). A
  promise (`int` back) with no `return` inside means the caller receives
  whatever junk was lying around — exactly the garbage values LLDB showed
  before a variable was initialized.
- The filled body:
  ```c
  int reverse_of_number(int number)
  {
      int tens = number / 10;
      int units = number % 10;
      return units * 10 + tens;
  }
  ```
- Output becomes:
  ```
  Reverse of 45 is 54.
  Reverse of 87 is 78.
  ```
- Warnings are not decoration. The program "worked" — it compiled, it ran —
  and it was still wrong. **Read warnings like errors.**

**Takeaway to say out loud:** an `int` function must `return` an `int` on
every path — a missing return is a promise broken at run time, and the
compiler's warning was the only advance notice.

---

# Iteration 5 — Split into three files: `aikfns.h`, `aikfns.c`, `digits.c`

### a. What we set up

Real projects don't keep everything in one file. The split follows the
roles you already know:

- **declarations** (the promises) → a **header file** `aikfns.h`
- **definitions** (the work) → an implementation file `aikfns.c`
- `main` (the use) → stays in `digits.c`, which `#include`s the header

```
 aikfns.h      declarations         int sum_of_digits(int number);
    ▲   ▲                           int reverse_of_number(int number);
    │   │
    │   └── #include "aikfns.h" ── digits.c    (main + the calls)
    └────── #include "aikfns.h" ── aikfns.c    (the two bodies)
```

Create `aikfns.h`:

```c
/* aikfns.h — declarations of the two-digit number tools */

/* Assumption: only two-digit numbers (10 to 99) are given. */
int sum_of_digits(int number);

/* Assumption: only two-digit numbers (10 to 99) are given. */
int reverse_of_number(int number);
```

Create `aikfns.c` (move both bodies here, and include our own header):

```c
/* aikfns.c — definitions (implementations) */
#include "aikfns.h"

/* Assumption: only two-digit numbers (10 to 99) are given. */
int sum_of_digits(int number)
{
    int tens = number / 10;
    int units = number % 10;
    return tens + units;
}

/* Assumption: only two-digit numbers (10 to 99) are given. */
int reverse_of_number(int number)
{
    int tens = number / 10;
    int units = number % 10;
    return units * 10 + tens;
}
```

Cut `digits.c` down to `main` plus the include:

```c
/* digits.c */
#include <stdio.h>
#include "aikfns.h"

int main()
{
    int number1 = 45;
    int number2 = 87;

    printf("Sum of digits of %d is %d.\n", number1, sum_of_digits(number1));
    printf("Sum of digits of %d is %d.\n", number2, sum_of_digits(number2));
    printf("Reverse of %d is %d.\n", number1, reverse_of_number(number1));
    printf("Reverse of %d is %d.\n", number2, reverse_of_number(number2));
    return 0;
}
```

### b. Task

1. Compile — **both** `.c` files go on the command line (the header does
   not):
   ```
   clang digits.c aikfns.c -o digits
   ./digits
   ```
2. Confirm the output is *identical* to Iteration 4's final output. Nothing
   changed for the user; everything changed in the layout.
3. Task-4 taught that `#include` is literal text-paste. Prove it to
   yourself here:
   ```
   clang -E digits.c | grep -n "int sum_of_digits"
   ```
   Your own header's line is sitting inside the preprocessed output — 
   pasted in, exactly like `stdio.h`'s contents.
4. Say out loud what each file contributes: `digits.c` (use), `aikfns.c`
   (work), `aikfns.h` (the shared promises both sides include).

### c. Observation (what you should find)

- Same four lines of output — a pure re-organisation, no behaviour change.
- The pattern is exactly `stdio.h`'s pattern: `digits.c` includes a header
  full of *declarations* (`printf`'s lives in `stdio.h`, yours live in
  `aikfns.h`) and the *definitions* live in a different file entirely. You
  have been using this architecture since your first `printf` — now you've
  built one.
- `aikfns.c` includes its own header too — so the compiler can check the
  definitions against the declared promises while compiling `aikfns.c`.
- The quotes in `#include "aikfns.h"` (instead of `< >`) matter — 
  Iteration 6 shows why.

**Takeaway to say out loud:** header = promises, `.c` = work, `#include`
pastes the promises wherever they're needed. `stdio.h` is not magic — it is
this, done by someone else.

---

# Iteration 6 — Where does the compiler *find* headers?

### a. What we set up

Task-5's Mission 2 answered "where does the *shell* find commands?" — the
`PATH` folder list. Headers have the same story: when the preprocessor sees
`#include`, it searches a **list of folders**. Let's print that list
instead of believing anyone.

### b. Task

1. Ask clang to narrate its preprocessing (`-v` = verbose), and read the
   search list it prints:
   ```
   clang -E -v digits.c -o /dev/null
   ```
   (Sending the output to `/dev/null` — the system's dustbin — keeps the
   screen clear of the pasted text; we only want the chatter.)
   In the flood, find these lines and copy them into your notebook:
   ```
   #include "..." search starts here:
   #include <...> search starts here:
    ...
   End of search list.
   ```
2. Visit the busiest system folder from that list and find an old friend:
   ```
   ls /usr/include | wc -l
   ls -l /usr/include/stdio.h
   ```
3. Check whether include-related environment variables are set on your
   machine (they usually aren't — the built-in list does the work):
   ```
   printenv CPATH
   printenv C_INCLUDE_PATH
   echo $PATH
   ```
   Compare the *idea* of `$PATH` (folders searched for **commands**) with
   the search list from step 1 (folders searched for **headers**).

### c. Observation (what you should find)

- clang prints two search lists. The `"..."` form searches **the file's own
  folder first**, then falls through to the system list; the `<...>` form
  searches **only the system list** (`/usr/local/include`, `/usr/include`,
  clang's own include folder, ...).
- `/usr/include` holds hundreds of headers, and `stdio.h` is physically
  there — an ordinary text file you could open. `#include <stdio.h>` works
  *because* `/usr/include` is on the `<...>` list.
- `#include "aikfns.h"` worked with no setup *because* the `"..."` search
  starts in the folder next to your source file — where `aikfns.h` sits.
- `CPATH` / `C_INCLUDE_PATH` are empty on a fresh machine: environment
  variables *can* add folders to the search, but the compiled-in defaults
  handle the normal cases.

**Takeaway to say out loud:** `""` means "my folders first, then the
system's"; `<>` means "system folders only." The include search list is to
headers what `$PATH` is to commands.

---

# Iteration 7 — Break it on purpose: three sabotages, three error stages

### a. What we set up

Task-4 showed the pipeline: **preprocessor → compiler → linker**. Each
stage can fail, and each fails with its *own kind of message*. We now
trigger all three, on purpose, and write down every message — so that the
day a real one appears, you recognise the stage it came from.

Work on the Iteration-5 three-file program. After each sabotage: **revert,
recompile, confirm the program is healthy again** before the next one.

### b. Task

**Sabotage (a) — comment out a declaration (compiler error)**

1. In `aikfns.h`, comment out the declaration of `sum_of_digits`:
   ```c
   /* int sum_of_digits(int number); */
   ```
2. Recompile:
   ```
   clang digits.c aikfns.c -o digits
   ```
3. Write the message down. Note: **which file and line** does it point at?
   Which *stage* is speaking — how do you know? Then **revert** and confirm
   a clean compile.

**Sabotage (b) — comment out a definition (linker error)**

4. Now, in `aikfns.c`, comment out the *entire implementation* of
   `reverse_of_number` (the declaration in `aikfns.h` stays!):
   ```c
   /*
   int reverse_of_number(int number)
   {
       int tens = number / 10;
       int units = number % 10;
       return units * 10 + tens;
   }
   */
   ```
5. Recompile the same way. Write the message down. Compare it, point by
   point, with sabotage (a)'s message: is there a file name and line
   number? Does the word `ld` or `linker` or `undefined` appear? Then
   **revert** and confirm a clean compile.

**Sabotage (c) — include your header with `<>` (preprocessor error)**

6. In `digits.c`, change the include of your own header to the angle-bracket
   form:
   ```c
   #include <aikfns.h>
   ```
7. Recompile. Write the message down — which stage refused, and why does
   Iteration 6 make the reason obvious? Revert to `"aikfns.h"` and confirm
   the final, healthy compile and run.

### c. Observation (what you should find)

- **(a)** The **compiler** complains, with an exact file and line in
  `digits.c` at the call site — something like
  `error: call to undeclared function 'sum_of_digits'` (older clangs say
  `implicit declaration of function`). The promise disappeared, so the call
  can no longer be checked. The body in `aikfns.c` still exists — 
  irrelevant: the compiler works one file at a time and needed the
  *declaration*.
- **(b)** Compilation of both files **succeeds** — each file keeps its
  promises as far as the compiler can see. The failure comes later and
  reads completely differently: the **linker** (`ld`) reports something
  like `undefined reference to 'reverse_of_number'` / `undefined symbol` —
  and there is **no source line number**, because the linker doesn't read
  source; it joins compiled pieces and found a promise nobody fulfilled.
- **(c)** The **preprocessor** stops immediately:
  `fatal error: 'aikfns.h' file not found`. Iteration 6 explains it
  exactly: `<>` searches only the system folders, and your `aikfns.h` is
  not in any of them — it's in the current folder, which only `""` looks
  at.
- The complete map, one error per stage:

  | Sabotage | Missing piece | Caught by | Message shape |
  |----------|--------------|-----------|----------------|
  | (a) | declaration | **compiler** | file + line, "undeclared function" |
  | (b) | definition | **linker** | no line number, "undefined reference/symbol" |
  | (c) | the header itself | **preprocessor** | "file not found" |

**Takeaway to say out loud:** a missing *declaration* angers the compiler,
a missing *definition* angers the linker, a missing *header* angers the
preprocessor. Read the message's shape and you know which stage — and
therefore which fix.

---

## One-page reference

| Goal | Shape / command |
|------|-----------------|
| Declaration (the promise) | `int sum_of_digits(int number);` |
| Definition (the work) | `int sum_of_digits(int number) { ... return ...; }` |
| Call (the use) | `sum_of_digits(45)` — an expression with an `int` value |
| Tens digit of a 2-digit number | `number / 10` |
| Units digit | `number % 10` |
| Rebuild reversed | `units * 10 + tens` |
| Compile one file | `clang digits.c -o digits` |
| Compile many files | `clang digits.c aikfns.c -o digits` (headers never listed) |
| Include your own header | `#include "aikfns.h"` (current folder first) |
| Include a system header | `#include <stdio.h>` (system folders only) |
| Print the include search lists | `clang -E -v digits.c -o /dev/null` |
| Include-related env variables | `printenv CPATH`, `printenv C_INCLUDE_PATH` |
| Missing declaration → | compiler error (has file + line) |
| Missing definition → | linker error (`undefined reference`, no line) |
| Header not found → | preprocessor error (`file not found`) |

**The picture to remember:**

```
 aikfns.h  (promises)  ──#include──►  digits.c (use)   ─┐
     └───────#include──►  aikfns.c (work)              ─┤ clang digits.c aikfns.c
                                                        ▼
                              preprocessor → compiler → linker → ./digits
```

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English word | తెలుగు అర్థం |
|--------------|--------------|
| **function** | ఫంక్షన్ — పేరు ఉన్న పని ముక్క; విలువలు తీసుకుని, పని చేసి, ఫలితాన్ని తిరిగి ఇస్తుంది. `main` కూడా ఒక ఫంక్షనే. |
| **declaration / prototype** | ప్రకటన — ఫంక్షన్ పేరు, తీసుకునే రకం, ఇచ్చే రకం మాత్రమే చెప్పే వాక్యం (`;` తో ముగుస్తుంది); "ఇలాంటిది ఉంది" అనే **వాగ్దానం**. |
| **definition / implementation** | నిర్వచనం / అమలు — ఫంక్షన్ అసలు శరీరం `{ }`; పని ఎలా జరుగుతుందో ఇక్కడే ఉంటుంది. |
| **call** | పిలుపు — ఫంక్షన్‌ను నడిపించడం (ఉదా: `sum_of_digits(45)`); పిలుపు ఒక విలువగల వ్యక్తీకరణ. |
| **parameter** | పరామితి — ఫంక్షన్ లోపల విలువను అందుకునే పేరు (ఉదా: `number`). |
| **argument** | ఆర్గ్యుమెంట్ — పిలిచేటప్పుడు పంపే అసలు విలువ (ఉదా: `45`, `87`). |
| **return value** | తిరిగి ఇచ్చే విలువ — ఫంక్షన్ `return` తో వెనక్కి పంపే ఫలితం; పిలిచిన చోటికి చేరుతుంది. |
| **integer division** (`/`) | పూర్ణాంక భాగహారం — శేషాన్ని వదిలేసి భాగఫలం మాత్రమే (45 / 10 = 4). |
| **remainder / modulo** (`%`) | శేషం — భాగించగా మిగిలేది (45 % 10 = 5); చివరి అంకెను ఇస్తుంది. |
| **assumption** | ఊహ / ముందస్తు షరతు — కోడ్ సరిగ్గా పనిచేయడానికి నిజమై ఉండాల్సిన షరతు (ఇక్కడ: రెండంకెల సంఖ్యలే వస్తాయి); వ్యాఖ్యగా రాసి పెడతాం. |
| **header file** (`.h`) | హెడర్ ఫైల్ — ప్రకటనలు (వాగ్దానాలు) ఉంచే ఫైల్; `#include` తో అవసరమైన చోట అతికించబడుతుంది. |
| **implementation file** (`.c`) | అమలు ఫైల్ — నిర్వచనాలు (అసలు పని) ఉండే ఫైల్; కంపైల్ కమాండ్‌లో ఇదే ఇస్తాం, హెడర్ ఇవ్వం. |
| **multi-file program** | బహుళ-ఫైల్ ప్రోగ్రామ్ — కోడ్‌ను పాత్రల వారీగా (వాడకం / పని / వాగ్దానాలు) వేరు ఫైళ్ళుగా విడదీయడం. |
| **include search path** | హెడర్ వెతుకుడు జాబితా — `#include` ఫైల్ కోసం కంపైలర్ చూసే ఫోల్డర్ల వరుస; `clang -E -v` తో చూడవచ్చు. కమాండ్లకు `$PATH` ఎలాగో, హెడర్లకు ఇది అలాగ. |
| **`""` vs `<>`** | రెండు రకాల చేర్పులు — `"file.h"` ముందుగా మన ఫోల్డర్‌లో వెతుకుతుంది; `<file.h>` సిస్టమ్ ఫోల్డర్లలో మాత్రమే వెతుకుతుంది. |
| **warning** | హెచ్చరిక — కంపైలర్ ఆపకుండా చెప్పే జాగ్రత్త మాట; పట్టించుకోకపోతే ప్రోగ్రామ్ నడిచినా తప్పు ఫలితాలు రావచ్చు (ఖాళీ శరీరం గుర్తుందా?). |
| **garbage value** | చెత్త విలువ — `return` లేని `int` ఫంక్షన్ నుండి పిలిచినవాడికి చేరే అర్థంలేని సంఖ్య; మెమొరీలో మిగిలిన పాత చెత్త. |
| **compiler error** | కంపైలర్ లోపం — ప్రకటన కనబడకపోతే వచ్చేది; ఫైల్ పేరు, పంక్తి సంఖ్యతో సహా చూపిస్తుంది. |
| **linker** | లింకర్ — కంపైల్ అయిన ముక్కలన్నీ కలిపి ఒక ప్రోగ్రామ్ చేసే సాధనం (`ld`); వాగ్దానానికి శరీరం దొరకకపోతే ఇదే ఫిర్యాదు చేస్తుంది. |
| **undefined reference / symbol** | నిర్వచించని పేరు — "ఈ ఫంక్షన్‌కు శరీరం ఎక్కడా లేదు" అనే లింకర్ ఫిర్యాదు; పంక్తి సంఖ్య ఉండదు. |
| **`/dev/null`** | చెత్తబుట్ట ఫైల్ — ఇందులోకి పంపినదంతా మాయమవుతుంది; అనవసర అవుట్‌పుట్ తెరపై రాకుండా ఉంచడానికి వాడతాం. |
