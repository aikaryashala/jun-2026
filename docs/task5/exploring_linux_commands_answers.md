# Exploring Linux Commands — Answers with Reasoning

Answer key for `exploring_linux_commands_questions.md`. Every answer
includes the reasoning — when checking a student's work, check the **why**,
not just the letter. A correct letter with a wrong reason is a lucky guess.

---

# Part A — Multiple Choice Questions

**Q1. Answer: (c) — `man <command>` first.**
The manual is *on the machine*, always, even without internet. The loop is:
man it → run it plain → add one option at a time → say what changed. The
habit matters more than any single command.

**Q2. Answer: (b) — brackets = optional, `...` = repeatable.**
`ls [OPTION]... [FILE]...` reads as: options optional, files optional, both
repeatable. Once you can decode one SYNOPSIS, you can decode them all —
every man page follows the same skeleton.

**Q3. Answer: (b).**
`whoami` answers "which account is this shell acting as?" — one word, your
username. `who` answers "who is logged into this machine?" — one row per
login session, because Linux is a multi-user system and several people can
be logged in at once.

**Q4. Answer: (c) — the exact executable file the shell will run.**
Commands are just files, usually in `/usr/bin`. When you type `ls`, the
shell finds an executable named `ls` and runs it; `which` shows you which
file that is. Even `which which` answers `/usr/bin/which`.

**Q5. Answer: (b) — the man page location, among other paths.**
`whereis` reports executable + manual + sources; alongside `/usr/bin/ls` you
get `/usr/share/man/man1/ls.1.gz` — the compressed file that `man ls` has
been reading to you all along.

**Q6. Answer: (c) — the shell splits the line into words first.**
`echo` never saw the spaces: the shell chopped the line into the words
`hello` and `world` and passed them as two arguments; `echo` printed them
joined by single spaces. Quotes protect the spacing because the shell then
hands over the whole string as **one** argument. This is why `echo` is the
best tool for debugging quoting — it shows you what the shell did to your
line.

**Q7. Answer: (b).**
Without `-e`, `\n` is just a backslash character followed by `n`. With `-e`,
echo interprets escapes and `\n` becomes a real newline, splitting the
output into two lines. Mission 3 (team names, one per line) is built on
exactly this.

**Q8. Answer: (d).**
`real` is wall-clock time — how long you waited. `user` and `sys` count time
the CPU actually spent working (in the program and in the kernel,
respectively). `sleep` waits; it doesn't compute — so 2 seconds pass on the
wall clock while the CPU does essentially nothing.

**Q9. Answer: (b) — a leading dot; `ls -a` reveals it.**
The dot prefix is the entire hiding mechanism. Your home folder is full of
`.bashrc`, `.profile`, and friends — programs keep their settings there, out
of sight of plain `ls`.

**Q10. Answer: (c) — it can never eat your work.**
`rmdir` refuses unless the directory is already empty, so the worst it can
ever do is remove an empty room. A tool whose failure mode is "does
nothing" is safe to experiment with. (There is no `rmdir -f`; forcing
deletion is a different, more dangerous tool.)

**Q11. Answer: (b).**
`bash script.sh` runs **bash** — a program that already has permission to
run — and the script is merely a text file bash reads. `./script.sh` asks
Linux to run the file *itself* as a program, and Linux checks the file's
execute bit first: no `x`, no entry. Same file, two different questions
being asked of it.

**Q12. Answer: (c) — the execute permission, nothing else.**
Windows decides by name (`.exe`); Ubuntu ignores the name entirely and
checks one thing: the `x` bit. That's why `chmod -x` can make real machine
code un-runnable without touching its contents, and `chmod +x` restores it.

**Q13. Answer: (b) — sends the TERM signal, a polite request.**
`kill` doesn't "kill" so much as *send a message*; the default message
(TERM) asks the process to exit, and well-behaved processes obey. The PID is
the address the message is delivered to.

**Q14. Answer: (c) — last resort only.**
Signal 9 (KILL) cannot be caught or ignored: the process is terminated with
no chance to save work, remove temporary files, or clean up. Always try the
polite `kill` first; escalate to `-9` only when the process ignores it.

---

# Part B — Fill in the Blanks

**Q15.** search with **`/word`**, next match with **`n`**, quit with
**`q`**.
These pager keys work in every man page — and in `more`/`less` generally.

**Q16.** **`-a`** (all).
`uname -a` prints the full identity card: kernel name, hostname, kernel
release, kernel version, hardware (`x86_64`), OS. `-r` and `-m` pick out
single fields.

**Q17.** its **timestamp** (modification time).
Contents untouched; only the clock moves. That's also why `touch` can create
an empty file — "update the timestamp of a file that doesn't exist" starts
by creating it.

**Q18.** **human**-readable sizes; one **summary** line.
`du -sh ~` = "how big is my home folder, in one line, in units I can read."

**Q19.** one command's **output**.
Command substitution: the shell runs `which ls`, takes its output
(`/usr/bin/ls`), and pastes it into the outer command line.

**Q20.** the **shebang**.
`#!/bin/bash` names the interpreter. Without it, the system has to guess
what language the file is written in.

**Q21.** **`chmod +x`** `script.sh`; the letter **`x`** appears (e.g.
`-rw-rw-r--` becomes `-rwxrwxr-x`).

**Q22.** **`-o <filename>`** (e.g. `curl -o page.html https://example.com`).
The bytes go to the file; a progress meter appears on screen instead of the
content.

**Q23.** the **background**; the process's **PID** (process ID).
`[1]` is the shell's job number; `7412` is the system-wide PID — the number
`kill` needs.

**Q24.** **`PATH`**; glued with **`:`** (colon).
`echo $PATH` shows something like `/usr/local/bin:/usr/bin:/bin:...` — the
shell's search list, consulted left to right.

**Q25.** `ls /usr/bin | **wc -l**`.
Each name in `/usr/bin` is a command; `wc -l` counts the lines and therefore
the commands.

---

# Part C — Scenario Questions

**Q26. Pavan's Permission denied.**
`bash script.sh` works because the running program is *bash* and the script
is just a file it reads — no execute permission needed to read a file.
`./script.sh` asks Linux to run the file itself as a program, and Linux
checks its execute bit first; a fresh file has `-rw-rw-r--` — no `x`
anywhere — so the door is locked. The one-time fix is `chmod +x script.sh`.
He can watch the change: `ls -l script.sh` before (`-rw-rw-r--`) and after
(`-rwxrwxr-x`) — three `x`s appear, and the name may even turn green.

**Q27. Sunitha's un-runnable machine code.**
Ubuntu's rule has one clause: a file may run **iff its execute permission is
set**. The name — extension included — is ignored completely. `chmod -x`
flipped that one switch, so Linux refuses to run the file even though every
byte of machine code inside is intact (`file sum` still says executable);
`chmod +x sum` flips it back. This also answers her second question: `clang`
produced a runnable `sum` with no extension because clang simply *set the
`x` bit* when it wrote the file. Windows: name ends in `.exe` → runnable.
Ubuntu: `x` permission on the file → runnable.

**Q28. Rajesh and `clear`.**
`clear` deletes nothing — it only gives a fresh screen to work on; all the
old output is still in the terminal's scrollback. He can prove it in two
seconds: run `clear`, then scroll up (mouse wheel or the terminal's
scrollbar) — every earlier result is still there to copy. The worksheet
notes exactly this in Sub-task 1.

**Q29. Manasa's not-quite-empty room.**
Plain `ls` hides files whose names start with a dot, so the folder almost
certainly contains hidden files — something like `.secret`. `ls -a emptyish`
reveals everything, including the two entries present in *every* directory:
`.` (this folder itself) and `..` (its parent) — the same dots she uses in
relative paths, literally listed as entries. `rmdir` was telling the truth;
`ls` was only telling part of it. Once she removes the hidden file, `rmdir`
will succeed.

**Q30. Harini's three sleepers.**
First list them: `ps` shows her processes — three `sleep` rows, each with
its PID (she also saw each PID at launch, in the `[N] <pid>` lines). Then
stop each: `kill <pid1> <pid2> <pid3>` (or one `kill` per PID) — the default
TERM signal asks each `sleep` to exit, and it obeys; the shell prints
`[N]+ Terminated sleep 300` for each. Afterwards `ps` shows no `sleep`
rows. A mistyped, non-existent PID gets `kill: (99999) - No such process` —
you can only signal something actually running (and, as a normal user, only
processes you own).

**Q31. Two `ls` in PATH.**
The shell searches PATH's folders left to right and runs the **first** match
it finds — the second `ls` is simply never reached. That ordering is why
installing a custom tool into `/usr/local/bin` works so smoothly: in the
standard PATH, `/usr/local/bin` comes *before* `/usr/bin`, so the custom
version shadows the system one without deleting or modifying it. `which ls`
always tells you which copy won.

**Q32. Varun's one-line team.**
The missing piece is `-e`: without it, `\n` is just two literal characters.
Corrected: `echo -e "Ravi\nSita\nKiran\nGeetha"` — `-e` makes echo interpret
escapes, so each `\n` becomes a real newline and each name lands on its own
line. Proof: `echo -e "Ravi\nSita\nKiran\nGeetha" | wc -l` must print `4`,
his team size — the same self-check the mission prescribes.

**Q33. The three numbers of `time`.**
`real` is wall-clock time: how long from Enter to prompt. `user` is CPU time
spent running the program's own code; `sys` is CPU time the kernel spent
working on the program's behalf. `sleep 2` waits without computing, so the
wall clock advances 2 seconds while the CPU spends almost nothing — waiting
is free, working is not. A command that *computes* hard — say, compressing a
big file or a tight counting loop — shows the opposite signature: `user`
close to (or, on multiple cores, even above) `real`.
