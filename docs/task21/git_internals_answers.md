# Git Internals — Answers with Reasoning

Check the **reasoning**, not just the letter. Five ideas cover nearly everything here:

1. `.git` is a folder of ordinary files — nothing in it is magic.
2. An object's name is a **hash of its own contents**, so identical content is stored **once**.
3. `git add` writes the **blob**; `git commit` writes the **tree** and the **commit**.
4. A branch is a small text file holding one hash; `HEAD` names the branch.
5. A hook is an executable script named for its moment, and its **exit code** is the whole API — `0` allows, non-zero blocks.

---

# Part A — Multiple Choice

**A1. B) zero** — `git init` creates the folder structure but no content. `objects/` and `refs/heads/` are both empty, and there is not even a `.git/index` yet. Objects only appear when you `git add`.

**A2. B) a text file naming the branch you are on** — you can `cat` it and read it. It holds `ref: refs/heads/main`, which is a *path*, not a hash. Right after `git init` that path does not exist yet, which is exactly what "no commits yet" means.

**A3. C) `~/.gitconfig`** — that is the entire meaning of `--global`: write it to the file in your home folder. It is plain text you could have typed by hand.

**A4. A) `a@b.com`** — local beats global, which beats system. `.git/config` is the local file, so it wins for that one repository. The global setting is untouched and still applies everywhere else.

**A5. B) `git config --list --show-origin`** — plain `--list` shows the values but not where they came from, which is useless when you are trying to work out why a setting is not taking effect.

**A6. A) the file's contents only** — not the name, not the author, not the time. This is why two students on two machines get the identical blob hash for identical content, and why an unchanged file is never stored twice.

**A7. B) one** — the blob. This surprises most people: the object is written at `git add`, before any commit exists. The tree and commit objects wait for `git commit`.

**A8. C) three** — one blob (the contents), one tree (the folder listing that gives the file its name), and one commit (which tree, who, when, message). Even the smallest possible commit needs all three types.

**A9. B) tree** — a blob is just bytes with no name attached; a commit only names its tree. The tree is what records `100644 blob ce0136… hello.txt`. Rename a file without changing its contents and the blob is reused; only the tree changes.

**A10. B) zlib-compressed binary** — exactly the binary-vs-text distinction from Task 16. `cat` is the wrong tool; `git cat-file -p <hash>` is the right one. (`file` on the object confirms: `zlib compressed data`.)

**A11. C) one** — only the changed file needs a new blob. The 500 unchanged files still hash to the values already on disk, so Git reuses those objects. You also get a few new trees and one new commit, but only one new **blob**.

**A12. B) the exact hook name plus `chmod +x`** — both are required. The shipped files are named `pre-commit.sample`, and that `.sample` ending is precisely why none of them run.

**A13. B) the commit is blocked** — non-zero means stop. Nothing else about the repository changes: the staged files stay staged, exactly as they were.

**A14. C) `post-commit`** — by the time it runs the commit object already exists. It is for notifying, logging or triggering something afterwards, never for validation.

**A15. C) `pre-receive`, on the server** — a client-side hook is useless as a guarantee because anyone can skip it with `--no-verify`, or simply not install it. This is what a "protected branch" on GitHub actually is.

**A16. B) is not cloned — hooks live inside `.git/`** — and Git never tracks its own folder. This is deliberate: cloning a repository must never run a stranger's script on your machine.

---

# Part B — Fill in the Blanks

**B1. `.git`** — delete it and you are left with an ordinary folder of files, with no history.

**B2. global, local** — full order, weakest to strongest: system (`/etc/gitconfig`) → global (`~/.gitconfig`) → local (`.git/config`).

**B3. blob** — contents only, no name.

**B4. tree** — the folder listing, and the only object type that records file names.

**B5. `parent`** — it holds the previous commit's hash. That single line *is* the history; nothing else links commits together.

**B6. `.git/refs/heads/main`** (or `.git/refs/heads/<branch-name>`) — a file containing one 40-character hash. That is why creating a branch is instant.

**B7. `.git/index`** — it does not exist until your first `git add`.

**B8. type, contents** — `-t` for type, `-p` to print. (`-s` gives the size in bytes.)

**B9. `0`** — `exit 0` allows; any non-zero value blocks.

**B10. `--no-verify`** — which is exactly why serious rules must also live on the server.

---

# Part C — Scenario Questions

**C1.**

(a) **No, nothing is broken.** This is the normal state of a brand-new repository.

(b) `HEAD` contains the *path* `refs/heads/master` — a name, not a hash. It is pointing at a branch file that has not been created yet. Git is saying "when a commit appears, put it on a branch called `master`."

(c) The **first commit**. Making a commit creates `.git/refs/heads/master` containing that commit's hash. Until then, `git status` reports "No commits yet".

**C2.**

(a) **Yes, the blob hashes match** — both are `ce013625030ba8dba906f756967f9e9ca394464a`.

(b) **No, the commit hashes differ.**

(c) A blob's hash is computed from the **content alone** (`hello\n`), so identical content gives an identical hash on any machine anywhere. A commit object also contains the author's **name, email and timestamp**; those differ between two students, so the resulting hash differs. This is why the worksheet's blob and tree hashes match yours exactly while its commit hashes do not.

**C3.**

(a) **One commit** — there is exactly one object of type `commit`.

(b) **One file** — one blob. (Strictly: one *distinct content*. Two files with identical contents would share a single blob, so the honest answer is "at least one," and the tree would settle it.)

(c) The **tree**, `aaa96ced…`. Running `git cat-file -p aaa96ced…` would print `100644 blob ce013625… hello.txt`. The blob itself has no idea it is called `hello.txt`.

**C4.**

(a) **No, it is not the first commit** — it has a `parent` line. The very first commit in a repository has no parent at all.

(b) `git cat-file -p 1d817ca4…`, or the shortcut `git cat-file -p HEAD^{tree}`.

(c) In the **blobs**, which the tree points to. The commit holds no file content whatsoever — it is about 170 bytes naming a tree, a parent, two people and a message.

**C5.**

(a) A new **blob** (the contents of `notes/day1.txt`), a new **tree** for the `notes/` folder, a new **root tree** (because the top-level listing now has an extra entry), and the new **commit**. Note that adding a file inside a folder forces a new tree for every folder on the path up to the root.

(b) **No new blob was written for `hello.txt`.** Its content did not change, so its hash did not change, so the object already sitting in `.git/objects/` is the correct one. Both commits' root trees point at the very same `ce013625…`.

**C6.**

Two likely causes:
1. **The file is not executable** — the `chmod +x` was forgotten. This is by far the most common cause, and Git gives no warning; it simply skips the hook.
2. **The name is wrong** — most often it is still `pre-commit.sample`, or misspelled (`precommit`, `pre_commit`). The name must be exact.

Fix for the common one:
```
chmod +x .git/hooks/pre-commit
```
(A third possibility: a missing or wrong `#!/bin/sh` line on the first line.)

**C7.**
```
pre-commit
prepare-commit-msg
commit-msg
post-commit
```
The order is fixed: check the **code** first (`pre-commit`, before a message has even been asked for), then build the message (`prepare-commit-msg`), then check the **finished** message (`commit-msg`), then react to the completed commit (`post-commit`). Git prints its own `[main abc1234] Add file` summary after all four.

**C8.**

| Message | Result | Why |
|---|---|---|
| (a) `fix: correct the total` | **succeeds** | starts with `fix: ` |
| (b) `Fixed the total` | **blocked** | no `feat:`/`fix:`/`docs:` prefix |
| (c) `docs: update readme` | **succeeds** | starts with `docs: ` |
| (d) `feature: add login` | **blocked** | the pattern requires exactly `feat:`, and `feature:` does not match it |

(d) is the interesting one — a near-miss still fails, because `grep -qE '^(feat|fix|docs): '` demands the colon-and-space immediately after `feat`.

**C9.** `$1` is the **first argument Git passes to the hook**: the path to the file holding the commit message, which is `.git/COMMIT_EDITMSG`. The hook cannot read the message from a variable because the message may have been typed in an editor, supplied with `-m`, or built by `prepare-commit-msg` — so Git standardises it by writing the final text to that file and handing over its path. That also means the hook can *rewrite* the file to modify the message.

**C10.**

(a) **Nothing happened to it — it is still staged**, exactly as it was before. The `A` in `A  notes.txt` means "added to the index". A blocked commit changes nothing.

(b) **No.** `git log` would show the same commits as before.

(c) Fix whatever the hook complained about, `git add` the corrected file, and commit again. (They should *not* reach for `--no-verify` — the hook was doing its job.)

**C11.**

(a) The **server**. The clue is the `remote:` prefix — Git adds that to every line of output that came back from the other machine.

(b) `pre-receive`. Git says so explicitly: `(pre-receive hook declined)`.

(c) **No.** `--no-verify` only skips hooks on *your own* machine (and for `push` it skips `pre-push`). The server runs its hooks regardless of what your client asks for — which is the entire reason serious rules are enforced there.

**C12.**

(a) The **local machine**. The clue is the *absence* of a `remote:` prefix — compare with C11, where every server line carried one.

(b) **No, nothing.** `pre-push` runs before any data is sent, so the push was abandoned locally. The server never heard about it.

(c) Because you commit far more often than you push. A slow check in `pre-commit` would interrupt people many times an hour, and they would respond by disabling the hook or using `--no-verify` constantly — so the check would end up protecting nothing. Putting it in `pre-push` runs it when it matters, at a moment people already expect to wait.

**C13.**

| Job | Hook | Where |
|---|---|---|
| (a) reformat code | `pre-commit` | client |
| (b) refuse a short message | `commit-msg` | client |
| (c) refuse history-erasing push to `main` | `pre-receive` | **server** |
| (d) start deployment after a push | `post-receive` | **server** |
| (e) block an AWS secret key | `pre-commit` | client |
| (f) run a 12-minute test suite | `pre-push` | client |

For (e), note *why* `pre-commit` and not something later: once a secret is committed and pushed it is in the history permanently, and rewriting history to remove it is painful. Stopping it before the commit object exists is the only clean moment. (Serious teams also run a server-side scan, because the client hook can be bypassed.)

**C14.**

(a) Because hooks must live in `.git/hooks/`, and **Git never tracks its own `.git` folder**. A hook file committed to the repository is just an ordinary file sitting in the working tree; Git will not run it from there.

(b) Because cloning a repository would otherwise **execute a stranger's script on your machine**. You clone code from the internet all the time; if `git clone` could install and run hooks automatically, a malicious repository would own your laptop the moment you cloned it.

(c) The two standard solutions:
1. **`core.hooksPath`** — commit the scripts to a tracked folder such as `.githooks/`, and each person runs `git config core.hooksPath .githooks` once.
2. **The `pre-commit` framework** — the team commits a `.pre-commit-config.yaml` listing the checks; each person runs `pre-commit install` once, which writes the real hook into `.git/hooks/` for them.

Both still end with a hook in `.git/hooks/` or `core.hooksPath` pointing elsewhere. There is no third mechanism, and both require a one-time manual step from each developer — by design.

---

# Part D — Trace the `.git` Folder

**D1. `git init demo`** — creates `demo/.git/` with `HEAD` (holding `ref: refs/heads/master`, or `main` if you set `init.defaultBranch` earlier), `config`, `description`, `hooks/` full of `.sample` files, `info/exclude`, and the empty `objects/` and `refs/heads/`. **No objects, no branch file, no index.**

**D2. `git config --global user.name "Asha"`** — **nothing inside `.git` changes at all.** This writes `~/.gitconfig` in the home folder, which is a completely different place. A common misconception is that `--global` means "for this whole project".

**D3. `printf 'hi\n' > a.txt`** — still nothing in `.git`. Git has not been asked to do anything; `a.txt` is simply an untracked file. Object count: **0**.

**D4. `git add a.txt`** — two things happen:
- the blob `45b983be36b73c0788dc9cbcb76cbb80fc7bb057` is written to `.git/objects/45/b983be…` (object count **1**);
- `.git/index` is created, listing `a.txt` against that hash.

Still no commit and still no branch file.

**D5. `git commit -m "first"`** — object count goes 1 → **3**: a tree and a commit are added. Also created:
- `.git/refs/heads/master` containing the new commit's hash;
- `.git/logs/HEAD` and `.git/logs/refs/heads/master` (the reflog), whose first line starts with 40 zeros meaning "there was nothing here before";
- `.git/COMMIT_EDITMSG`, holding the message you just used.

**D6. `printf 'hi\n' > b.txt`, `git add b.txt`, `git commit -m "second"`**

**Two** new objects, taking the total from 3 to **5** — not three.

The surprise is that **`git add b.txt` creates nothing at all.** `b.txt` contains `hi\n`, exactly the same bytes as `a.txt`, so it hashes to the same `45b983be…` — and that object is already on disk. Git simply records the existing hash in the index. Only the commit step adds anything: a new tree (the root listing now has two entries) and the new commit object.

Confirm it by printing the new tree:
```
100644 blob 45b983be36b73c0788dc9cbcb76cbb80fc7bb057	a.txt
100644 blob 45b983be36b73c0788dc9cbcb76cbb80fc7bb057	b.txt
```
**Two different file names pointing at one single blob.** Git stores the content once, and the tree supplies both names.

---

## The pattern to notice

Everything in this task follows from one sentence: **the name of an object is a hash of its contents.**

Because the name comes from the content, identical content *cannot* be stored twice — which is why an unchanged file costs nothing in a new commit (C5, A11), why two students on different laptops get the same blob (C2), and why `git add b.txt` in D6 wrote nothing. Because a commit's content includes your name and the time, commit hashes are unique to you (C2). And because a branch is nothing more than a file holding one of those names, branching is instant.

The second pattern is about hooks: **`exit 0` allows, non-zero blocks — and that is the entire interface.** Where a hook lives decides how much it is worth. On your machine it is a helpful reminder that anyone can skip with `--no-verify`; on the server it is a rule.
