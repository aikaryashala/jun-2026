# Pipes and Redirections — Question Bank

Answer these **after** finishing the Task-7 worksheet (Pipes and Redirections —
Streams in the Shell). Write your answers in your notebook first. For every
answer, also say **why** — for shell questions, say out loud *where the input
comes from and where the output goes* before you decide.

Do not run the commands to find the answers. **Predict first.** You may verify
in the shell only *after* writing your prediction down.

---

# Part A — Multiple Choice Questions

Choose the one best option. One question may feel like it has two right
answers — pick the one that is right for the *reason* the worksheet taught.

**Q1.** By default, where is a program's `stdout` connected?

- (a) A file called `stdout.txt`
- (b) The screen (terminal)
- (c) The keyboard
- (d) The next command in the pipeline

**Q2.** What is the difference between `>` and `>>`?

- (a) `>` works only with `echo`; `>>` works with every command
- (b) `>` overwrites the file; `>>` appends at the end of the file
- (c) `>` appends at the end of the file; `>>` overwrites the file
- (d) There is no difference; `>>` is just a slower spelling of `>`

**Q3.** You run this bare command, with no command in front of the `>`:

```
> precious.txt
```

`precious.txt` already existed and had data in it. What happens?

- (a) Nothing — without a command, the shell ignores the line
- (b) The shell prints an error: "missing command"
- (c) The file is emptied to 0 bytes
- (d) The shell waits for you to type the file's new content

**Q4.** `head commands.txt` and `head < commands.txt` print the same lines.
What is the *mechanism* difference between them?

- (a) There is none; `<` is just optional decoration
- (b) In the first, `head` opens the named file itself; in the second, the shell connects the file to `head`'s stdin and `head` just reads stdin
- (c) The first reads from disk, the second reads from memory
- (d) The second one is faster because it skips the file system

**Q5.** How many lines do `head file.txt` and `tail file.txt` print by
default (when the file is long enough)?

- (a) 5
- (b) 10
- (c) 20
- (d) One full screen

**Q6.** What does `cmd1 | cmd2` connect?

- (a) The stdout of `cmd1` to the stdin of `cmd2`, with no file in between
- (b) The stdout of `cmd1` to a temporary file that `cmd2` then opens
- (c) The stdin of `cmd1` to the stdin of `cmd2`
- (d) The stdout of `cmd1` to the stdout of `cmd2`

**Q7.** Which lines of `commands.txt` does this pipeline print?

```
head -20 commands.txt | tail -5
```

- (a) Lines 1–5
- (b) Lines 1–20 and then lines from the bottom of the file
- (c) Lines 16–20
- (d) The last 5 lines of the whole file

**Q8.** You run `./sum` interactively and type `3` and `4`. Later you run
`echo -e "3\n4" | ./sum` and the `3` and `4` no longer appear on the screen.
Why?

- (a) The pipe deleted the numbers after `scanf` read them
- (b) The `3` and `4` you saw before were the terminal echoing your keystrokes, not program output — with a pipe there are no keystrokes to echo
- (c) `printf` refuses to print numbers when stdin is a pipe
- (d) The numbers went into `output.txt` instead

**Q9.** In an input file for `./sum` (used as `./sum < input.txt`), what
should the file contain?

- (a) The prompts and the numbers, exactly as the screen looked
- (b) Only the numbers — exactly and only what a human would have typed
- (c) The numbers plus the expected answer, for `diff` to check
- (d) The command `./sum` on the first line, then the numbers

**Q10.** `diff expected_output1.txt output1.txt` prints **nothing**. What
does that mean?

- (a) `diff` failed to open one of the files
- (b) Both files are empty
- (c) The two files are identical — the test passed
- (d) The test was skipped

**Q11.** In the shell, what does an exit status of `0` mean, and how do you
see the last command's exit status?

- (a) `0` means failure; see it with `echo $STATUS`
- (b) `0` means success; see it with `echo $?`
- (c) `0` means the command printed nothing; see it with `diff`
- (d) `0` means success; see it with `echo $0`

**Q12.** In `test_sum.sh`, why does the script check `$?` right after
`clang sum.c -o sum` and `exit 1` if it is non-zero?

- (a) Because `clang` cannot run inside a script without this check
- (b) To make the script run faster by skipping the tests
- (c) Because if compilation failed there is no fresh `sum`, and the tests would silently "test" whatever old executable was lying around
- (d) Because `diff` refuses to run when `$?` is non-zero

---

# Part B — Fill in the Blanks

Write the exact missing word, symbol, or command. Spelling matters in the
shell.

**Q13.** Every program has two hoses: it reads from __________ and writes to
__________.

**Q14.** `echo "note" ______ diary.txt` adds the line at the **end** of
`diary.txt` without erasing what is already there.

**Q15.** The operator `<` re-plumbs a program's stdin so that it comes from a
__________ instead of the keyboard.

**Q16.** To see only the **first 3** lines of `commands.txt`:
`__________ commands.txt`. To see only the **last 5** lines:
`__________ commands.txt`.

**Q17.** `ls /usr/bin | wc -l` — in this pipeline, the __________ of `ls`
is connected directly to the __________ of `wc`.

**Q18.** When a program is silently waiting for keyboard input, you end the
input stream (EOF) by pressing __________.

**Q19.** In C, `printf` writes to the stream called __________ and `scanf`
reads from the stream called __________.

**Q20.** One automated test case for `sum` is built from three files:
__________ (the frozen keystrokes), __________ (the blessed correct answer),
and __________ (regenerated on every run).

**Q21.** The special shell variable __________ holds the exit status of the
last command; for `diff`, the value __________ means the files were identical.

**Q22.** Complete the bash block so it prints PASSED when the previous
command succeeded (fill the three blanks):

```bash
if [ $? ______ 0 ]
______
    echo "PASSED"
else
    echo "FAILED"
______
```

---

# Part C — Scenario Questions

Answer in 2–4 sentences each. Always name the streams and operators involved
— "where does the input come from, where does the output go?"

**Q23.** Ravi keeps his lab notes in `notes.txt`. At the end of the day he
runs `echo "Day 4: learned pipes" > notes.txt` to add today's line. The next
morning the file has only one line in it. What happened to the rest of his
notes, why, and what should he have typed instead? Can he undo it?

**Q24.** Lakshmi runs `head -2` (no file name) and the terminal "hangs" —
the cursor just sits there and nothing happens. She thinks the program has
crashed and asks you for help. Explain what `head` is actually doing, two
different ways she can get her prompt back, and why `head -2 commands.txt`
never "hangs" like this.

**Q25.** Two students want the first 5 command names from `/usr/bin`.
Student A runs:

```
ls /usr/bin > commands.txt
head -5 commands.txt
```

Student B runs:

```
ls /usr/bin | head -5
```

Both see the same 5 lines. What is different about what happened on disk?
State one situation where A's approach is actually the better choice.

**Q26.** Priya prepares `input.txt` for `./sum` by copying the whole
interactive session exactly as the screen showed it:

```
To add two numbers.
Enter the first number: 3
Enter the second number: 4
```

Then she runs `./sum < input.txt` and the program misbehaves. Explain her
misunderstanding: which parts of what she copied were never *input* at all,
and what should `input.txt` contain instead?

**Q27.** Arun runs `./sum < input1.txt > output.txt` and is alarmed:
"My program is broken — the screen shows nothing at all!" Then he is alarmed
a second time: "And `output.txt` has the prompts *inside* it — prompts are
not results!" Calm him down: explain both observations using stdin, stdout,
and what `<` and `>` each re-plumbed.

**Q28.** Meena's `test_sum.sh` prints `TEST 1 PASSED` and `TEST 2 PASSED`.
To prove the tests actually work, her teacher tells her to *sabotage* the
program: change `+` to `-` in `sum.c`, then run only `./test_sum.sh` again.
What should she now expect to see, and why is deliberately breaking the
program a valuable step rather than a waste of time? What would it mean if
the tests still said PASSED after the sabotage?

**Q29.** Kiran writes his own test script **without** the compile guard:

```bash
#!/bin/bash
clang sum.c -o sum
./sum < input1.txt > output1.txt
diff expected_output1.txt output1.txt
```

One day he deletes a semicolon in `sum.c` by mistake and runs his script.
`clang` prints an error — but the script continues, and `diff` prints
nothing, so Kiran concludes "all good." Which `sum` did the test actually
run? Why is this conclusion dangerous, and which lines from `test_sum.sh`
would have prevented it?

**Q30.** A pipeline puzzle. `commands.txt` has 800 lines. Without running
anything, work out what each of these prints, and then explain the general
trick in one sentence:

```
head -30 commands.txt | tail -1
tail -30 commands.txt | head -1
```

(Which single line of the file does each one show?)
