# Looking Inside the Compiler — Answers with Reasoning

Answer key for `compilation_intermediate_files_questions.md`. Every answer
includes the reasoning — when checking a student's work, check the **why**,
not just the letter. A correct letter with a wrong reason is a lucky guess.

---

# Part A — Multiple Choice Questions

**Q1. Answer: (b) — `.c → .i → IR (.ll/.bc) → .s → .o → executable`.**
Preprocess, then front-end to IR, then back-end to assembly, then assemble
to an object file, then link. `--save-temps` shows the products in exactly
this order, and each stage's tool hands its file to the next.

**Q2. Answer: (b) — stop after preprocessing.**
`-E` runs *only* the preprocessor. The `.i` file is still text, still C —
but with every `#include` pasted in and line markers planted. Nothing
downstream ever sees the word `#include`.

**Q3. Answer: (c) — literal text copy-paste, before the compiler runs.**
That is the whole feature. Your program didn't "import" printf; the text of
its declaration was physically copied into your translation unit. (a) is a
different, later stage (linking supplies the *implementation*; the header
only supplies *declarations*), and (b)/(d) credit the preprocessor with
intelligence it does not have — it doesn't understand C at all.

**Q4. Answer: (d) — the pasted headers.**
One `#include <stdio.h>` dragged in the whole of `stdio.h` *and every header
stdio.h itself includes*. `grep -n "int printf" hello.i` finds the printf
declaration physically sitting in your `.i` — copied there from the system
header.

**Q5. Answer: (b).**
`bare.c` has no includes, and its `.i` is almost empty — a controlled
experiment. Same preprocessor, same kind of file; the only variable was the
`#include`, so the ~800 extra lines in `hello.i` must have come entirely
from it, not from anything the preprocessor "adds to every program."

**Q6. Answer: (c) — a line marker.**
Format: `# <line-number> "<file-name>" <flags>`. It announces: "the text
that follows is line 5 of hello.c." The preprocessor plants these signposts
all through the `.i`; the compiler reads each one and resets its internal
line counter and current-file name.

**Q7. Answer: (b) — the compiler reads the line markers.**
The compiler never looks at `hello.c` again (it may not even exist — you can
compile a `.i` alone, see Q29). As it walks the `.i`, every marker it passes
resets its notion of "where am I in the user's source." When it hits the bug
at physical line ~831, the most recent marker says "you are in hello.c around
line 5" — so that's what the error reports.

**Q8. Answer: (d) — the compiler trusts the markers completely.**
After you lie in the marker, the error dutifully reads `AIK.c:103` — a file
that doesn't exist. Count the physical lines from your fake marker down to
the bug and the number lines up exactly. There is no validation, no magic:
error locations *are* the markers plus simple counting.

**Q9. Answer: (a) — LLVM IR serialized in binary form.**
`.bc` = bitcode. It is the *same information* as the `.ll` text file, just
packed into bytes for tools to read quickly — Concept 3 proved it by round-
tripping with `llvm-dis`. (c) describes the `.o` file, a later stage.

**Q10. Answer: (b) — the magic number.**
`42 43` are ASCII `B` and `C`; then come `0xC0 0xDE` — together `BC\xC0\xDE`,
the four-byte signature by which tools recognize a bitcode file. Magic
numbers are how `file` can name a file's type without being told.

**Q11. Answer: (b) — bitcode → readable text IR.**
`llvm-dis` **dis**assembles LLVM bitcode into `.ll` text. The output is
identical in content to what `clang -emit-llvm -S` produces — proving `.ll`
and `.bc` are one thing in two encodings, not two different things.

**Q12. Answer: (b) — the optimizer works on IR, and the diff makes its work visible.**
At `-O0` the IR mirrors the naïve meaning of the C: allocate a stack slot,
store the argument, load it back twice, multiply. At `-O2` the optimizer
proved the loads/stores pointless and deleted them, leaving
`mul nsw i32 %0, %0` + `ret`. The multiplication is very much still there —
(a) is wrong — it's the *ceremony around it* that vanished.

**Q13. Answer: (c) — clang always passes through IR.**
It never turns C straight into assembly. `-emit-llvm` and `--save-temps`
don't *create* the IR stage; they only let you *see* a step that happens on
every compile. That is the single most important idea on the sheet.

---

# Part B — Fill in the Blanks

**Q14.** a **driver**.
clang orchestrates preprocessor, front-end, back-end, assembler, and linker
— many tools behind one command.

**Q15.** `clang **--save-temps** -c file.c`.
Four products appear: `.i`, `.bc`, `.s`, `.o` — the pipeline frozen to disk,
one file per stage.

**Q16.** `.i` is the **preprocessed** source; `.s` is native **assembly**;
`.o` is machine code not yet **linked**.

**Q17.** **`-emit-llvm`** in both blanks.
The flag means "produce LLVM IR instead of native assembly"; pair it with
`-S` for text (`.ll`) or `-c` for binary (`.bc`).

**Q18.** `1` = just **entered** a new file (an `#include` began);
`2` = just **returned** to a file (the include ended);
`3` = text is from a **system** header (which mutes warnings in it).

**Q19.** `wc -l` counts **lines**; `grep -n` prints the **line number** of
each match. Both were the worksheet's measuring instruments: `wc -l` showed
the before/after explosion, `grep -n` located declarations inside the `.i`.

**Q20.** **`file`**.
It reads the first bytes (the magic number) and names the type —
`LLVM IR bitcode` for `.bc`, `ELF ... executable` for compiled programs.

**Q21.** the **linker**.

**Q22.** the **line markers**.
`-P` strips the `# N "file"` signposts, leaving pure preprocessed code —
easier to read, but the compiler would no longer be able to map errors back
to your original lines.

---

# Part C — Scenario Questions

**Q23. What the compiler actually reads.**
The compiler proper never sees `hello.c` — it sees the preprocessed text,
with all includes pasted in (that's the `.i` you can capture with
`clang -E`). The file name and line number in errors come from the **line
markers** the preprocessor planted: `# 5 "hello.c"` means "the following
text is line 5 of hello.c," and the compiler resets its position from each
marker it passes. So `hello.c:5` in an error is the compiler reading the
nearest signpost — not evidence it read your file directly. Concept 2's
experiment proves it: compile the `.i` alone and the error *still* says
`hello.c:5`, even though the bug physically sits near line 831.

**Q24. Swathi's shrinking `.i`.**
The ~830 lines were the pasted text of `stdio.h` and everything it includes;
remove the `#include` and the paste never happens, so the `.i` is just her
own ~7 lines (plus markers) — exactly the `bare.c` result. When she
compiles, `printf` now has no declaration in the translation unit: modern
clang reports an error (implicit declaration of function `printf`) and tells
her to include `<stdio.h>`. The lesson: the declaration your code relies on
is not built into the compiler — it is text, and she just deleted the
instruction that pastes that text in.

**Q25. Naveen's "garbage".**
Nothing went wrong — `.bc` is **bitcode**, the binary serialization of LLVM
IR, and binary files look like garbage in a text editor by design.
`file square.bc` identifies it (`LLVM IR bitcode` — recognized by the
`BC\xC0\xDE` magic number in its first four bytes), and
`llvm-dis square.bc -o square.ll` converts it into the human-readable text
form. Rule of thumb: when a file looks like noise, ask `file` what it is
before assuming it is broken.

**Q26. Demonstration that IR is a real stage.**
Run `clang --save-temps -c square.c` and list the products: `square.i`,
`square.bc`, `square.s`, `square.o`. The `.bc` appears *without anyone
asking for IR* — on an ordinary compile — sitting between the preprocessed
source and the assembly, which shows IR is a stage clang passes through
every time, not an opt-in extra. To close the loop: `llvm-dis square.bc`
shows readable IR, and reading `square.s` alongside it shows the assembly is
plainly a translation *of that IR* (same shape, one operation per line), not
of the C directly. Path demonstrated: C → IR → assembly.

**Q27. Errors inside `util.h`.**
When the preprocessor pastes `util.h` into `use.i`, it brackets the pasted
text with markers: one announcing entry —
`# 1 "util.h" 1` (flag **1** = "we just entered a new file") — and one
announcing the return to `use.c` (flag `2`). While the compiler is between
those markers, its current-file name is `util.h` and its line counter runs
from the marker's number. So an error in the pasted region is reported
against `util.h` at the right line, even though the compiler only ever read
one combined text.

**Q28. What `-O2` proved, and why IR is the window.**
To delete the `alloca`/`store`/`load` traffic, the optimizer had to prove
the stack slot was pointless: the value stored is only ever loaded straight
back, nothing else can observe or modify that memory, so multiplying the
argument register by itself is behaviourally identical. The transformation
happens **on the IR** — which is why diffing the two `.ll` files makes the
optimizer's decisions directly visible. That is the worksheet's point:
assembly shows you the final result, C shows your intent, but IR is where
the compiler's actual decisions are written down, one per line.

**Q29. Compiling the `.i` directly.**
It works because a `.i` file *is* complete, valid C text — everything the
compiler needs (declarations included) is already pasted in, and the line
markers carry the position information. `clang` sees the `.i` extension,
knows preprocessing is already done, and starts from the front-end. This
shows the stages are cleanly separated: each stage's output is a real,
self-contained file that the next stage can consume on its own — which is
exactly what lets `--save-temps` freeze the pipeline to disk.

**Q30. The prankster's marker.**
The compiler will believe the marker: the error will be reported against
`mystery.c` at a line in the 4000s — the marker's `4000` plus however many
physical lines sit between the marker and the missing semicolon. No file
called `mystery.c` needs to exist. The lesson: there is no magic behind
error locations — they are the nearest upstream marker plus simple line
counting, and the compiler trusts the markers completely. (Real tools
exploit this deliberately: code generators plant markers so errors point at
*your* original source, not at their generated output.)
