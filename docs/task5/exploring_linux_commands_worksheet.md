# Exploring Linux Commands — The Shell Toolbox

A lab built around *typing commands and reading what comes back*. The rule for
the whole sheet: **don't memorise a command — run it, read its output, then
read its man page.**

All commands are for the **Ubuntu shell**. Nothing here needs a C compiler —
this is about becoming comfortable in the shell itself.

## The explorer's loop (keep this in front of you)

```
 hear about a command
   │
   ▼
 man <command>          ← read what it claims to do (q to leave)
   │
   ▼
 run it plain           ← no options, just the command
   │
   ▼
 run it with options    ← -a, -l, ... one at a time
   │
   ▼
 say what changed       ← out loud, in your own words
```

Do this loop for **every** command on this sheet. The habit matters more than
any single command.

---

# Sub-task 1 — `man man`: the manual about the manual

### a. What we set up

Every command on a Linux machine ships with a **manual page** ("man page").
The command `man` opens it. So `man man` opens *the manual of the manual
system itself* — the first page every explorer should read.

```
man man
```

Inside the pager: **arrow keys / space** to scroll, **`/word`** to search,
**`n`** for the next match, **`q`** to quit.

### b. Task

1. Run `man man`. Find the **NAME** and **SYNOPSIS** sections at the top.
2. Search inside it: type `/section` and press `n` a few times. Find the table
   that lists the manual **sections** (1 = user commands, 2 = system calls, ...).
3. Every man page follows the same skeleton. Open two more and confirm:
   ```
   man ls
   man date
   ```
   In each, locate: NAME, SYNOPSIS, DESCRIPTION, and the options list.
4. Run `clear` afterwards to wipe the screen clean.
   **Note:** `clear` doesn't delete anything — scroll up and your history is
   still there. It only gives you a fresh screen to work on.

### c. Observation (what you should find)

- `man man` says man pages are divided into numbered **sections**; user
  commands like `ls` live in section 1 (that's why the page header says
  `LS(1)`).
- Every page has the same skeleton — once you can read one man page, you can
  read them all.
- In the SYNOPSIS, things in `[square brackets]` are **optional**; `...` means
  "repeatable". `ls [OPTION]... [FILE]...` reads as: options optional, files
  optional, both repeatable.

**Takeaway to say out loud:** the manual is *on the machine*, always, even
without internet. `man <command>` first; searching the web second.

---

# Sub-task 2 — Who am I, who else is here, what is this machine?

### a. What we set up

Three identity questions, three commands:

| Question | Command |
|----------|---------|
| Which user account am I using right now? | `whoami` |
| Who (else) is logged into this machine? | `who` |
| What machine / OS / kernel is this? | `uname -a` |

### b. Task

1. Run `whoami`. One word comes back — whose name is it?
2. Run `who`. How many rows? Find your own row — it shows your terminal and
   login time.
3. Run `uname` plain, then `uname -a`. Compare the two outputs.
4. Read `man uname` and decode each field of `uname -a`: kernel name, hostname,
   kernel release, kernel version, machine hardware name, operating system.
5. Try `uname -r` and `uname -m` alone — match them to the fields you decoded.

### c. Observation (what you should find)

- `whoami` prints your **username** — the account the shell is acting as.
  Every file you create will be owned by this name.
- `who` can show *more than one* person: Linux is a **multi-user** system;
  several people can be logged into one machine at once (each row = one
  login session).
- `uname` alone says just `Linux`. `-a` (**a**ll) expands to the full identity
  card: e.g. `Linux mypc 6.8.0-49-generic ... x86_64 GNU/Linux` — kernel
  release `6.8.0-49`, hardware `x86_64` (a 64-bit Intel/AMD CPU).

**Takeaway to say out loud:** `whoami` = my account, `who` = everyone logged
in, `uname -a` = the machine's identity card. First things to check on *any*
unfamiliar machine.

---

# Sub-task 3 — Where do commands live? `which`, `whereis`

### a. What we set up

When you type `ls`, the shell doesn't magically know it — it **finds an
executable file** named `ls` and runs it. Two commands reveal the hiding
places:

```
which ls        # the exact executable the shell will run
whereis ls      # executable + man page + source locations
```

### b. Task

1. Run `which` on commands you already use:
   ```
   which ls
   which clang
   which man
   which which
   ```
2. Run `whereis ls` and compare with `which ls` — what *extra* paths appear?
3. Try a command that doesn't exist: `which blahblah`. What comes back?
4. Look at one of the answers directly:
   ```
   file $(which ls)
   ```
   **Note:** `$(...)` pastes one command's output into another — so this is
   `file /usr/bin/ls`.

### c. Observation (what you should find)

- Every command is **just an executable file**, usually in `/usr/bin`. Even
  `which` itself lives there — `which which` answers `/usr/bin/which`.
- `whereis` shows more than `which`: alongside `/usr/bin/ls` you also get
  `/usr/share/man/man1/ls.1.gz` — the compressed man page you read in
  Sub-task 1. Now you know where `man` gets its text from!
- A missing command returns *nothing* (and a non-zero exit) — that's exactly
  the situation behind `command not found`.
- `file $(which ls)` reports an **ELF 64-bit executable** — the same kind of
  file you produce when you compile your own C programs. `ls` is a compiled C
  program somebody else wrote.

**Takeaway to say out loud:** commands are files. `which` tells you *which
file* will run; `whereis` also finds its manual. (Where does the shell *search*
for these files? Hold that thought — Mission 2 at the bottom answers it.)

---

# Sub-task 4 — `echo`, `date`, and `time`: printing and clocks

### a. What we set up

- `echo` prints its arguments back to the screen — the shell's `printf`.
- `date` prints the current date and time.
- `time <command>` runs any command and reports **how long it took**.

### b. Task

1. Play with `echo`:
   ```
   echo hello
   echo hello        world
   echo "hello        world"
   ```
   Look carefully at the spaces in the two outputs.
2. `echo` can print special characters, but only if you ask with `-e`:
   ```
   echo "a\nb"
   echo -e "a\nb"
   ```
   Check `-e` and the meaning of `\n` in `man echo`.
3. Run `date`. Then ask it for a custom format:
   ```
   date "+%Y-%m-%d"
   date "+%H:%M:%S"
   ```
   Find `%Y`, `%m`, `%d`, `%H` in `man date`.
4. Time something:
   ```
   time ls
   time sleep 2
   ```
   **Note:** `sleep 2` is a command that does *nothing* for 2 seconds — perfect
   for testing `time`.

### c. Observation (what you should find)

- `echo hello        world` collapses the gap to **one space** — the *shell*
  splits your line into words before `echo` ever sees it. Quotes `"..."`
  protect the spacing because the shell hands the whole string over as **one**
  argument.
- Without `-e`, `\n` is just two characters; with `-e`, it becomes a real
  **newline** and the output splits into two lines. Remember this — the final
  mission uses it.
- `date "+..."` lets you build any date format from `%` codes — this is how
  scripts put timestamps into file names.
- `time sleep 2` reports `real` ≈ 2 seconds. `real` = wall-clock time;
  `user`/`sys` = CPU time actually spent working (nearly 0 for sleep — it
  waited, it didn't work).

**Takeaway to say out loud:** `echo` shows you *what the shell did to your
line* before running it — the best tool for debugging quoting. `date` reads
the clock; `time` measures a command with the clock.

---

# Sub-task 5 — Files you can't see, sizes, and empty rooms: `touch`, `ls -a`, `du`, `rmdir`

### a. What we set up

Build a small practice area:

```
cd ~
mkdir shell_lab
cd shell_lab
```

Four tools:

- `touch <name>` — create an empty file (or update a file's timestamp).
- `ls -a` — list **a**ll files, *including hidden ones*.
- `du` — **d**isk **u**sage: how much space files/folders take.
- `rmdir` — remove an **empty** directory (and only an empty one).

### b. Task

1. Create files, one of them "hidden":
   ```
   touch notes.txt
   touch .secret
   ls
   ls -a
   ```
   Compare the two listings. What starts every hidden file's name?
2. Go home and look for the hidden world that was always there:
   ```
   cd ~
   ls -a
   ```
   Count how many `.something` entries your home folder has. What are `.` and
   `..` in the listing? (You met them in the paths task.)
3. `touch` an *existing* file and watch the timestamp move:
   ```
   cd ~/shell_lab
   ls -l notes.txt
   touch notes.txt
   ls -l notes.txt
   ```
4. Measure disk usage:
   ```
   du ~/shell_lab
   du -h ~/shell_lab
   du -sh ~
   ```
   Check `-h` and `-s` in `man du`.
5. Try to remove directories:
   ```
   mkdir emptyroom
   rmdir emptyroom
   mkdir fullroom
   touch fullroom/thing.txt
   rmdir fullroom
   ```
   Read the error on the last one carefully.

### c. Observation (what you should find)

- Files whose names start with a **dot** are hidden from plain `ls`; `ls -a`
  reveals them. Your home folder is *full* of them (`.bashrc`, `.profile`,
  ...) — programs keep their settings there, out of sight.
- `.` (this folder) and `..` (parent) appear in every `ls -a` — the same dots
  you used in relative paths are literally listed as entries.
- `touch` on an existing file changes only its **modification time** — the
  contents are untouched. (Remember Task-6 Q2: timestamps tell you which file
  is newer. `touch` is how you move that clock by hand.)
- `du -h` prints **h**uman sizes (`4.0K`, `1.2M`) instead of raw block counts;
  `-s` gives one **s**ummary line instead of every subfolder.
- `rmdir fullroom` fails: `Directory not empty`. `rmdir` is the *safe* remover
  — it refuses unless the room is already empty. That refusal is a feature,
  not a bug.

**Takeaway to say out loud:** the dot prefix hides a file, `ls -a` unhides
everything, `du -h` says how big, and `rmdir` only demolishes empty rooms —
it can never eat your work.

---

# Sub-task 6 — `chmod` and your first shell script

### a. What we set up

A **shell script** is a text file full of shell commands — the shell runs them
top to bottom, exactly as if you typed them. Create this file in
`~/shell_lab` (use `nano script.sh` or any editor):

```bash
#!/bin/bash
# script.sh — my first script
clear
echo "Report generated on:"
date
echo "Running as user:"
whoami
echo "This machine is:"
uname -a
echo "Disk used by my lab:"
du -sh ~/shell_lab
```

The first line `#!/bin/bash` (the **shebang**) tells Linux which program
should interpret the file. Lines starting with `#` are comments.

### b. Task

1. Run it the "training wheels" way — hand the file to bash yourself:
   ```
   bash script.sh
   ```
2. Now try to run it like a real command:
   ```
   ./script.sh
   ```
   Read the error: `Permission denied`.
3. Look at the file's **permissions**:
   ```
   ls -l script.sh
   ```
   You'll see something like `-rw-rw-r--`. Note there is **no `x`** anywhere.
4. Grant the e**x**ecute permission, then look again, then run again:
   ```
   chmod +x script.sh
   ls -l script.sh
   ./script.sh
   ```
5. Take the permission away and confirm the door locks again:
   ```
   chmod -x script.sh
   ./script.sh
   ```
   Then give it back (`chmod +x script.sh`) — you'll want it working.
6. Skim `man chmod` — find what `r`, `w`, `x` stand for.

### c. Observation (what you should find)

- `bash script.sh` works even *without* execute permission — there, **bash**
  is the program running, and `script.sh` is merely a file it reads.
- `./script.sh` asks Linux to run the file *as a program* — and Linux checks
  the file's **execute bit** first. No `x`, no entry: `Permission denied`.
- After `chmod +x`, `ls -l` shows `-rwxrwxr-x` — three `x`s appeared, and the
  file's name may even turn green in the listing. Now `./script.sh` runs.
- The script's output is exactly what typing those commands one-by-one would
  produce — a script is *recorded typing*.
- `r` = read, `w` = write, `x` = execute. Permissions are per-file switches;
  `chmod` flips them.

**Takeaway to say out loud:** two ways to run a script — `bash script.sh`
(bash reads a file) and `./script.sh` (the file itself is a program, needs
`chmod +x` once). This is how *every* command-line tool you'll ever install
became runnable.

### d. Extra task — `chmod` works on your compiled C programs too

You just saw `chmod` control a *script*. Does it also control a *compiled*
program — real machine code? Test it on your own `sum.c` from the earlier
task.

1. Copy your `sum.c` into the current folder (adjust the source path to
   wherever yours lives):
   ```
   cp ~/path/to/your/sum.c .
   ```
2. Compile it and run it — this works, as always:
   ```
   clang sum.c -o sum
   ./sum
   ```
3. Now take away the execute permission from the compiled program, look at it,
   and try to run it again:
   ```
   chmod -x sum
   ls -l sum
   ./sum
   ```
4. Observe the issue, then repair it and confirm:
   ```
   chmod +x sum
   ./sum
   ```

**What you should find:** step 3 fails with `Permission denied` — the *same*
error the script gave. The file is still perfectly good machine code (`file
sum` still says it's an executable, and not a single byte inside it changed) —
yet Linux refuses to run it.

**The explanation, short and clear:** on Windows, a file is runnable because
its *name* ends in `.exe`. **Ubuntu does not care about file extensions at
all.** Whether a file may run is decided by exactly one thing: the **execute
permission (`x`) on that file**. That is why:

- `clang ... -o sum` produced a runnable program *with no extension* — clang
  simply set the `x` bit when it wrote the file;
- `chmod -x` made real machine code un-runnable without touching its contents;
- `chmod +x` made it runnable again;
- and your `script.sh` needed `chmod +x` even though its name already "looked
  runnable".

```
 Windows:  name ends in .exe ─────────► runnable
 Ubuntu :  name  (ignored!)
           x permission on the file ──► runnable
```

**Takeaway to say out loud:** in Ubuntu, "executable" is a **permission**, not
a file-name ending. `chmod` flips that permission on any file — script or
compiled program alike.

---

# Sub-task 7 — `curl`: the shell talks to the internet

### a. What we set up

`curl` fetches things over the network — it is a web browser with no screen:
you give it a URL, it prints what the server sent.

### b. Task

1. Ask a friendly test server to echo back who you are:
   ```
   curl https://ifconfig.me
   ```
   (Prints your public IP address, then your prompt.)
2. Fetch a tiny page of text:
   ```
   curl https://example.com
   ```
   Read the output — what language is it written in?
3. Ask for weather in the terminal (a service made for curl):
   ```
   curl https://wttr.in/Vijayawada
   ```
4. Save instead of print — download to a file:
   ```
   curl -o page.html https://example.com
   ls -l page.html
   ```
   Find `-o` in `man curl` (the manual is huge — use `/-o` to search).

### c. Observation (what you should find)

- `curl https://example.com` prints raw **HTML** — the same text your browser
  receives, but without drawing it. A browser = curl + rendering.
- `wttr.in` proves servers can send *anything* as text — even a weather
  report drawn with characters.
- `-o page.html` sends the bytes to a file instead of the screen; the download
  progress meter appears instead of the content.
- `curl` is how scripts fetch things: put a `curl` line inside `script.sh` and
  your script now uses the internet.

**Takeaway to say out loud:** the network is reachable from the shell with one
word. `curl <url>` fetches; `-o` saves. What browsers hide, curl shows raw.

---

# Sub-task 8 — `kill`: stopping a process on purpose

### a. What we set up

Every running program is a **process** with a number — the **PID** (process
ID). You saw one in Task-6 (`Process 4821 stopped`). `kill` sends a *signal*
to a process by its PID — usually the signal that asks it to terminate.

We need a harmless victim. `sleep 300` (does nothing for 5 minutes) is
perfect. The `&` at the end starts it **in the background** so you get your
prompt back:

```
sleep 300 &
```

### b. Task

1. Start the victim and note the PID the shell prints:
   ```
   sleep 300 &
   ```
   Output looks like `[1] 7412` — the second number is the PID.
2. See it running:
   ```
   ps
   ```
3. Kill it by PID (use *your* number, not 7412):
   ```
   kill 7412
   ps
   ```
4. Do it once more, and this time also try killing a PID that doesn't exist:
   ```
   kill 99999
   ```
   Read the error.
5. Skim `man kill` — find the list of signals (`kill -l` prints them too).
   Spot signal `9` (`KILL`) — the "no arguments, terminate now" signal, used
   as `kill -9 <pid>` only when a process ignores the polite request.

### c. Observation (what you should find)

- The shell tracks background jobs: `[1] 7412` = job number 1, PID 7412.
- `ps` lists your processes — `sleep` sits there with the same PID.
- After `kill 7412`, `ps` no longer shows it; the shell prints
  `[1]+ Terminated  sleep 300`. The default signal (`TERM`) *asks* the
  process to exit, and `sleep` obeys.
- Killing a non-existent PID gives `No such process` — you can only signal
  something that is actually running, and (as a normal user) only processes
  that **you** own.
- `kill -9` exists for stuck processes — but always try plain `kill` first;
  `-9` gives the program no chance to clean up.

**Takeaway to say out loud:** `kill` doesn't "kill" so much as *send a
message*; the usual message is "please exit." PID is the address the message
is sent to. This is your tool when a program hangs.

---

# Final Missions — know your system

Three small investigations. Use `man`, use the commands above, write down what
you find.

## Mission 1 — Set the time-zone of your Ubuntu machine

1. First *look* before touching anything:
   ```
   timedatectl
   ```
   Read every line: local time, universal time (UTC), and the current
   `Time zone`.
2. List what time-zones exist (it's a long list — `q` quits the pager):
   ```
   timedatectl list-timezones
   timedatectl list-timezones | grep Asia
   ```
3. Set it (needs administrator rights, hence `sudo`):
   ```
   sudo timedatectl set-timezone Asia/Kolkata
   ```
4. Verify with *both* tools:
   ```
   timedatectl
   date
   ```
   `date` should now print `IST` and the correct local time.

*To answer in your notebook:* what is the difference between the `Local time`
and `Universal time` lines? Why do servers often stay on UTC?

## Mission 2 — Find ALL the commands on the machine, using the system PATH

Sub-task 3 left a question open: *where does the shell search* when you type a
command? Answer: a list of folders stored in the variable `PATH`.

1. Print it:
   ```
   echo $PATH
   ```
   You'll see folders glued together with `:` —
   e.g. `/usr/local/bin:/usr/bin:/bin:...`
2. Look inside the busiest one:
   ```
   ls /usr/bin
   ```
   That flood of names? **Each one is a command.**
3. Count the commands in each PATH folder:
   ```
   ls /usr/bin | wc -l
   ls /usr/local/bin | wc -l
   ls /bin | wc -l
   ```
4. Cross-check with Sub-task 3: run `which` on any name you spotted in
   `/usr/bin` — the answer should be that very folder.

*To answer in your notebook:* roughly how many commands does your machine
have? Why does `which ls` search PATH folders *in order* — what would happen
if two folders both contained an `ls`?

## Mission 3 — One `echo` line: every team member's name on its own line

Write a **single** `echo` command that prints each of your team members'
names, one per line, like:

```
Ravi
Sita
Kiran
Geetha
```

You already met the two ingredients in Sub-task 4: the `-e` option and `\n`.
One possible shape (fill in your real team):

```
echo -e "Ravi\nSita\nKiran\nGeetha"
```

*To check yourself:* count the lines of your own output:

```
echo -e "Ravi\nSita\nKiran\nGeetha" | wc -l
```

The number must equal your team size. Bonus: add the line to `script.sh` under
a heading `echo "My team:"` — now your script introduces your whole team.

---

## One-page command reference

| Goal                                     | Command                         |
|------------------------------------------|---------------------------------|
| Read any command's manual                | `man <command>` (quit with `q`) |
| Clean the screen                         | `clear`                         |
| My username                              | `whoami`                        |
| Everyone logged in                       | `who`                           |
| Machine / kernel identity card           | `uname -a`                      |
| Where a command's executable is          | `which <command>`               |
| Executable + man page locations          | `whereis <command>`             |
| Print text                               | `echo "text"`                   |
| Print with `\n` as real newlines         | `echo -e "a\nb"`                |
| Current date & time                      | `date`                          |
| How long a command takes                 | `time <command>`                |
| Create empty file / bump timestamp       | `touch <file>`                  |
| List ALL files (hidden too)              | `ls -a`                         |
| Disk usage, human sizes, summary         | `du -sh <folder>`               |
| Remove an EMPTY directory                | `rmdir <folder>`                |
| Make a script runnable                   | `chmod +x script.sh`            |
| Run a script (two ways)                  | `bash script.sh` / `./script.sh`|
| Fetch a URL / save it                    | `curl <url>` / `curl -o f <url>`|
| Stop a process by PID                    | `kill <pid>`                    |
| See/set the time-zone                    | `timedatectl`                   |
| The shell's command search list          | `echo $PATH`                    |

**The habit to remember:** `man` it → run it plain → add one option → say what
changed.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English word | తెలుగు అర్థం |
|--------------|--------------|
| **manual / man page** | మాన్యువల్ పేజీ — ప్రతి కమాండ్‌తో పాటు యంత్రంలోనే ఉండే వాడుక పుస్తకం; `man` తో తెరుస్తాం. |
| **pager** | పేజర్ — పొడవైన పాఠ్యాన్ని పేజీ-పేజీగా చూపించే ప్రోగ్రామ్ (బాణాలు = స్క్రోల్, `q` = బయటికి). |
| **section** | విభాగం — మాన్యువల్ లోని సంఖ్యా భాగాలు (1 = యూజర్ కమాండ్లు, 2 = system calls, ...). |
| **multi-user** | బహుళ-వినియోగదారు — ఒకే యంత్రాన్ని ఒకేసారి చాలా మంది వాడగల ఏర్పాటు. |
| **username / user account** | వినియోగదారు పేరు / ఖాతా — shell ఎవరి పేరుతో పనిచేస్తుందో ఆ పేరు (`whoami`). |
| **kernel** | కెర్నల్ — ఆపరేటింగ్ సిస్టమ్ యొక్క గుండె; హార్డ్‌వేర్‌ను, ప్రాసెస్‌లను నడిపే మూల ప్రోగ్రామ్. |
| **hostname** | హోస్ట్ పేరు — నెట్‌వర్క్‌లో ఈ యంత్రం పేరు. |
| **hidden file** | దాచిన ఫైల్ — పేరు చుక్క (`.`)తో మొదలయ్యే ఫైల్; మామూలు `ls` లో కనిపించదు, `ls -a` లో కనిపిస్తుంది. |
| **timestamp** | కాల ముద్ర — ఫైల్ ఎప్పుడు మార్చబడిందో చెప్పే సమయం; `touch` దీన్ని తాజాగా మారుస్తుంది. |
| **disk usage** | డిస్క్ వినియోగం — ఫైళ్ళు/ఫోల్డర్లు ఎంత స్థలం తీసుకుంటున్నాయో (`du`). |
| **permission** | అనుమతి — ఫైల్‌పై ఎవరు ఏమి చేయవచ్చో చెప్పే స్విచ్‌లు: `r` చదవడం, `w` రాయడం, `x` నడపడం. |
| **execute bit** (`x`) | నడిపే అనుమతి — ఫైల్‌ను ప్రోగ్రామ్‌గా నడపడానికి Linux చూసే అనుమతి; `chmod +x` తో ఇస్తాం. |
| **shell script** | షెల్ స్క్రిప్ట్ — shell కమాండ్లు వరుసగా రాసి ఉంచిన పాఠ్య ఫైల్; shell దాన్ని పై నుండి క్రిందికి నడుపుతుంది. |
| **shebang** (`#!/bin/bash`) | షీబాంగ్ — స్క్రిప్ట్ మొదటి పంక్తి; ఈ ఫైల్‌ను ఏ ప్రోగ్రామ్ నడపాలో Linux కు చెబుతుంది. |
| **comment** (`#`) | వ్యాఖ్య — ప్రోగ్రామ్ నడవడానికి కాక, చదివే మనుషుల కోసం రాసే పంక్తి. |
| **URL** | యూఆర్ఎల్ — ఇంటర్నెట్‌లో ఒక వనరు చిరునామా (ఉదా: `https://example.com`). |
| **download** | దింపుకోవడం — నెట్‌వర్క్ నుండి బైట్లను తెచ్చి ఫైల్‌గా దాచడం (`curl -o`). |
| **process** | ప్రక్రియ — నడుస్తున్న ప్రోగ్రామ్; ప్రతి దానికి ఒక సంఖ్య (PID) ఉంటుంది. |
| **PID (process ID)** | ప్రాసెస్ సంఖ్య — నడుస్తున్న ప్రతి ప్రోగ్రామ్‌కు ఇచ్చే ప్రత్యేక సంఖ్య; `kill` కు ఇదే చిరునామా. |
| **background job** (`&`) | వెనుక పని — ప్రాంప్ట్‌ను వదలకుండా, తెర వెనుక నడిచే కమాండ్ (`sleep 300 &`). |
| **signal** | సంకేతం — ఒక ప్రాసెస్‌కు పంపే చిన్న సందేశం; `kill` పంపే మామూలు సందేశం "దయచేసి ఆగిపో" (TERM). |
| **time-zone** | కాల మండలం — స్థానిక సమయం లెక్కించే ప్రాంతం (ఉదా: `Asia/Kolkata`). |
| **UTC / universal time** | సార్వత్రిక సమయం — ప్రపంచమంతా ఒప్పుకున్న ప్రామాణిక గడియారం; time-zone లు దీని నుండి తేడాగా లెక్కిస్తారు. |
| **environment variable** | పరిసర చరరాశి — shell దగ్గర ఉండే పేరు-విలువ జత (ఉదా: `PATH`); `echo $పేరు` తో చూస్తాం. |
| **PATH** | పాత్ — కమాండ్ కోసం shell వెతికే ఫోల్డర్ల జాబితా; `:` తో అతికించి ఉంటుంది. |
| **administrator / sudo** | నిర్వాహకుడు — యంత్ర సెట్టింగులు మార్చగల అధికారం; `sudo` ఆ అధికారంతో ఒక కమాండ్ నడుపుతుంది. |
