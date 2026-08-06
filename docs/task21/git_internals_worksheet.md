# Git Internals — Inside the `.git` Folder

**Goal.** You have used `git add` and `git commit` before. Today you will open the box. You will see exactly which files `git init` creates, where your name and email are stored, watch objects appear inside `.git/objects/` one by one as you commit, and then write **hooks** — small scripts Git runs for you at fixed moments, including one that refuses a bad commit.

**You need:** your Linux VM (WSL), a terminal, `git`, a notebook, and a pencil.

Check Git is there:

```
git --version
```

You should see something like `git version 2.43.0`. Any `2.x` is fine; a few file names differ slightly between versions, and the worksheet says so where it matters.

Make a folder for today:

```
mkdir -p ~/task21
cd ~/task21
```

> **The golden rule of Git**
> `.git` is not magic and it is not a black box — it is a folder of ordinary files. Every version of every file you commit is saved there as an **object**, and each object's name is a **hash of its own contents**. Everything else — branches, `HEAD`, the staging area — is just a small file holding one of those names.

*(A note on paths: everything today happens inside your own `~/task21` folder. Nothing here touches the internet, and nothing here can damage another repository.)*

---

## Iteration 1 — What `git init` actually creates

**a. What we set up**

Nothing yet. Just an empty folder and one command.

**b. Task**

Before running, write down your guess: how many files and folders does `git init` create? One? Five? Twenty?

```
cd ~/task21
git init demo
cd demo
ls -a
```

Then look inside:

```
find .git | sort
```

**c. Observation (what you should find)**

The `git init` command says this:

```
hint: Using 'master' as the name for the initial branch. This default branch name
hint: is subject to change. To configure the initial branch name to use in all
hint: of your new repositories, which will suppress this warning, call:
hint:
hint: 	git config --global init.defaultBranch <name>
hint:
hint: Names commonly chosen instead of 'master' are 'main', 'trunk' and
hint: 'development'. The just-created branch can be renamed via this command:
hint:
hint: 	git branch -m <name>
Initialized empty Git repository in /home/student/task21/demo/.git/
```

Keep that hint in mind — Iteration 2 will make it disappear.

`ls -a` shows only one new thing:

```
.  ..  .git
```

**Everything Git knows lives inside that one folder.** Delete `.git` and you have an ordinary folder of files again, with no history.

`find .git | sort` shows what is inside:

```
.git
.git/HEAD
.git/branches
.git/config
.git/description
.git/hooks
.git/hooks/applypatch-msg.sample
.git/hooks/commit-msg.sample
.git/hooks/fsmonitor-watchman.sample
.git/hooks/post-update.sample
.git/hooks/pre-applypatch.sample
.git/hooks/pre-commit.sample
.git/hooks/pre-merge-commit.sample
.git/hooks/pre-push.sample
.git/hooks/pre-rebase.sample
.git/hooks/pre-receive.sample
.git/hooks/prepare-commit-msg.sample
.git/hooks/push-to-checkout.sample
.git/hooks/sendemail-validate.sample
.git/hooks/update.sample
.git/info
.git/info/exclude
.git/objects
.git/objects/info
.git/objects/pack
.git/refs
.git/refs/heads
.git/refs/tags
```

*(The exact list of `.sample` files varies a little between Git versions, and `branches/` is gone in Git 2.45 and newer. Do not memorise the list — memorise the four important entries below.)*

Here is what matters:

| Entry | What it is |
|---|---|
| `HEAD` | a **text file** saying which branch you are on |
| `config` | this repository's own settings |
| `objects/` | where every version of every file will be stored — **empty right now** |
| `refs/heads/` | one small file per branch — **empty right now** |
| `hooks/` | scripts Git will run at fixed moments (Iteration 6) |
| `description` | only used by an old web tool; ignore it |
| `info/exclude` | like `.gitignore`, but private to you and not committed |

Now read `HEAD` — it really is just text:

```
cat .git/HEAD
```
```
ref: refs/heads/master
```

It points at `refs/heads/master`. But look again at the `find` output: **there is no file `refs/heads/master`.** `HEAD` is pointing at a branch that does not exist yet. That is exactly what "no commits yet" means.

And `config`:

```
cat .git/config
```
```
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
```

Four lines. No name, no email — Git has not saved anything about *you* here.

Two more things are **not** there yet. Check:

```
ls .git/index
find .git/objects -type f | wc -l
find .git/refs -type f | wc -l
```
```
ls: cannot access '.git/index': No such file or directory
0
0
```

No staging area, no objects, no branches. A fresh repository is almost entirely empty.

**Takeaway to say out loud:** "`git init` makes one folder, `.git`, and it starts out nearly empty — no objects, no branches, not even an index."

---

## Iteration 2 — Where your settings are stored

Git needs to know your name and email to record a commit. Where does it keep them?

**a. What we set up**

Git reads settings from **three files**, in this order:

| Level | File | Applies to |
|---|---|---|
| system | `/etc/gitconfig` | every user on the machine |
| **global** | `~/.gitconfig` | **you**, in every repository |
| local | `.git/config` | this one repository only |

Later beats earlier: **local wins over global, and global wins over system.**

**b. Task**

First check whether the system file even exists, and what Git can see right now:

```
ls -l /etc/gitconfig
git config --list --show-origin
```

Then set your identity globally, and find the file it wrote:

```
git config --global user.name "Ravi Kumar"
git config --global user.email "ravi@example.com"
git config --global init.defaultBranch main

ls -a ~ | grep gitconfig
cat ~/.gitconfig
```

*(Use your **own** name and email, not Ravi's.)*

**c. Observation (what you should find)**

On Ubuntu there is usually no system file at all:

```
ls: cannot access '/etc/gitconfig': No such file or directory
```

and before you set anything, `--show-origin` lists only the repository's own four settings:

```
file:.git/config	core.repositoryformatversion=0
file:.git/config	core.filemode=true
file:.git/config	core.bare=false
file:.git/config	core.logallrefupdates=true
```

After the three `--global` commands, a new file has appeared **in your home folder** — not in the project:

```
.gitconfig
```
```
[user]
	name = Ravi Kumar
	email = ravi@example.com
[init]
	defaultBranch = main
```

That is all `--global` means: *write it to `~/.gitconfig`.* It is an ordinary text file you could have typed yourself.

Now `--show-origin` names the file each setting came from:

```
file:/home/student/.gitconfig	user.name=Ravi Kumar
file:/home/student/.gitconfig	user.email=ravi@example.com
file:/home/student/.gitconfig	init.defaultbranch=main
file:.git/config	core.repositoryformatversion=0
file:.git/config	core.filemode=true
file:.git/config	core.bare=false
file:.git/config	core.logallrefupdates=true
```

**Now prove that local wins.** Set a different email *without* `--global`:

```
git config user.email "ravi@school.edu"
cat .git/config
```
```
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
[user]
	email = ravi@school.edu
```

It went into `.git/config`, the repository's own file. Which one does Git actually use?

```
git config user.email
git config --show-origin user.email
git config --global user.email
```
```
ravi@school.edu
file:.git/config	ravi@school.edu
ravi@example.com
```

The local one wins here, and the global one is untouched — it still applies everywhere else. Remove the local override and the global comes back:

```
git config --unset user.email
git config user.email
```
```
ravi@example.com
```

Finally, start a fresh repository and watch the hint disappear, because `init.defaultBranch` is now set:

```
cd ~/task21
rm -rf demo
git init demo
cd demo
cat .git/HEAD
```
```
Initialized empty Git repository in /home/student/task21/demo/.git/
ref: refs/heads/main
```

No hint, and the branch is `main`. **Keep this new `demo` folder — the rest of the worksheet uses it.**

**Takeaway to say out loud:** "`--global` just writes to `~/.gitconfig`; the repo's own `.git/config` beats it."

---

## Iteration 3 — The first object appears (and it is not at commit time)

**a. What we set up**

One small file, in the `demo` folder from Iteration 2:

```
cd ~/task21/demo
printf 'hello\n' > hello.txt
```

Git names every object by a **hash** of its contents — a long hex string worked out from the bytes themselves. You can ask for that hash *before* Git stores anything:

```
git hash-object hello.txt
```

**b. Task**

Predict: does `git hash-object` create anything inside `.git/objects/`? Then check the count before and after each step below.

```
git hash-object hello.txt
find .git/objects -type f | wc -l

git add hello.txt
find .git/objects -type f
```

**c. Observation (what you should find)**

```
ce013625030ba8dba906f756967f9e9ca394464a
0
```

`hash-object` only *calculates* — it stores nothing. Still zero objects.

Then `git add`:

```
.git/objects/ce/013625030ba8dba906f756967f9e9ca394464a
```

**The object appeared at `git add`, before any commit.** And look at the path: the hash is `ce013625030ba8...`, and Git filed it as folder `ce` + file `013625030ba8...`. It splits off the first two characters as a folder name so no single folder ends up with a million files in it.

Your hash will be **exactly the same as the one printed here** — `ce0136…`. The hash comes from the content `hello\n` and nothing else: not your name, not the filename, not the time. Same content, same hash, on every machine in the world. That is what *content-addressed* means.

`git add` also created the staging area:

```
ls -l .git/index
git ls-files -s
```
```
-rw-rw-r-- 1 student student 104 Aug  6 10:00 .git/index
100644 ce013625030ba8dba906f756967f9e9ca394464a 0	hello.txt
```

So `.git/index` is a list saying "the file named `hello.txt` currently stages the object `ce0136…`".

Now try to read the object with `cat` — and remember Task 16:

```
cat .git/objects/ce/013625030ba8dba906f756967f9e9ca394464a
file .git/objects/ce/013625030ba8dba906f756967f9e9ca394464a
```
```
x^AKM-JM-IOR0cM-HHM-MM-IM-IM-g^B^@^]M-E^D^T
.git/objects/ce/013625030ba8dba906f756967f9e9ca394464a: zlib compressed data
```

Gibberish — because it is a **binary** file, compressed, exactly like the binary files in Task 16. `cat` is the wrong tool. Git gives you the right one:

```
git cat-file -t ce013625030ba8dba906f756967f9e9ca394464a
git cat-file -p ce013625030ba8dba906f756967f9e9ca394464a
```
```
blob
hello
```

`-t` shows the **type**, `-p` **prints** the contents. A **blob** is Git's word for "the contents of one file, with no name and no date attached." Just the bytes.

**Takeaway to say out loud:** "`git add` writes the blob. The hash comes from the content alone, so everyone gets the same one."

---

## Iteration 4 — The commit adds two more objects

**a. What we set up**

The staged `hello.txt` from Iteration 3, and one commit.

**b. Task**

Right now there is **1** object. Predict how many there will be after `git commit`. Write the number down before you run it.

```
git commit -m "Add hello"
find .git/objects -type f | sort
```

**c. Observation (what you should find)**

Three:

```
.git/objects/32/ac93555e79786deae14faa9dc6dfe1c509a43f
.git/objects/aa/a96ced2d9a1c8e72c56b253a0e2fe78393feb7
.git/objects/ce/013625030ba8dba906f756967f9e9ca394464a
```

Ask each one what it is:

```
git cat-file -t ce013625030ba8dba906f756967f9e9ca394464a
git cat-file -t aaa96ced2d9a1c8e72c56b253a0e2fe78393feb7
git cat-file -t 32ac93555e79786deae14faa9dc6dfe1c509a43f
```
```
blob
tree
commit
```

Those are Git's three object types, and one commit of one file needs all three. Print them:

```
git cat-file -p aaa96ced2d9a1c8e72c56b253a0e2fe78393feb7
```
```
100644 blob ce013625030ba8dba906f756967f9e9ca394464a	hello.txt
```

A **tree** is a folder listing: permissions, type, hash, and — at last — the **file name**. The blob never knew it was called `hello.txt`; the tree is what remembers that.

```
git cat-file -p 32ac93555e79786deae14faa9dc6dfe1c509a43f
```
```
tree aaa96ced2d9a1c8e72c56b253a0e2fe78393feb7
author Ravi Kumar <ravi@example.com> 1785990600 +0530
committer Ravi Kumar <ravi@example.com> 1785990600 +0530

Add hello
```

A **commit** is tiny: which tree is the snapshot, who made it, when, and the message. That is the whole thing.

> **Your commit hash will not be `32ac935…`, and that is correct.** Look at what is inside the commit: your name, your email, and the second you ran it. Different contents, different hash. But your **blob** `ce0136…` and your **tree** `aaa96ce…` *will* match, because those depend only on the file's content and name.

Now follow the chain from `HEAD` down to the file:

```
cat .git/HEAD
cat .git/refs/heads/main
```
```
ref: refs/heads/main
32ac93555e79786deae14faa9dc6dfe1c509a43f
```

So: **`HEAD` → `refs/heads/main` → the commit → the tree → the blob.** A branch is not a heavy thing. It is a 41-byte text file holding one hash. That is why making a branch in Git is instant.

Two more files appeared too:

```
find .git -type f | grep -v hooks | grep -v objects
```
```
.git/COMMIT_EDITMSG
.git/HEAD
.git/config
.git/description
.git/index
.git/info/exclude
.git/logs/HEAD
.git/logs/refs/heads/main
.git/refs/heads/main
```

`.git/logs/` is the **reflog** — a plain-text diary of every time a branch moved:

```
cat .git/logs/HEAD
```
```
0000000000000000000000000000000000000000 32ac93555e79786deae14faa9dc6dfe1c509a43f Ravi Kumar <ravi@example.com> 1785990600 +0530	commit (initial): Add hello
```

All zeros on the left means "there was nothing here before." This file is how `git reflog` can rescue work you thought you had lost.

**Takeaway to say out loud:** "One commit of one file makes three objects — blob, tree, commit — and a branch is just a file holding a hash."

---

## Iteration 5 — How `.git` fills up with many objects

**a. What we set up**

A second commit that adds a file **inside a folder**, leaving `hello.txt` completely untouched.

```
mkdir notes
printf 'first note\n' > notes/day1.txt
```

**b. Task**

There are 3 objects now. Predict how many after this next commit — and in particular, predict whether Git stores `hello.txt` a second time.

```
git add notes/day1.txt
git commit -m "Add a note"
find .git/objects -type f | wc -l
```

**c. Observation (what you should find)**

```
7
```

Four new objects. Here is the full set with their types:

```
ce013625030ba8dba906f756967f9e9ca394464a  blob     (hello\n          — from commit 1)
aaa96ced2d9a1c8e72c56b253a0e2fe78393feb7  tree     (root of commit 1)
32ac93555e79786deae14faa9dc6dfe1c509a43f  commit   (commit 1)
aa93d5bc06369541d7d1a7a6ad0a3975d17fb571  blob     (first note\n     — new)
3ef89f4f8cbcce64a779c8416faf59e5ac24f442  tree     (the notes/ folder — new)
1d817ca446a8b4e99142735f2d3031be6d657836  tree     (root of commit 2  — new)
c9b1a4c6ce0b282d51cf4d017171b7d749f68845  commit   (commit 2          — new)
```

Read the new commit:

```
git cat-file -p HEAD
```
```
tree 1d817ca446a8b4e99142735f2d3031be6d657836
parent 32ac93555e79786deae14faa9dc6dfe1c509a43f
author Ravi Kumar <ravi@example.com> 1785994200 +0530
committer Ravi Kumar <ravi@example.com> 1785994200 +0530

Add a note
```

A new line: **`parent`**, holding the hash of commit 1. *That* is the history — each commit naming the one before it. Nothing else links them.

Now the new root tree:

```
git cat-file -p HEAD^{tree}
```
```
100644 blob ce013625030ba8dba906f756967f9e9ca394464a	hello.txt
040000 tree 3ef89f4f8cbcce64a779c8416faf59e5ac24f442	notes
```

Two things to see here.

**First: a folder is just another tree.** `notes` has mode `040000` and type `tree`, pointing at its own listing:

```
git cat-file -p HEAD:notes
```
```
100644 blob aa93d5bc06369541d7d1a7a6ad0a3975d17fb571	day1.txt
```

**Second — the important one: `hello.txt` still points at `ce013625…`, the very same blob as commit 1.** Git did not store a second copy. It did not need to: the content did not change, so the hash did not change, so the object already on disk is the right one.

That answers "how does `.git` fill up?" Every commit writes:
- a **new blob** for each file whose content **changed** (unchanged files cost nothing),
- a **new tree** for each folder along the path to a change,
- exactly **one new commit** object.

Change one file deep inside a project and you get one blob, a handful of trees, and one commit — not a fresh copy of the whole project.

**Takeaway to say out loud:** "Same content, same hash, stored once. New commits only add what actually changed."

---

## Iteration 6 — Hooks: a script Git runs for you

**a. What we set up**

Remember `.git/hooks/` from Iteration 1. Look at it properly:

```
ls -1 .git/hooks/
head -5 .git/hooks/pre-commit.sample
```

**b. Task**

Predict: do those `.sample` files do anything right now?

Then write a hook of your own. The name of the file *is* the trigger point — a file called `pre-commit` runs before a commit is created:

```
nano .git/hooks/pre-commit
```

```sh
#!/bin/sh
echo "Hello from the pre-commit hook!"
exit 0
```

Make it executable, then commit something:

```
chmod +x .git/hooks/pre-commit
printf 'a\n' > a.txt
git add a.txt
git commit -m "Add a"
```

**c. Observation (what you should find)**

```
applypatch-msg.sample
commit-msg.sample
fsmonitor-watchman.sample
post-update.sample
pre-applypatch.sample
pre-commit.sample
pre-merge-commit.sample
pre-push.sample
pre-rebase.sample
pre-receive.sample
prepare-commit-msg.sample
push-to-checkout.sample
sendemail-validate.sample
update.sample
```

Every one ends in `.sample`, and **that is why none of them run.** Git looks for a hook named exactly `pre-commit`, not `pre-commit.sample`. They are switched-off examples; you turn one on by copying it to the name without `.sample`.

Your own hook does run:

```
Hello from the pre-commit hook!
[main a52c7d5] Add a
 1 file changed, 1 insertion(+)
 create mode 100644 a.txt
```

A hook is nothing more than **an executable script with the right name in `.git/hooks/`**. Two conditions, both required: the exact name, and `chmod +x`. Forget the `chmod` and Git silently ignores it — a very common mistake.

**Now the useful part.** Change the hook to refuse the commit:

```sh
#!/bin/sh
if git diff --cached --name-only | grep -q "secret"; then
    echo "BLOCKED: a file name contains 'secret'"
    exit 1
fi
exit 0
```

(`git diff --cached --name-only` lists the staged file names.) Try to commit a badly named file:

```
printf 'x\n' > secret_key.txt
git add secret_key.txt
git commit -m "Add secret"
```
```
BLOCKED: a file name contains 'secret'
```

Check that nothing happened:

```
git status --short
git log --oneline
```
```
A  secret_key.txt
a52c7d5 Add a
c9b1a4c Add a note
32ac935 Add hello
```

The file is still staged, and **no new commit exists** — the log still ends at "Add a". The whole rule is the **exit code**: `exit 0` means "carry on", any non-zero exit means "stop". That is the entire hook API.

**Takeaway to say out loud:** "A hook is an executable script named for its moment — `exit 0` allows, non-zero blocks."

---

## Iteration 7 — When exactly do the commit hooks fire?

**a. What we set up**

First tidy up after Iteration 6. That `secret_key.txt` is still staged — if you leave it there, the very next commit will sweep it in. Unstage it, delete it, and remove the old hook:

```
git restore --staged secret_key.txt
rm secret_key.txt
rm .git/hooks/pre-commit
```

Now four hooks that each announce themselves.

Then create all four, each containing its own name:

```sh
#!/bin/sh
echo "   >> pre-commit ran"
exit 0
```

…and the same for `prepare-commit-msg`, `commit-msg`, and `post-commit`. Remember `chmod +x` on all four.

**b. Task**

Predict the **order** they will print in. Then:

```
printf 'z\n' > order.txt
git add order.txt
git commit -m "Order test"
```

**c. Observation (what you should find)**

```
   >> pre-commit ran
   >> prepare-commit-msg ran
   >> commit-msg ran
   >> post-commit ran
[main 3e48745] Order test
 1 file changed, 1 insertion(+)
 create mode 100644 order.txt
```

The order, and what each moment is for:

| Hook | Fires | Good for | Can it block? |
|---|---|---|---|
| `pre-commit` | before the message is even asked for | checking the **code**: formatting, linting, secrets | **yes** |
| `prepare-commit-msg` | after the default message is written, before you edit it | **filling in** a message template | yes (rarely used to) |
| `commit-msg` | after the message is final | checking the **message** | **yes** |
| `post-commit` | after the commit exists | notifying, logging | **no** — too late |

**`commit-msg` is handed the message.** Git passes it the path to a file containing what you typed, as `$1`. Replace the `commit-msg` hook with one that enforces a house style:

```sh
#!/bin/sh
echo "   >> commit-msg received: $1"
echo "   >> first line: $(head -1 "$1")"
if ! head -1 "$1" | grep -qE '^(feat|fix|docs): '; then
    echo "   BLOCKED: message must start with feat:, fix: or docs:"
    exit 1
fi
exit 0
```

```
printf 'y\n' > msg.txt
git add msg.txt
git commit -m "changed some stuff"
```
```
   >> pre-commit ran
   >> prepare-commit-msg ran
   >> commit-msg received: .git/COMMIT_EDITMSG
   >> first line: changed some stuff
   BLOCKED: message must start with feat:, fix: or docs:
```

The message lives in `.git/COMMIT_EDITMSG` — a file you saw appear back in Iteration 4. Note that **`post-commit` did not run**: the commit was cancelled, so there was nothing to run after. Now with a good message:

```
git commit -m "feat: add msg file"
```
```
   >> pre-commit ran
   >> prepare-commit-msg ran
   >> commit-msg received: .git/COMMIT_EDITMSG
   >> first line: feat: add msg file
   >> post-commit ran
[main 00f7378] feat: add msg file
 1 file changed, 1 insertion(+)
 create mode 100644 msg.txt
```

All four run, and the commit is made.

**One escape hatch.** `--no-verify` skips `pre-commit` and `commit-msg` entirely:

```
git commit --no-verify -m "whatever I like"
```

So hooks are a **helpful guard, not a security wall** — anyone with the repository can bypass them. This is exactly why the serious checks also run on the server, which is Iteration 8.

**Takeaway to say out loud:** "`pre-commit` checks the code, `commit-msg` checks the message, `post-commit` is too late to stop anything."

---

## Iteration 8 — Hooks on the server side

Everything so far ran on your own machine. But a team needs rules **nobody can bypass with `--no-verify`**. Those hooks live on the server the team pushes to.

**a. What we set up**

A pretend server. A **bare** repository is one with no working files — just the `.git` contents, at the top level. That is what GitHub stores.

```
cd ~/task21
git init --bare server.git
ls server.git
```

**b. Task**

Predict what `ls server.git` shows. Then set up a rule protecting `main`:

```
nano server.git/hooks/pre-receive
```

```sh
#!/bin/sh
while read old new ref
do
    echo "[server] someone is pushing to $ref"
    if [ "$ref" = "refs/heads/main" ]; then
        echo "[server] REJECTED: main is protected, open a pull request"
        exit 1
    fi
done
exit 0
```

```
chmod +x server.git/hooks/pre-receive
cd demo
git remote add origin ~/task21/server.git
git push origin main
```

**c. Observation (what you should find)**

`ls server.git` shows the familiar contents, but at the top level — there is no `.git` subfolder, because **the folder *is* the repository**:

```
HEAD  branches  config  description  hooks  info  objects  refs
```

And the push is refused:

```
remote: [server] someone is pushing to refs/heads/main
remote: [server] REJECTED: main is protected, open a pull request
To /home/student/task21/server.git
 ! [remote rejected] main -> main (pre-receive hook declined)
error: failed to push some refs to '/home/student/task21/server.git'
```

Look at the `remote:` prefix on the first two lines. That output came from **the other machine** — Git relayed it back to you. This is precisely what GitHub's "protected branch" is: a `pre-receive` hook saying no.

`pre-receive` reads three values per line — the old hash, the new hash, and the ref being updated — which is how it knows a push targets `refs/heads/main`. Push a different branch and it is allowed:

```
git switch -c feature-login
printf 'login\n' > login.txt
git add login.txt
git commit -m "Add login"
git push origin feature-login
```
```
remote: [server] someone is pushing to refs/heads/feature-login
To /home/student/task21/server.git
 * [new branch]      feature-login -> feature-login
```

**Two other push-time hooks are worth meeting.**

`pre-push` runs on **your** machine, before anything is sent — the usual place to run the test suite:

```sh
#!/bin/sh
echo "[local] pre-push: running the tests before anything leaves this machine..."
echo "[local] a test failed!"
exit 1
```

```
git push origin feature-login
```
```
[local] pre-push: running the tests before anything leaves this machine...
[local] a test failed!
error: failed to push some refs to '/home/student/task21/server.git'
```

No `remote:` prefix anywhere — the server never even heard about this push.

`post-receive` runs on the **server**, after a push is accepted. It cannot block anything, so it is where deployment is triggered:

```sh
#!/bin/sh
echo "[server] post-receive: push accepted, starting deployment..."
echo "[server] deployment finished"
```
```
remote: [server] post-receive: push accepted, starting deployment...
remote: [server] deployment finished
To /home/student/task21/server.git
   2ec2004..2d78727  feature-login -> feature-login
```

**Takeaway to say out loud:** "Client hooks can be skipped; server hooks cannot. `pre-receive` guards the branch, `post-receive` deploys."

---

## Iteration 9 — How hooks are really used, and why you never see them

**a. What we set up**

No commands. Look back at the hooks you wrote and match each to a real job.

**b. Task**

Before reading on, answer one puzzle in your notebook. You have written five hooks today. Run:

```
cd ~/task21/demo
git switch main
git status --short
git ls-files
```

Your teammate clones this repository tomorrow. **Do they get your hooks?** Why or why not?

**c. Observation (what you should find)**

```
a.txt
hello.txt
msg.txt
notes/day1.txt
order.txt
```

`git status --short` prints nothing at all, and your hooks are nowhere in `git ls-files`. **Hooks live inside `.git/`, and Git never tracks its own folder.** So they are not committed, not pushed, and not cloned. Your teammate gets none of them.

That is a deliberate safety decision: cloning a repository must never run someone else's scripts on your machine. But it leaves teams with a real problem — how do you give everyone the same `pre-commit` check? Two standard answers:

- **`core.hooksPath`** — a setting that points Git at a *tracked* folder instead. Commit your scripts to `.githooks/`, and each person runs `git config core.hooksPath .githooks` once.
- **The `pre-commit` framework** — a widely used tool (`pip install pre-commit`). The team commits a `.pre-commit-config.yaml` file listing the checks; each person runs `pre-commit install` once, which writes the real hook into `.git/hooks/` for them.

Both work the same way underneath: the hook file still has to end up in `.git/hooks/`, or `core.hooksPath` has to point somewhere else. There is no third mechanism.

**Where these hooks earn their keep in industry:**

| Hook | Real job | Why there |
|---|---|---|
| `pre-commit` | run a formatter (`black`, `prettier`, `clang-format`) so the whole team's code looks identical | before the commit exists — nothing bad is recorded |
| `pre-commit` | **scan for secrets** — AWS keys, passwords, API tokens | once a key is committed and pushed, it is in the history forever; it must be stopped here |
| `pre-commit` | run the linter on staged files only — fast | keeps the check quick enough that people don't disable it |
| `commit-msg` | require a ticket ID (`JIRA-123: ...`) or the `feat:`/`fix:` convention | the message is final at this point |
| `commit-msg` | reject a message under ~10 characters ("fix", "asdf", "wip") | cheap way to keep history readable |
| `pre-push` | run the test suite | slower checks belong here — you push far less often than you commit |
| `pre-receive` | **protected branches**: refuse a direct push to `main`, force everything through a pull request | server-side, so `--no-verify` cannot help |
| `pre-receive` | reject a force-push that would erase history, or a file over 100 MB | protects everyone, not just the careful people |
| `post-receive` | start the CI build, deploy the site, post to the team's Slack channel | runs only after the push is safely accepted |

Notice the pattern: **cheap checks early and locally, expensive checks later, unskippable checks on the server.**

**Takeaway to say out loud:** "Hooks aren't cloned, because cloning must never run a stranger's script."

---

## Practice — Predict the answer

Write your answer in your notebook **before** checking. Assume the `demo` repository from the worksheet.

**P1.** You run `git init fresh`. How many objects are in `fresh/.git/objects/`?

**P2.** Which single command tells you which *file* a setting came from?

**P3.** You run `git config --global user.email "me@x.com"`. Which file changed?

**P4.** `.git/config` says `user.email = a@b.com` and `~/.gitconfig` says `user.email = c@d.com`. Which does a commit in that repository use?

**P5.** Two students on two different laptops both create a file containing exactly `hello` and press Enter. Do their blob hashes match? Do their commit hashes match? Explain both answers.

**P6.** After `git add big.txt` but *before* `git commit`, how many new objects exist?

**P7.** `cat .git/HEAD` prints `ref: refs/heads/main`. What single command shows the commit hash it eventually leads to?

**P8.** A commit changes one file in a project that has 400 unchanged files. Roughly how many **new blobs** does Git write?

**P9.** You commit a file, then delete the working copy with `rm hello.txt`. Is the blob still in `.git/objects/`?

**P10.** You write `.git/hooks/pre-commit` and it never runs. Give the two most likely reasons.

**P11.** Put these in firing order: `commit-msg`, `post-commit`, `pre-commit`, `prepare-commit-msg`.

**P12.** Which of those four **cannot** stop a commit, no matter what it returns?

**P13.** Your `pre-commit` hook ends with `exit 1`. What happens to the staged files?

**P14.** A teammate clones your repository. Do they get your `pre-commit` hook? Name one way a team solves this.

**P15.** Your company must guarantee that **nobody** pushes straight to `main`. Which hook, and on which machine?

**P16.** Why is running a 10-minute test suite in `pre-commit` a bad idea, and where should it go instead?

---

### Self-check

**Cover this until every answer is written down.**

**P1** — Zero. `objects/` starts empty; so does `refs/heads/`, and there is no `.git/index` yet either.

**P2** — `git config --list --show-origin` (or `git config --show-origin <key>` for one setting).

**P3** — `~/.gitconfig`, in your home folder. Not the project. That is all `--global` means.

**P4** — `a@b.com`. Local beats global, which beats system.

**P5** — **Blob hashes match** — a blob's hash comes from the content alone, so `hello\n` is `ce013625…` everywhere. **Commit hashes differ** — a commit contains their name, email and timestamp, which are different.

**P6** — One: the blob. `git add` writes it. The tree and commit objects wait for `git commit`.

**P7** — `git rev-parse HEAD`. (Or follow it by hand: `cat .git/refs/heads/main`.)

**P8** — One. Unchanged files keep the blobs they already have — same content, same hash, already on disk. You also get a few new trees and one new commit, but only **one** new blob.

**P9** — Yes. The object is committed; the working copy is a separate thing. `git checkout hello.txt` brings it back from that blob.

**P10** — (1) You forgot `chmod +x`. (2) The file is named `pre-commit.sample`, or otherwise misspelled — the name must be exact. (A missing `#!/bin/sh` line is a third possibility.)

**P11** — `pre-commit` → `prepare-commit-msg` → `commit-msg` → `post-commit`.

**P12** — `post-commit`. The commit already exists by then; there is nothing left to stop.

**P13** — Nothing happens to them — they stay staged, exactly as they were. No commit is created. Fix the problem and commit again.

**P14** — **No.** Hooks live in `.git/`, which is never tracked, so they are not committed, pushed or cloned. Teams solve it with `core.hooksPath` pointing at a committed folder, or with the `pre-commit` framework and a committed `.pre-commit-config.yaml`.

**P15** — `pre-receive`, on the **server**. A client-side hook is no good: anyone can skip it with `--no-verify`.

**P16** — People commit many times an hour, so a 10-minute wait each time would make them disable the hook or use `--no-verify` constantly. Slow checks belong in `pre-push` (you push far less often) or in CI triggered by `post-receive`.

---

## One-page reference

**The `.git` folder**

| Entry | What it holds |
|---|---|
| `HEAD` | text: which branch you are on (`ref: refs/heads/main`) |
| `config` | this repository's settings |
| `index` | the staging area — appears at the first `git add` |
| `objects/` | every version of every file, named by content hash |
| `refs/heads/<name>` | one file per branch, holding one commit hash |
| `logs/` | the reflog — a diary of every branch movement |
| `hooks/` | scripts Git runs at fixed moments |
| `info/exclude` | private, uncommitted ignore rules |

**The three config levels** (later beats earlier)

| Level | File | Set with |
|---|---|---|
| system | `/etc/gitconfig` | `git config --system` |
| global | `~/.gitconfig` | `git config --global` |
| local | `.git/config` | `git config` |

**The three object types**

| Type | Holds | Knows the file name? |
|---|---|---|
| blob | one file's contents | **no** |
| tree | a folder listing: mode, type, hash, name | **yes** |
| commit | one tree, parent(s), author, date, message | no |

**Commands for looking inside**

| Command | Does |
|---|---|
| `git hash-object <file>` | calculate a hash without storing anything |
| `git cat-file -t <hash>` | the object's type |
| `git cat-file -p <hash>` | the object's contents, readable |
| `git cat-file -s <hash>` | its size in bytes |
| `git rev-parse HEAD` | the hash `HEAD` leads to |
| `git ls-files -s` | what is in the index |
| `git count-objects` | how many loose objects exist |

**Hooks — when they fire and whether they can block**

| Hook | Where | Fires | Blocks? |
|---|---|---|---|
| `pre-commit` | client | before the message is asked for | yes |
| `prepare-commit-msg` | client | before you edit the message | yes |
| `commit-msg` | client | message is final (path in `$1`) | yes |
| `post-commit` | client | commit already made | no |
| `pre-push` | client | before anything is sent | yes |
| `pre-receive` | **server** | before any ref is updated | yes |
| `update` | **server** | once per ref being updated | yes |
| `post-receive` | **server** | after the push is accepted | no |

**Rules to keep:**
- `.git` is a folder of ordinary files; delete it and the history is gone.
- The hash comes from the content — same content, same hash, everywhere.
- `git add` writes the blob; `git commit` writes the tree and the commit.
- Unchanged files are stored once, not once per commit.
- A branch is a small file holding one hash.
- A hook needs the **exact name** and `chmod +x`. `exit 0` allows, non-zero blocks.
- Client hooks can be skipped with `--no-verify`; server hooks cannot.
- Hooks are never cloned — they live in `.git/`.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| repository (repo) | రిపాజిటరీ | ప్రాజెక్ట్ + దాని మొత్తం చరిత్ర ఉన్న ఫోల్డర్ |
| `.git` folder | డాట్-గిట్ ఫోల్డర్ | గిట్ తన సమాచారమంతా దాచే చోటు |
| hash | హాష్ | విషయాన్ని బట్టి లెక్కించిన పొడవైన పేరు (`ce0136…`) |
| object | ఆబ్జెక్ట్ | `.git/objects/`లో నిల్వ ఉన్న ఒక ముక్క |
| blob | బ్లాబ్ | ఒక ఫైల్ లోపలి విషయం — పేరు లేకుండా |
| tree | ట్రీ | ఫోల్డర్ జాబితా — పేర్లు, హాష్‌లు |
| commit | కమిట్ | ఒక స్నాప్‌షాట్ — ట్రీ, రచయిత, సమయం, సందేశం |
| parent | పేరెంట్ | ముందటి కమిట్ — చరిత్రను కలిపే లింక్ |
| branch | బ్రాంచ్ | ఒక హాష్‌ను పట్టుకున్న చిన్న ఫైల్ |
| `HEAD` | హెడ్ | ప్రస్తుతం ఏ బ్రాంచ్‌లో ఉన్నామో చెప్పే ఫైల్ |
| index / staging area | ఇండెక్స్ | `git add` చేసినవి వేచి ఉండే జాబితా |
| reflog | రెఫ్‌లాగ్ | బ్రాంచ్ కదలికల డైరీ — పోయిన పనిని తిరిగి తెస్తుంది |
| config | కాన్ఫిగ్ | సెట్టింగ్‌లు — system / global / local |
| global | గ్లోబల్ | మీ అన్ని రిపోలకూ వర్తించేది (`~/.gitconfig`) |
| local | లోకల్ | ఈ ఒక్క రిపోకే (`.git/config`) |
| hook | హుక్ | నిర్ణీత క్షణంలో గిట్ నడిపే స్క్రిప్ట్ |
| exit code | ఎగ్జిట్ కోడ్ | `0` = సరే, `0` కానిది = ఆపు |
| bare repository | బేర్ రిపాజిటరీ | పని ఫైల్‌లు లేని రిపో — సర్వర్‌లో ఉండేది |
| push | పుష్ | మీ కమిట్‌లను సర్వర్‌కు పంపడం |
| protected branch | రక్షిత బ్రాంచ్ | నేరుగా పుష్ చేయనివ్వని బ్రాంచ్ (`pre-receive`) |
