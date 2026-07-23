# Adding to an Address — Discover the Rule

**Goal.** Today you are the scientist. You will run two small programs, watch exactly what they do — on screen and inside the debugger — and **write down what you see**. Then, from your own notes, you will invent a theory that explains it. Only after that will you read the explanation and check it against your theory. Do not skip ahead: the discovery is the lesson.

**You need:** your Linux VM (WSL) with `clang` and `lldb`, a notebook, and a pencil.

> **How to use this sheet**
> Parts 1 and 2 are for **observing only** — record what happens, do not try to explain it yet. Part 3 is where you build your theory. Part 4 gives the explanation. If you read Part 4 first, you have wasted the experiment.

Two small reminders so you can read the code:
- From Task 9 you already know `&x` means **"the address of x"** — *where* something lives in memory.
- You will also see `*( ... )` and `+` used on an address. Those are **new** — and working out what they do is exactly your job today. Don't look them up; watch them.

---

# Part 1 — Observe Sample #1

## 1a. Run it

Create `array_int.c`:

```c
#include <stdio.h>

void print_array(int a[4]);

int main()
{
    int a[4] = {0, 0, 0, 0};

    print_array(a);

    *(&a[1] + 2) = 22;
    print_array(a);

    *(&a[1] - 1) = 11;
    print_array(a);

    *(&a[1] + 1) = 99;
    print_array(a);

    *(a + 0) = 66;
    print_array(a);

    return 0;
}

void print_array(int a[4])
{
    printf("%2i, %2i, %2i, %2i\n",
           a[0], a[1], a[2], a[3]);
}
```

Compile and run:

```
clang -g array_int.c -o array_int
./array_int
```

**Record in your notebook.** You should see these five rows — check yours match:

```
 0,  0,  0,  0
 0,  0,  0, 22
11,  0,  0, 22
11,  0, 99, 22
66,  0, 99, 22
```

Now make a table. For each assignment line, write **which slot changed** and **to what value**. (Compare each printed row with the one above it — the slot that changed is your answer.)

| assignment line | slot that changed | new value |
|---|---|---|
| `*(&a[1] + 2) = 22;` | ? | ? |
| `*(&a[1] - 1) = 11;` | ? | ? |
| `*(&a[1] + 1) = 99;` | ? | ? |
| `*(a + 0) = 66;` | ? | ? |

Do not explain *why* yet. Just fill the table from what you saw.

## 1b. See it in the debugger

Break inside `print_array` and ask for some addresses:

```
lldb array_int
(lldb) breakpoint set --name print_array
(lldb) run
(lldb) p &a[0]
(lldb) p &a[1]
(lldb) p &a[1] + 2
(lldb) p &a[1] - 1
(lldb) p a + 0
(lldb) p sizeof(int)
```

**Record in your notebook.** You will see something like this (your exact hex will differ — the *gaps between them* will not). Ignore the `(int *)` label on the left; write down only the hex number:

```
(int *) 0x00007fffffffe4c8      &a[0]
(int *) 0x00007fffffffe4cc      &a[1]
(int *) 0x00007fffffffe4d4      &a[1] + 2
(int *) 0x00007fffffffe4c8      &a[1] - 1
(int *) 0x00007fffffffe4c8      a + 0
(unsigned long) 4               sizeof(int)
```

Now let the program finish and read the array as raw memory. Break on the `return` line and dump the bytes:

```
(lldb) breakpoint set --file array_int.c --line 22
(lldb) continue
(lldb) memory read -f d -s 4 -c 4 &a[0]
(lldb) memory read -f x -s 1 -c 16 &a[0]
```

You should see (final values `66, 0, 99, 22`):

```
0x7fffffffe4c8: 66 0 99 22
0x7fffffffe4c8: 42 00 00 00 00 00 00 00 63 00 00 00 16 00 00 00
```

**Record three things in your notebook, as plain numbers — no explanations:**
1. How many bytes is `&a[1]` past `&a[0]`? And `&a[1] + 2` past `&a[1]`?
2. Which two of the printed addresses came out *equal*?
3. How many bytes did the four numbers fill in the raw dump?

---

# Part 2 — Observe Sample #2

## 2a. Run it

Create `array_char.c` — the assignment lines have the **same shape** as Sample #1, but the array is made of `char`, and the numbers stored are letter codes (`65` is `'A'`, `88` is `'X'`):

```c
#include <stdio.h>

void print_array(char a[4]);

int main()
{
    char a[4] = {'.', '.', '.', '.'};

    print_array(a);

    *(&a[1] + 2) = 68;
    print_array(a);

    *(&a[1] - 1) = 65;
    print_array(a);

    *(&a[1] + 1) = 67;
    print_array(a);

    *(a + 0) = 88;
    print_array(a);

    return 0;
}

void print_array(char a[4])
{
    int i = 0;

    while (i < 4)
    {
        printf("'%c'", a[i]);

        if (i < 3)
        {
            printf(", ");
        }

        i++;
    }

    printf("\n");
}
```

```
clang -g array_char.c -o array_char
./array_char
```

**Record in your notebook.** Expected rows:

```
'.', '.', '.', '.'
'.', '.', '.', 'D'
'A', '.', '.', 'D'
'A', '.', 'C', 'D'
'X', '.', 'C', 'D'
```

Fill the same "which slot changed" table as before:

| assignment line | slot that changed | new letter |
|---|---|---|
| `*(&a[1] + 2) = 68;` | ? | ? |
| `*(&a[1] - 1) = 65;` | ? | ? |
| `*(&a[1] + 1) = 67;` | ? | ? |
| `*(a + 0) = 88;` | ? | ? |

## 2b. See it in the debugger

```
lldb array_char
(lldb) breakpoint set --name print_array
(lldb) run
(lldb) p &a[0]
(lldb) p &a[1]
(lldb) p &a[1] + 2
(lldb) p sizeof(char)
```

You will see something like:

```
(char *) 0x00007fffffffe4d4      &a[0]
(char *) 0x00007fffffffe4d5      &a[1]
(char *) 0x00007fffffffe4d7      &a[1] + 2
(unsigned long) 1                sizeof(char)
```

Then read the raw bytes at the end (final values `X . C D`):

```
(lldb) breakpoint set --file array_char.c --line 22
(lldb) continue
(lldb) memory read -f x -s 1 -c 4 &a[0]
(lldb) memory read -f c -s 1 -c 4 &a[0]
```

```
0x7fffffffe4d4: 58 2e 43 44
0x7fffffffe4d4: X . C D
```

**Record, as plain numbers:**
1. How many bytes is `&a[1]` past `&a[0]`? And `&a[1] + 2` past `&a[1]`?
2. How many bytes did the four letters fill in the raw dump?

---

# Part 3 — Build your own theory

Do not read Part 4 until you have written a theory. Use your two sets of notes and answer these, in your notebook:

1. In Sample #1, which slot did `*(&a[1] + 2)` change? In Sample #2, which slot did the *same* expression change? **Same slot, or different?**
2. In Sample #1, how many bytes did `&a[1] + 2` sit past `&a[1]`? In Sample #2, how many? **Same number of bytes, or different?**
3. So the same expression moved a **different number of bytes** in the two programs — yet changed the **same slot**. What is the one thing that is different between the two programs that could explain this?
4. Look at your `sizeof` notes (`4` and `1`). Look at the byte-gaps you measured. Do you see a relationship?
5. In both programs, `a + 0` and `&a[0]` printed the **same** address. What does that tell you about what the bare name `a` stands for?
6. The raw dump filled **16** bytes in Sample #1 but only **4** in Sample #2. Why might the *same number of slots* take up different amounts of memory?

Now write your theory in two or three sentences. It should finish this sentence:

> "When you add a whole number `n` to the address of an array slot, the address moves forward by ____, and the amount depends on ____."

Write it down before turning the page. A wrong theory you actually committed to is worth more than a right one you read.

---

# Part 4 — The theory

Now compare your theory with this.

**Reaching a slot by its address.** `&a[i]` is the address of slot `i`. The new symbol `*( addr )` means **"the slot that lives at this address"** — follow the address and use that box. So `*(&a[1]) = 22` would just be `a[1] = 22` said the long way. And the bare array name `a` is the address of the first slot: `a` is the same as `&a[0]` — which is why `a + 0` and `&a[0]` printed the same address (your observation 5).

**A step counts slots, not bytes.** This is the heart of it. When you write `&a[1] + 2`, the `+ 2` does **not** move 2 bytes forward. It moves **2 whole slots** forward — `a[1] → a[2] → a[3]` — so it lands on `a[3]`. That is why the same expression hit the same slot in both programs (observation 1): "2 slots past slot 1" is `a[3]` no matter what the array holds.

**How many bytes one slot is.** The language turns "2 slots" into an actual address by multiplying by the **size of one slot**:

```
bytes moved  =  step  ×  size of one element
```

An `int` is 4 bytes (`sizeof(int)` = 4), so in Sample #1 each step was 4 bytes and `+2` moved `2 × 4 = 8`. A `char` is 1 byte (`sizeof(char)` = 1), so in Sample #2 each step was 1 byte and `+2` moved `2 × 1 = 2`. Same step, same landing slot, different byte-distance (observations 2, 3, 4) — because the slots are different sizes. Stepping works backwards too: `&a[1] - 1` is one slot back, `a[0]`.

**The big identity.** Put `*` and `+` together and you get something you have used since your very first array:

```
a[i]   is exactly   *(a + i)
```

Start at `a` (the front), step `i` slots, open the box. The square-bracket index was address-stepping all along — the compiler was doing `i × element-size` for you every time.

**The memory footprint.** The raw dumps showed why the byte counts differ: four `int` slots are `4 × 4 = 16` bytes, four `char` slots are `4 × 1 = 4` bytes (observation 6). In the dump, the spacing between one value and the next *is* the slot size — 4 apart for ints, 1 apart for chars. (The value's low byte comes first, e.g. `66` as `42 00 00 00`; that ordering is called **little-endian** — just note it.)

**Start-step-land.** From now on, read any line like `*(&a[i] + n)` in three beats: **start** at the address of slot `i`, **step** `n` slots (forward or back), **land** and open that box. It is the whole sheet in three words.

**What comes next.** This behaviour — doing arithmetic on addresses — has a proper name in C, and there is a name for the kind of value an address is. You will meet both in the next lesson. But the hard part is already done: you discovered the rule yourself, and you can now predict where any of these expressions lands. The vocabulary will just be a label for what you already understand.

Go back and check: does your Part 3 theory match this? Where it differed, which observation would have pointed you the right way?

---

## One-page reference

| You write | Means | Example on `int a[4]` |
|---|---|---|
| `a` | the address of the first slot (`&a[0]`) | — |
| `&a[i]` | the address of slot `i` | `&a[2]` is `a` + 8 bytes |
| `*( addr )` | the slot living at that address | `*(&a[2])` is `a[2]` |
| `a + i` | address stepped forward `i` **slots** | `a + 2` is `&a[2]` |
| `a[i]` | shorthand for `*(a + i)` | `a[2]` is `*(a + 2)` |

**Rules to keep:**
- One step = one slot, **not** one byte.
- Slot size in bytes = size of the element: `int` = 4, `char` = 1 (check with `sizeof`).
- Bytes moved by `+ n` = `n × slot-size`.
- Stepping works both ways: `-1` goes back one slot.
- `a[i]` is exactly `*(a + i)`; and `a` is `&a[0]`.
- In LLDB: `p &a[i]` (an address), `p sizeof(int)` (slot size), `memory read -s 1 -c N &a[0]` (raw bytes — spacing shows slot size).

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| array | వరుస (అరే) | ఒకే రకమైన పెట్టెలు వరుసగా పక్కపక్కన |
| slot / element | గది / మూలకం | వరుసలో ఒక్క పెట్టె |
| index | సూచిక | పెట్టె నంబరు — `a[2]` లో `2` |
| address | చిరునామా | మెమరీలో ఆ పెట్టె ఉన్న స్థానపు సంఖ్య |
| address of (`&`) | చిరునామా | `&a[1]` = a[1] పెట్టె చిరునామా |
| the slot at (`*`) | ఆ చిరునామాలోని గది | `*(addr)` = ఆ చిరునామాలో ఉన్న పెట్టె |
| step | అడుగు | చిరునామాకు సంఖ్య కలిపి ఇన్ని గదులు జరగడం |
| element size | గది పరిమాణం | ఒక గది ఆక్రమించే బైట్ల సంఖ్య (int=4, char=1) |
| byte | బైట్ | మెమరీలో అతి చిన్న కొలత (8 బిట్లు) |
| little-endian | లిటిల్-ఎండియన్ | విలువ తక్కువ బైట్లు ముందు నిల్వ చేసే క్రమం |
| `sizeof` | సైజ్ ఆఫ్ | ఒక రకం ఎన్ని బైట్లు అని చెప్పే పదం |
| theory | సిద్ధాంతం | పరిశీలనల ఆధారంగా మనం రూపొందించే వివరణ |
