# Address Variables — Store an Address, Reach the Box

**Goal.** In Task 11 you saw that adding to an address steps it by whole slots, and that `a[i]` is just `*(a + i)`. Now you meet a variable whose whole job is to **hold an address** — an *address variable*. You will store an address in it, read and write the box it points to, step it to the neighbours, and confirm every move in the debugger with `fr v -L`.

**You need:** your Linux VM (WSL) with `clang` and `lldb`, a notebook, and a pencil.

> **The golden rule of `*( )`**
> `*( address )` means **"the box living at this address."**
> On the **right** of `=` it **reads** the value out of that box. On the **left** of `=` it **writes** a value into that box. Same notation, two jobs, decided by which side of `=` it sits on.

Two reminders from Task 11 you will use constantly:
- `&x` is **"the address of x."**
- `a[i]` is exactly `*(a + i)` — an index is address-stepping in disguise.

---

## Iteration 1 — A variable that holds an address

**a. What we set up**

You have declared `int` variables (a box holding a number) and `char` variables (a box holding a character). An **address variable** is a box that holds an *address*. You declare one with a `*` in front of the name:

```c
int num2 = 3;
int *anum;          /* anum is an address variable: it holds the address of an int */
anum = &num2;       /* store the address of num2 into anum */
```

Read `int *anum;` as "**anum holds the address of an int**." (For characters it is `char *anum;` — an address variable works for both `int` and `char`; only the slot size it steps by changes, as you will see.)

**b. Task**

Type this into `addr.c` inside a `main`, build with `clang -g addr.c -o addr`, break in `main`, run past the `anum = &num2;` line, and look:

```
(lldb) fr v -L
```

(`fr v -L` is short for `frame variable -L` — show each variable with its address.)

**c. Observation (what you should find)**

Something like (your hex will differ):

```
0x00007fffffffe4d8: (int) num2 = 3
0x00007fffffffe4c8: (int *) anum = 0x00007fffffffe4d8
```

Look carefully: the **value stored in `anum`** (`0x…e4d8`) is exactly the **address of `num2`** (the number on the far left of the `num2` line). `anum` does not hold `3` — it holds *where `3` lives*.

**Takeaway to say out loud:** "An address variable holds an address — `anum = &num2` puts num2's location into anum."

---

## Iteration 2 — Open the box: read on the right, write on the left

**a. What we set up**

```c
int num2 = 3;
int *anum;
anum = &num2;

int x = *(anum);      /* RHS: read the box anum points to → x becomes 3 */
*(anum) = 50;         /* LHS: write into the box anum points to → num2 becomes 50 */
```

**b. Task**

Predict `x` and `num2` after both lines. Then step through in the debugger, running `fr v -L` after each line, and watch which box changes.

**c. Observation (what you should find)**

`x` becomes `3` — the right-hand `*(anum)` **read** the value out of num2's box. Then `num2` becomes `50` — the left-hand `*(anum)` **wrote** into that same box. `anum` itself never changed; it still holds num2's address. One notation `*(anum)`, two jobs, chosen by the side of `=`.

**Takeaway to say out loud:** "`*(anum)` on the right reads the box; on the left writes the box."

---

## Iteration 3 — Step the address variable to the neighbours

**a. What we set up**

An address variable steps exactly like the array addresses in Task 11: `+1` moves one whole slot (4 bytes for an `int` address), and `a[i] == *(a + i)`. So `*(anum + 1)` means "the box one slot past where anum points," and `*(anum - 1)` is one slot before.

```c
int *anum;
anum = &num2;
*(anum + 1) = 100;    /* write into the box one int-slot after num2 */
```

**b. Task**

Where does `anum + 1` actually point? That depends on what sits next to `num2` in memory — which the debugger will show you in the next iteration. For now, just hold the idea: stepping an address variable lands on whatever box is that many slots away.

**c. Observation (what you should find)**

`*(anum + 1)` reads or writes a *different box* from `*(anum)` — one int-slot (4 bytes) further along. Nothing here is new: it is the Task 11 rule (`*(a + i)`, step counts slots) applied to an address you stored in a variable.

**Takeaway to say out loud:** "Stepping an address variable is the same slot-stepping as arrays — `*(anum + 1)` is one slot over."

---

## Iteration 4 — See the layout of your local variables

**a. What we set up**

Local variables of a function sit next to each other in memory. The debugger shows exactly where — so you can tell what `anum + 1` and `anum - 1` will reach. Use this program:

```c
#include <stdio.h>

int main()
{
    int num1 = 2;
    int num2 = 3;
    int sum = num1 + num2;
    int *anum;
    anum = &num2;

    return 0;
}
```

**b. Task**

Build with `clang -g`, break in `main`, run to the `return` line, and dump the layout:

```
(lldb) fr v -L
```

Write down the address of `num1`, `num2`, and `sum`. Work out the gaps.

**c. Observation (what you should find)**

On your Ubuntu (WSL) setup you will see the three ints laid out like this (your exact hex differs; the **gaps** are the point):

```
0x00007fffffffe4dc: (int) num1 = 2
0x00007fffffffe4d8: (int) num2 = 3
0x00007fffffffe4d4: (int) sum = 5
0x00007fffffffe4c8: (int *) anum = 0x00007fffffffe4d8
```

The key fact your class spotted: **`num2`'s address is `num1`'s address − 4 bytes**, and `sum` is another 4 bytes below `num2`. So starting from `anum = &num2`:

```
anum - 1   →  4 bytes before num2  →  sum
anum       →  num2
anum + 1   →  4 bytes after num2   →  num1
```

Draw it as a ladder in your notebook (address on one side, name on the other), the way you did in Task 11. This layout is what makes the next iteration's output predictable. Always confirm it with `fr v -L` — the exact arrangement is decided by your compiler.

**Takeaway to say out loud:** "`fr v -L` shows the real layout — here `anum+1` lands on num1 and `anum-1` lands on sum."

---

## Iteration 5 — The whole program: predict, then verify

**a. What we set up**

```c
#include <stdio.h>

int main()
{
    int num1 = 2;
    int num2 = 3;
    int sum = num1 + num2;
    int *anum;
    anum = &num2;
    *(anum) = *(anum + 1) + 50;
    *(anum + 1) = 100;
    *(anum - 1) = 200;
    printf("num1 = %i\n", num1);
    printf("num2 = %i\n", num2);
    printf("sum  = %i\n", sum);
    return 0;
}
```

**b. Task**

Using the layout from Iteration 4 (`anum+1` = num1, `anum` = num2, `anum-1` = sum) and the read/write rule, predict all three printed values **on paper** before running. Decode each line as a box being read or written:

- `*(anum) = *(anum + 1) + 50;` — read num1, add 50, write into num2.
- `*(anum + 1) = 100;` — write 100 into num1.
- `*(anum - 1) = 200;` — write 200 into sum.

Then build, run, and — best of all — set a breakpoint and run `fr v -L` after **each** assignment to watch the exact box change.

**c. Observation (what you should find)**

```
num1 = 100
num2 = 52
sum  = 200
```

Line by line: `*(anum) = *(anum + 1) + 50` reads `num1` (`2`), adds 50, and writes `52` into `num2`. Then `*(anum + 1) = 100` writes `100` into `num1`. Then `*(anum - 1) = 200` writes `200` into `sum`. The initial `sum = 5` never mattered — it was overwritten through an address. Every value you predicted from the layout is exactly what `fr v -L` shows and what the program prints.

**Takeaway to say out loud:** "Once I know the layout and the read/write rule, I can predict every value an address variable touches."

---

## One-page reference

| You write | Means |
|---|---|
| `int *anum;` | declare an address variable that holds the address of an `int` |
| `char *ach;` | declare an address variable that holds the address of a `char` |
| `anum = &num2;` | store the address of `num2` into `anum` |
| `*(anum)` on the **right** of `=` | **read** the value from the box `anum` points to |
| `*(anum)` on the **left** of `=` | **write** a value into the box `anum` points to |
| `*(anum + k)` | the box `k` slots away from where `anum` points (`k × slot-size` bytes) |
| `a[i]` | the same as `*(a + i)` |

**Rules to keep:**
- An address variable holds an **address**, not a value — `&x` gives an address to store.
- `*( addr )` reads on the right of `=`, writes on the left.
- Stepping an address variable moves by whole slots (Task 11): `int` = 4 bytes, `char` = 1.
- The layout of local variables is real and visible — confirm it with `fr v -L` before trusting `+1` / `-1`.
- In LLDB: `fr v -L` (short for `frame variable -L`) shows each variable *with its address*.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| address variable | చిరునామా చరరాశి | ఒక చిరునామాను నిల్వ చేసే చరరాశి (`int *anum;`) |
| address | చిరునామా | మెమరీలో ఒక పెట్టె ఉన్న స్థానపు సంఖ్య |
| store an address | చిరునామా నిల్వ చేయడం | `anum = &num2;` — num2 స్థానాన్ని anum లో పెట్టడం |
| read (RHS) | చదవడం | `=` కుడి వైపు `*(anum)` — పెట్టెలోని విలువ తీసుకోవడం |
| write (LHS) | రాయడం | `=` ఎడమ వైపు `*(anum)` — పెట్టెలోకి విలువ పెట్టడం |
| step | అడుగు | చిరునామాకు సంఖ్య కలిపి ఇన్ని గదులు జరగడం |
| layout | అమరిక | మెమరీలో చరరాశులు పక్కపక్కన ఉండే క్రమం |
| `fr v -L` | ఎఫ్ఆర్ వి -ఎల్ | ప్రతి చరరాశిని దాని చిరునామాతో చూపే lldb ఆదేశం |
