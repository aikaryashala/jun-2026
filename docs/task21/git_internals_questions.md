# Git Internals — Question Bank

Answer on paper, using the worksheet's ideas: `.git` is a folder of ordinary files; an object's name is a **hash of its own contents**; `git add` writes the blob and `git commit` writes the tree and the commit; a branch is a small file holding one hash; and a hook is an executable script whose **exit code** decides whether the operation continues.

Answers are **not** in this file.

---

# Part A — Multiple Choice

**A1.** Immediately after `git init`, how many files are in `.git/objects/`?

- A) one per file in the folder
- B) zero
- C) three
- D) one

**A2.** `cat .git/HEAD` prints `ref: refs/heads/main`. This means `HEAD` is:

- A) a compressed binary object
- B) a text file naming the branch you are on
- C) the hash of the latest commit
- D) a folder containing every branch

**A3.** `git config --global user.email "me@x.com"` writes to:

- A) `.git/config`
- B) `/etc/gitconfig`
- C) `~/.gitconfig`
- D) `.git/objects/`

**A4.** `.git/config` sets `user.email` to `a@b.com` and `~/.gitconfig` sets it to `c@d.com`. A commit in that repository is recorded with:

- A) `a@b.com`
- B) `c@d.com`
- C) both
- D) Git refuses to commit

**A5.** Which command tells you **which file** a setting came from?

- A) `git config --list`
- B) `git config --list --show-origin`
- C) `git status`
- D) `git cat-file -p`

**A6.** A blob object's hash is calculated from:

- A) the file's contents only
- B) the file name and contents
- C) the contents, the author and the time
- D) the commit message

**A7.** After `git add one.txt` and **before** any commit, how many new objects are in `.git/objects/`?

- A) zero
- B) one
- C) two
- D) three

**A8.** Committing one new file to an empty repository creates how many objects?

- A) one
- B) two
- C) three
- D) four

**A9.** Which object type is the only one that stores a **file name**?

- A) blob
- B) tree
- C) commit
- D) none of them

**A10.** `cat .git/objects/ce/013625…` prints gibberish because the file is:

- A) encrypted with your password
- B) zlib-compressed binary
- C) empty
- D) written in a foreign language

**A11.** A commit is made that changes one file in a project of 500 unchanged files. How many **new blob** objects does Git write?

- A) 500
- B) 501
- C) one
- D) zero

**A12.** What makes a file in `.git/hooks/` actually run?

- A) listing it in `.gitignore`
- B) the exact hook name plus `chmod +x`
- C) committing it
- D) naming it with a `.sample` ending

**A13.** A `pre-commit` hook finishes with `exit 1`. The result is:

- A) the commit is made anyway
- B) the commit is blocked
- C) the staged files are deleted
- D) Git switches branches

**A14.** Which hook **cannot** stop a commit from happening?

- A) `pre-commit`
- B) `commit-msg`
- C) `post-commit`
- D) `prepare-commit-msg`

**A15.** Your company must guarantee nobody pushes directly to `main`. The right hook, and where it lives:

- A) `pre-commit`, on each developer's machine
- B) `pre-push`, on each developer's machine
- C) `pre-receive`, on the server
- D) `post-receive`, on the server

**A16.** A teammate clones your repository. Your `pre-commit` hook:

- A) is cloned along with the code
- B) is not cloned — hooks live inside `.git/`
- C) is cloned only if it is executable
- D) is cloned only on Linux

---

# Part B — Fill in the Blanks

**B1.** Everything Git knows about a project lives in a single folder named ____________.

**B2.** The three configuration levels, from weakest to strongest, are system, ____________ and ____________.

**B3.** The object type that stores one file's contents, with no name attached, is a ____________.

**B4.** The object type that lists a folder's entries — mode, type, hash and name — is a ____________.

**B5.** The line in a commit object that names the commit before it is the ____________ line.

**B6.** A branch such as `main` is stored as a small file at the path ____________.

**B7.** The staging area is stored in the file ____________.

**B8.** `git cat-file -t <hash>` shows an object's ____________, while `git cat-file -p <hash>` shows its ____________.

**B9.** A hook allows the operation to continue by exiting with the code ____________.

**B10.** The flag that tells `git commit` to skip the `pre-commit` and `commit-msg` hooks is ____________.

---

# Part C — Scenario Questions

**C1.** A student runs `git init project`, then immediately `cat .git/HEAD`, and sees `ref: refs/heads/master`. They then run `ls .git/refs/heads/` and it is empty.
(a) Is something broken?
(b) Explain what `HEAD` is pointing at.
(c) What must happen before that file appears?

**C2.** Two students on two different laptops each create a file containing exactly `hello` followed by Enter, then `git add` and `git commit` it.
(a) Do their **blob** hashes match?
(b) Do their **commit** hashes match?
(c) Explain both answers by saying what goes into each hash.

**C3.** Here is a repository's object list after some work:
```
ce013625030ba8dba906f756967f9e9ca394464a  blob
aaa96ced2d9a1c8e72c56b253a0e2fe78393feb7  tree
32ac93555e79786deae14faa9dc6dfe1c509a43f  commit
```
(a) How many commits have been made?
(b) How many files are being tracked?
(c) Which object knows what the file is called?

**C4.** `git cat-file -p HEAD` prints:
```
tree 1d817ca446a8b4e99142735f2d3031be6d657836
parent 32ac93555e79786deae14faa9dc6dfe1c509a43f
author Ravi Kumar <ravi@example.com> 1785994200 +0530
committer Ravi Kumar <ravi@example.com> 1785994200 +0530

Add a note
```
(a) Is this the first commit in the repository? How can you tell?
(b) What command would show the folder listing for this snapshot?
(c) Where is the actual text of the committed files?

**C5.** A repository has 3 objects. The student adds a file inside a new folder `notes/` and commits. Now there are 7.
(a) List the four new objects by type.
(b) The old `hello.txt` was not touched. Was a new blob written for it? Why or why not?

**C6.** A student writes this file as `.git/hooks/pre-commit`:
```sh
#!/bin/sh
echo "checking..."
exit 0
```
They commit, and nothing is printed. Give the two most likely causes, and the command that fixes the more common one.

**C7.** Write the exact output order for this commit, given that all four hooks exist, are executable, and each simply echoes its own name before `exit 0`:
```
git add file.txt
git commit -m "Add file"
```
Hooks present: `post-commit`, `commit-msg`, `pre-commit`, `prepare-commit-msg`.

**C8.** A `commit-msg` hook contains:
```sh
#!/bin/sh
if ! head -1 "$1" | grep -qE '^(feat|fix|docs): '; then
    echo "BLOCKED: bad message"
    exit 1
fi
exit 0
```
For each message below, say whether the commit succeeds:
(a) `fix: correct the total`
(b) `Fixed the total`
(c) `docs: update readme`
(d) `feature: add login`

**C9.** In C8, what is `$1`? Name the actual file Git passes in, and say why the hook cannot simply read the message from a variable.

**C10.** A `pre-commit` hook blocks a commit. The student then runs `git status --short` and sees `A  notes.txt`.
(a) What happened to the staged file?
(b) Is there a new commit?
(c) What should they do next?

**C11.** A student pushes and sees:
```
remote: [server] REJECTED: main is protected, open a pull request
To /home/student/task21/server.git
 ! [remote rejected] main -> main (pre-receive hook declined)
```
(a) Which machine produced the first line, and how can you tell from the output?
(b) Which hook rejected it?
(c) Would `git push --no-verify` get around this? Explain.

**C12.** A different student pushes and sees:
```
[local] pre-push: running the tests before anything leaves this machine...
[local] a test failed!
error: failed to push some refs to '/home/student/task21/server.git'
```
(a) Which machine produced the first two lines? Give the clue in the output.
(b) Did the server receive anything at all?
(c) Why do teams put the test suite here instead of in `pre-commit`?

**C13.** For each job, name the single best hook **and** say whether it runs on the client or the server:
(a) reformat code so the whole team's files look identical
(b) refuse any commit message shorter than 10 characters
(c) refuse a push that would erase history on `main`
(d) start the deployment after a push is accepted
(e) block an AWS secret key from ever being recorded
(f) run a 12-minute test suite before code leaves the laptop

**C14.** A team wants every developer to run the same `pre-commit` check. Simply committing the hook file to the repository does not work.
(a) Why not?
(b) Why is this restriction a **good** thing for security?
(c) Name the two standard ways teams solve it.

---

# Part D — Trace the `.git` Folder

For each step, say what changed inside `.git`. Start from an empty folder.

**D1.** `git init demo`
**D2.** `git config --global user.name "Asha"`
**D3.** `printf 'hi\n' > a.txt`
**D4.** `git add a.txt`
**D5.** `git commit -m "first"`
**D6.** `printf 'hi\n' > b.txt` then `git add b.txt` then `git commit -m "second"`

For **D6** in particular: how many *new* objects appear, and why is that number surprising?
