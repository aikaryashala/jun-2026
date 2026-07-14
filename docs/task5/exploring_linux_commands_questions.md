# Exploring Linux Commands — Question Bank

Answer these **after** finishing the Task-5 worksheet (Exploring Linux
Commands — The Shell Toolbox). Write your answers in your notebook first.
For every answer, also say **why** — and if you are unsure, remember the
explorer's loop: `man` it → run it plain → add one option → say what
changed. But **predict first**; verify in the shell only after writing your
prediction down.

---

# Part A — Multiple Choice Questions

Choose the one best option.

**Q1.** In the explorer's loop, what is the *first* step when you hear about
a new command?

- (a) Search the web for a tutorial
- (b) Run it with all its options at once
- (c) Read its man page with `man <command>`
- (d) Ask a friend what it does

**Q2.** In a SYNOPSIS line such as `ls [OPTION]... [FILE]...`, what do the
square brackets and the `...` mean?

- (a) Brackets = required, `...` = type it literally
- (b) Brackets = optional, `...` = repeatable
- (c) Brackets = dangerous options, `...` = output continues
- (d) Brackets = deprecated, `...` = press Enter

**Q3.** What is the difference between `whoami` and `who`?

- (a) They are the same command with two spellings
- (b) `whoami` prints your username; `who` lists everyone logged into the machine
- (c) `whoami` lists everyone; `who` prints your username
- (d) `whoami` prints the hostname; `who` prints the kernel version

**Q4.** What does `which ls` tell you?

- (a) The options `ls` accepts
- (b) Whether `ls` is spelled correctly
- (c) The exact executable file the shell will run when you type `ls`
- (d) How many times you have run `ls`

**Q5.** Compared to `which ls`, what *extra* information does `whereis ls`
give?

- (a) The size of the executable
- (b) The location of the man page (e.g. `/usr/share/man/man1/ls.1.gz`), among other paths
- (c) The list of users allowed to run `ls`
- (d) The source code of `ls`, printed to the screen

**Q6.** You run `echo hello        world` (with many spaces). The output has
only **one** space. Why?

- (a) `echo` trims whitespace for neatness
- (b) The terminal cannot display more than one space
- (c) The shell splits the line into words before `echo` runs, so `echo` receives two separate arguments
- (d) It is a bug in `echo`

**Q7.** What is the difference between `echo "a\nb"` and `echo -e "a\nb"`?

- (a) None — both print two lines
- (b) Without `-e`, `\n` is just two characters; with `-e`, it becomes a real newline and the output splits into two lines
- (c) `-e` makes echo print to a file
- (d) Without `-e`, echo prints an error

**Q8.** `time sleep 2` reports `real` ≈ 2 s but `user` and `sys` ≈ 0. Why?

- (a) `time` failed to measure the command
- (b) `sleep` finished instantly
- (c) The CPU was too busy to time it
- (d) `real` is wall-clock time; `user`/`sys` count CPU work, and `sleep` waited without working

**Q9.** What makes a file "hidden" in Linux, and what reveals it?

- (a) A special hidden attribute set by `chmod`; revealed by `ls -l`
- (b) A name starting with a dot; revealed by `ls -a`
- (c) Being inside a folder named `hidden`; revealed by `cd`
- (d) A name ending in `.tmp`; revealed by `du`

**Q10.** `rmdir fullroom` fails with `Directory not empty`. The worksheet
calls this refusal "a feature, not a bug." Why?

- (a) Because you can force it with `rmdir -f`
- (b) Because `rmdir` only works on hidden folders
- (c) Because a remover that only demolishes empty rooms can never eat your work
- (d) Because directories can never be removed in Linux

**Q11.** `bash script.sh` runs fine, but `./script.sh` says
`Permission denied`. Why?

- (a) The two commands run different scripts
- (b) In the first, bash is the program and the script is just a file it reads; in the second, the file itself must be a program, so Linux checks its execute bit — and there is no `x`
- (c) `./` only works for compiled programs
- (d) The script has a syntax error that only `./` notices

**Q12.** On Ubuntu, what decides whether a file may be run as a program?

- (a) Whether its name ends in `.exe` or `.sh`
- (b) Whether it is in the home folder
- (c) The execute permission (`x`) on the file — the name is ignored
- (d) Whether it was created by `clang`

**Q13.** What does plain `kill <pid>` actually do?

- (a) Instantly erases the process from memory
- (b) Sends the TERM signal — a message that *asks* the process to exit
- (c) Deletes the program's executable file
- (d) Restarts the process

**Q14.** When should you use `kill -9 <pid>`?

- (a) Always — it is the fastest
- (b) Never — it is forbidden for normal users
- (c) Only as a last resort, when a process ignores the polite request — `-9` gives it no chance to clean up
- (d) Whenever the process belongs to another user

---

# Part B — Fill in the Blanks

Write the exact missing word, option, or command.

**Q15.** Inside a man page: scroll with arrows/space, search with
__________, jump to the next match with __________, and quit with
__________.

**Q16.** `uname` alone prints just `Linux`; adding the option __________
expands it to the machine's full identity card.

**Q17.** `touch notes.txt` on a file that already exists does not change its
contents — it only updates the file's __________.

**Q18.** `du -h` prints sizes in __________ form (like `4.0K`, `1.2M`), and
`-s` gives one __________ line instead of every subfolder.

**Q19.** In `file $(which ls)`, the `$(...)` construction takes one
command's __________ and pastes it into another command — so this runs
`file /usr/bin/ls`.

**Q20.** The first line of a shell script, `#!/bin/bash`, is called the
__________ — it tells Linux which program should interpret the file.

**Q21.** The command __________ `script.sh` grants the execute permission;
after it, `ls -l` shows __________ appearing in the permission string.

**Q22.** `curl https://example.com` prints the page to the screen; adding
the option __________ saves it to a file instead.

**Q23.** `sleep 300 &` — the `&` starts the command in the __________, and
in the shell's reply `[1] 7412`, the number `7412` is the process's
__________.

**Q24.** The variable __________ holds the list of folders the shell
searches when you type a command; the folders are glued together with the
character __________.

**Q25.** To count how many commands live in `/usr/bin`:
`ls /usr/bin | __________`.

---

# Part C — Scenario Questions

Answer in 2–4 sentences each. Name the exact commands you would use.

**Q26.** Pavan writes his first script and runs `./script.sh` — 
`Permission denied`. Confused, he tries `bash script.sh` and it works
perfectly. Explain to him why one way works and the other doesn't, what
one-time command fixes `./script.sh`, and how he can *see* the difference
before and after with `ls -l`.

**Q27.** Sunitha compiles her C program (`clang sum.c -o sum`) and it runs.
As an experiment she does `chmod -x sum` and now `./sum` says
`Permission denied` — yet `file sum` still reports a perfectly good
executable, and not a single byte of it changed. She asks: "On Windows a
`.exe` always runs — what is Ubuntu doing differently?" Explain the rule,
and what it implies about why `clang`'s output was runnable in the first
place despite having no extension.

**Q28.** Rajesh's terminal is full of old output and he wants a clean
screen, but he is afraid `clear` will delete his earlier results, which he
still needs to copy into his notebook. What should you tell him, and how can
he confirm it for himself?

**Q29.** Manasa makes a folder look empty — `ls emptyish` prints nothing —
but `rmdir emptyish` still refuses: `Directory not empty`. What is almost
certainly going on, which single command reveals it, and what are the two
dots she will also see in that listing?

**Q30.** Harini ran `sleep 300 &` three times while experimenting and now
wants to stop all of them. Walk her through it: how does she find the PIDs,
how does she stop each one, what will `ps` show afterwards — and what
message will she get if she mistypes a PID that doesn't exist?

**Q31.** Mission 2 asks: `which ls` searches the PATH folders **in order** —
what would happen if two different folders in PATH both contained a program
named `ls`? Answer the question, and explain what this ordering rule means
for someone who installs a custom tool into `/usr/local/bin` when an older
version exists in `/usr/bin`.

**Q32.** For Mission 3, Varun writes
`echo "Ravi\nSita\nKiran\nGeetha"` and gets one line with literal `\n`s in
it. Give the corrected command, explain what the missing piece does, and
show how he can prove with `wc -l` that his output now has exactly four
lines.

**Q33.** A teammate looks at `time ls` output — `real 0m0.004s` — and at
`time sleep 2` — `real 0m2.002s, user 0m0.001s` — and asks why `sleep`
"took 2 seconds but did no work." Untangle the three numbers (`real`,
`user`, `sys`) for her, and explain what kind of command would instead show
a *large* `user` time.
