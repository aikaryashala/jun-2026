# Looking Inside the Compiler — Question Bank

Answer these **after** finishing the Task-4 worksheet (Looking Inside the
Compiler: `.i`, `.bc`, and IR). Write your answers in your notebook first,
with the reasoning. The worksheet's rule applies here too: if you are unsure,
**generate the file and look** — but only *after* writing your prediction
down.

Keep the pipeline map in front of you:

```
 file.c → file.i → IR (.ll/.bc) → file.s → file.o → a.out
```

---

# Part A — Multiple Choice Questions

Choose the one best option.

**Q1.** Which is the correct order of the compilation pipeline?

- (a) `.c → .s → .i → .o → executable`
- (b) `.c → .i → IR (.ll/.bc) → .s → .o → executable`
- (c) `.c → .o → .i → .s → executable`
- (d) `.c → IR → .i → .s → .o → executable`

**Q2.** What does `clang -E hello.c -o hello.i` do?

- (a) Compiles `hello.c` into an executable named `hello.i`
- (b) Runs only the preprocessor and saves its output — the exact text the rest of the compiler will see
- (c) Produces LLVM IR in text form
- (d) Checks `hello.c` for errors without producing any file

**Q3.** What does `#include <stdio.h>` actually do?

- (a) Tells the linker to attach the stdio library
- (b) Imports only the functions your program calls
- (c) Copies the entire text of `stdio.h` (and everything it includes) into your file, before the compiler runs
- (d) Creates a reference the compiler resolves lazily when it meets `printf`

**Q4.** `hello.c` is ~7 lines but `hello.i` is over 800 lines. Where did the
extra lines come from?

- (a) The compiler adds boilerplate setup code to every program
- (b) The preprocessor expands `printf` into its implementation
- (c) They are comments the preprocessor generates for debugging
- (d) The pasted-in text of `stdio.h` and every header `stdio.h` itself includes

**Q5.** `bare.c` (no includes) produces a `.i` with almost nothing in it.
What does that prove?

- (a) `bare.c` is too small to preprocess
- (b) The bulk of `hello.i` came entirely from the `#include`, not from your code
- (c) The preprocessor skips files without includes
- (d) `bare.c` will not compile

**Q6.** In a `.i` file you find the line `# 5 "hello.c"`. What is it?

- (a) A comment the compiler ignores
- (b) A syntax error the preprocessor left behind
- (c) A line marker: "the text that follows is line 5 of hello.c"
- (d) A count of how many headers were included

**Q7.** The buggy `printf` line physically sits on line ~831 of `hello.i`,
yet `clang -c hello.i` reports the error at `hello.c:5`. How?

- (a) The compiler secretly re-reads the original `hello.c`
- (b) The compiler reads the line markers and resets its internal line counter and file name from them
- (c) The error position is stored in the `.o` file
- (d) It guesses, based on the size ratio of the two files

**Q8.** In step 3 of Concept 2, you hand-edit a marker to `# 100 "AIK.c"`
and recompile the `.i`. The error now says `AIK.c:103`. What does this
demonstrate?

- (a) The compiler validates markers against the real files on disk
- (b) Editing a `.i` file corrupts it
- (c) Renaming markers renames the output file
- (d) The compiler trusts the markers completely — they are its only source of position information

**Q9.** What is in a `.bc` file?

- (a) LLVM IR, serialized in binary form (bitcode)
- (b) Compressed C source code
- (c) Native machine code, not yet linked
- (d) The compiler's error log

**Q10.** The first four bytes of `square.bc` are `42 43 c0 de`. What are
they?

- (a) The length of the file
- (b) The magic number `BC\xC0\xDE` that identifies a bitcode file
- (c) The first IR instruction
- (d) Random garbage that changes every compile

**Q11.** What does `llvm-dis square.bc -o square_from_bc.ll` do?

- (a) Optimizes the bitcode
- (b) Converts binary bitcode into readable text IR — the same content in the other encoding
- (c) Disassembles machine code into assembly
- (d) Checks the bitcode for viruses

**Q12.** Comparing `sq_O0.ll` and `sq_O2.ll` for `square`, the `-O2` version
lost all the `alloca`/`store`/`load` lines and kept only `mul` and `ret`.
What does this show?

- (a) `-O2` removed the multiplication
- (b) The optimizer does its work on the IR — it proved the stack traffic pointless and deleted it
- (c) `-O2` produces machine code instead of IR
- (d) The `-O0` version was miscompiled

**Q13.** The single most important idea on the sheet: which statement is
true?

- (a) clang turns C directly into assembly, and IR is an optional add-on
- (b) IR exists only when you pass `-emit-llvm`
- (c) clang always passes through LLVM IR; the `.ll`/`.bc` files just let you see a step that is always happening
- (d) IR is generated after assembly, as documentation

---

# Part B — Fill in the Blanks

Write the exact missing word, flag, or command.

**Q14.** `clang` is not a single program — it is a __________ that runs a
chain of tools, each handing a file to the next.

**Q15.** To dump every intermediate stage at once:
`clang __________ -c file.c` — producing `.i`, `.bc`, `.s`, and `.o`.

**Q16.** In pipeline order: `.i` is the __________ source, `.s` is native
__________, and `.o` is machine code that is not yet __________.

**Q17.** Text IR: `clang __________ -S square.c -o square.ll`.
Binary IR: `clang __________ -c square.c -o square.bc`. (Same flag in both
blanks.)

**Q18.** In a line marker's flags, `1` means we just __________ a new file,
`2` means we just __________ to a file, and `3` means the text is from a
__________ header.

**Q19.** `wc -l` counts the number of __________ in a file; `grep -n`
searches for a pattern and also prints the __________ where each match was
found.

**Q20.** The command __________ reports a file's type — for `square.bc` it
answers `LLVM IR bitcode`.

**Q21.** The __________ combines `.o` files and libraries into the final
executable (for example `a.out`).

**Q22.** `clang -E -P file.c` preprocesses the file but leaves out the
__________ __________.

---

# Part C — Scenario Questions

Answer in 2–4 sentences each. Where a command proves your point, name the
command.

**Q23.** Ramesh says: "The compiler must read my `hello.c` directly — after
all, the error messages say `hello.c:5`." Using what you saw in Concepts 1
and 2, explain what the compiler *actually* reads, and how the correct file
name and line number still end up in the error message.

**Q24.** Swathi deletes the line `#include <stdio.h>` from `hello.c` and
regenerates the `.i` file. It shrinks from ~830 lines to about 7. Explain
exactly why — and predict what will now happen when she compiles the file,
given that `main` still calls `printf`.

**Q25.** Naveen opens `square.bc` in his text editor and sees unreadable
garbage, so he concludes the compile "went wrong." Explain what he is
actually looking at, one command that identifies the file's type, and one
command that turns it into something he can read.

**Q26.** A classmate claims: "C compilers translate C straight into
assembly — this IR business is an optional academic detail." Design a short
demonstration, using only commands from the worksheet, that shows IR is a
real stage clang always passes through. Say which files you would generate
and what each one proves.

**Q27.** In `use.c` (which has `#include "util.h"`), you introduce an error
*inside `util.h`*. When you compile `use.c`, the error is reported against
`util.h` with its own line number — even though the compiler only ever saw
the single combined `use.i` text. Explain the mechanism that makes this
possible, including which marker flag announces "we just entered an included
file."

**Q28.** Deepa runs `clang -O0 -emit-llvm -S square.c` and
`clang -O2 -emit-llvm -S square.c`, then diffs the two `.ll` files. The
`-O0` version stores the argument to a stack slot and loads it back twice;
the `-O2` version is just a `mul` and a `ret`. What did the optimizer
*prove* in order to make that change safe, and why does the worksheet say
reading IR is "the clearest window into what the compiler decided to do with
your code"?

**Q29.** Ajay runs `clang -c hello.i` — compiling the *preprocessed* file
directly, with no `-E` this time — and it produces a perfectly good
`hello.o`. Why does this work, and what does it tell you about how cleanly
the preprocessing stage is separated from the rest of the compiler?

**Q30.** A prankster edits the last line marker in your `hello.i` from
`# 4 "hello.c" 2` to `# 4000 "mystery.c" 2` and recompiles the `.i`. Your
code has a missing semicolon a few lines below that marker. What will the
error message look like now, and what does this teach you about how much
"magic" is behind compiler error locations?
