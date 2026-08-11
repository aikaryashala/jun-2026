# Pull Requests — What Is Actually Happening

**This page is different from the other tasks.** There is nothing to set up and nothing to run. You already know how to open a pull request, leave a review and merge it — you have done it. This page explains **what Git and the website are doing while you click**, so that when something behaves strangely you know why.

Read it once from top to bottom. After that, keep it as a reference: the one-page summary at the end has the tables you will actually come back for.

**Assumed:** Task 21 (Git Internals). Everything here leans on three facts from that task — a commit is an object named by a hash of its contents, a branch is a small text file holding one such hash, and a "protected branch" is a `pre-receive` hook on the server saying no.

**Commands in this page are illustrations, not instructions.** They are shown with their real output to make a point. You are not expected to run them, though nothing stops you.

> **The golden rule of pull requests**
> A pull request is **not a Git command.** Type `git pull-request` and Git will tell you no such thing exists. A PR is a *record kept by a website*, saying: "branch X would like to go into branch Y." The commits are already on the server **before** the PR exists. Underneath every PR — however long the discussion, however many reviewers — there is only ever **two branch names and one merge.**

*(A note on the examples: two people share one repository. **Asha** writes a change; **Ravi** reviews it. The commit hashes shown are from a real run — yours would be different, for the reason Task 21 gave: a commit contains its author and its timestamp.)*

---

## 1. A pull request is not a Git thing

Git does not have pull requests. Not in any version, not with any flag:

```
$ git pull-request
git: 'pull-request' is not a git command. See 'git --help'.
```

The whole idea belongs to the **forge** — the website hosting your repository: GitHub, GitLab, Gitea, Bitbucket. Git provides commits, branches and merging. The forge adds a place to *talk about a merge before it happens*.

So what exists on the server when a PR is open? Exactly this — and nothing more:

```
$ git ls-remote origin
11a9d5f5d2ed6f35624137bbfdc8eedc3d50e155	HEAD
b17cc20f028d01533aab78433ea5e787c2f70fb5	refs/heads/feature/greet
11a9d5f5d2ed6f35624137bbfdc8eedc3d50e155	refs/heads/main
```

Two branches. That is the entire Git-side content of the pull request. Asha's commits arrived on the server when she ran `git push`; the PR did not carry them there. Opening the PR:

- creates **no** new object in `.git/objects/`,
- moves **no** branch,
- changes **nothing** in the repository at all.

It writes a row in GitHub's own database: *PR #7, base `main`, compare `feature/greet`, state open, author Asha.* That is why opening and closing a PR is instant, and why you can close one and open it again with no effect on your code.

This also explains a thing that confuses people: **the PR is not a copy of your work.** If someone deletes the `feature/greet` branch from the server, the pull request has nothing left to show.

**Say it out loud:** "The push moves the commits. The pull request is just a note asking someone to merge them."

---

## 2. The four names in every pull request

Every PR is built from four things. Two you choose, one Git works out, one follows from the other three.

| Name | What it is | Who decides |
|---|---|---|
| **base** | the branch you want your work to end up in (usually `main`) | you, when you open the PR |
| **compare** | the branch holding your work (`feature/greet`) | you, when you open the PR |
| **merge base** | the newest commit that **both** branches already have | Git works it out |
| **the diff** | what would change if the merge happened | follows from the other three |

Here is the situation, drawn. Asha branched off `main`, made two commits, and while she was working Ravi pushed one commit to `main`:

```
                    A1 ──── A2        ← feature/greet   (compare)
                   ╱
      ──── M1 ────┘
             ╲
              ──── M2                 ← main            (base)

      M1 = the merge base: the newest commit both branches have
```

Git will tell you which commit that is:

```
$ git merge-base origin/main feature/greet
d5e5b26c485e17457c88b19beec10a4b7bc2216c
```

`d5e5b26` is `M1`, the "Initial project" commit — the last point where the two branches agreed. Everything the PR is about happened after it.

The commit list on the PR's **Commits** tab is exactly the commits on compare that are not on base:

```
$ git log --oneline origin/main..feature/greet
b17cc20 Greet a name
8aa453f Move greeting into a function
```

Two commits, which is what the PR will say: *"Asha wants to merge 2 commits into `main` from `feature/greet`."* Now you can read that sentence properly — it names the compare branch, the base branch, and the count from the command above.

**Say it out loud:** "Base, compare, and the merge base where they last agreed — everything else in the PR follows from those three."

---

## 3. What "Files changed" is really showing

This is the single most misunderstood thing about pull requests, and it is worth getting right, because sooner or later it will confuse you.

There are two ways to compare two branches, and they give **different answers**.

### Two dots — "how do these two branches differ?"

```
$ git diff origin/main..feature/greet
diff --git a/README.md b/README.md
index 1ebefdc..dfb4b07 100644
--- a/README.md
+++ b/README.md
@@ -1,3 +1 @@
 Team notes
-
-Build with: clang greet.c -o greet
diff --git a/greet.c b/greet.c
index d783c35..017fe55 100644
--- a/greet.c
+++ b/greet.c
@@ -1,6 +1,11 @@
 #include <stdio.h>
 
+void greet(const char *name)
+{
+    printf("Hello, %s\n", name);
+}
+
 int main(void)
 {
-    printf("Hello\n");
+    greet("world");
 }
```

Look at the first file. This says Asha's PR **deletes two lines from `README.md`** — the build instructions Ravi added.

**It does no such thing.** Asha never touched `README.md`. She has never even seen those lines; they were added to `main` after she branched off.

Why does the diff claim it? Because two dots compares the **tip of `main`** with the **tip of `feature/greet`**, as two piles of files. `main`'s pile has the build line; Asha's pile does not. Read as a change, "present on the left, absent on the right" means deletion. The comparison is honest — it is the question that was wrong.

### Three dots — "what would merging change?"

```
$ git diff origin/main...feature/greet
diff --git a/greet.c b/greet.c
index d783c35..017fe55 100644
--- a/greet.c
+++ b/greet.c
@@ -1,6 +1,11 @@
 #include <stdio.h>
 
+void greet(const char *name)
+{
+    printf("Hello, %s\n", name);
+}
+
 int main(void)
 {
-    printf("Hello\n");
+    greet("world");
 }
```

`README.md` is gone from the output. Only `greet.c` remains — which is the truth about what Asha did.

Three dots compares the **merge base** with the tip of compare. Not `M2...A2`, but `M1...A2`. It answers "what has Asha added since the branches parted?", and Ravi's README change is simply not part of that story.

Side by side:

```
$ git diff --stat origin/main..feature/greet      ← two dots
 README.md | 2 --
 greet.c   | 7 ++++++-
 2 files changed, 6 insertions(+), 3 deletions(-)

$ git diff --stat origin/main...feature/greet     ← three dots
 greet.c | 7 ++++++-
 1 file changed, 6 insertions(+), 1 deletion(-)
```

**GitHub's "Files changed" tab is the three-dot diff.** So is the "+6 −1" badge, and so is the file count. This is why a PR that has been open for two weeks, while thirty other PRs merged into `main`, still shows only *your* files. It is comparing against the merge base, not against today's `main`.

| | Compares | Answers | Where you see it |
|---|---|---|---|
| `A..B` (two dots) | tip of A ↔ tip of B | "how do these two branches differ?" | almost never what you want for a PR |
| `A...B` (three dots) | **merge base** ↔ tip of B | "what does B add?" | **the PR's Files changed tab** |

One caution, and it is the reason the two-dot form exists at all: the three-dot diff shows what your branch *adds*, which is not quite the same as what the code will *look like* after the merge. If someone else edited a different part of the same file, the merged result contains both changes — but your PR's diff will never show you theirs. Section 8 is about what happens when those two changes collide.

**Say it out loud:** "Files changed is a three-dot diff — measured from where the branches parted, not from today's main."

---

## 4. Why the pull request updates itself when you push

Ravi reviews the PR and asks for a change. Asha does not open a new pull request. She commits a fix and pushes it to the same branch — and the existing PR quietly becomes the new version, with the review comments still attached.

This surprises people, and the reason is in Section 1. The PR's database row does not store commits. It stores a **branch name**. So:

```
PR #7:  base = main   compare = feature/greet
```

Whatever `refs/heads/feature/greet` points to *right now* is what the PR shows. Push a third commit and the branch file on the server holds a new hash; the PR redraws itself around it. Nothing needed updating, because nothing was ever copied.

Three consequences worth knowing:

**Pushing is how you answer a review.** There is no "submit new version" button because there is no version to submit. Push to the branch and you are done.

**Everything on that branch is in the PR, wanted or not.** A commit you pushed for an unrelated reason is now part of the proposal. This is the usual cause of "why are there 40 files in my PR" — the branch was made from the wrong starting point, so the merge base is far further back than intended.

**A force-push rewrites what the reviewer already read.** If you rebase or amend and then `git push --force`, the old commits are gone from the branch and the review comments were attached to lines in *those* commits. GitHub will mark them "outdated" and hide them, and Ravi has to read the whole thing again. Nothing is corrupted, but it is rude in a large review. `--force-with-lease` at least refuses to clobber work you have not seen; the politeness problem remains.

**Say it out loud:** "The PR follows a branch name, so pushing to the branch *is* updating the PR."

---

## 5. Review is a state machine

The Conversation tab looks like a chat, but underneath there are only a few distinct things, and only some of them affect whether the merge can happen.

| What you do | What it is | Blocks the merge? |
|---|---|---|
| Single line comment | one remark, posted immediately | no |
| **Review: Comment** | a bundle of remarks, no verdict | no |
| **Review: Approve** | a bundle plus "this is fine by me" | it *un*blocks — it is what a required approval counts |
| **Review: Request changes** | a bundle plus "not yet" | **yes**, until the same person approves or dismisses it |
| Suggestion | a comment containing replacement lines the author can commit with one click | no |
| Resolve conversation | marks a thread dealt with | only if the repo requires all conversations resolved |

Two things follow that are worth being clear about.

**"Request changes" is a lock held by one person.** It stays until *that reviewer* approves, or somebody with permission dismisses their review. Pushing a fix does not clear it — the fix is not automatically an answer, and only the reviewer can say whether it was. This is the usual reason a PR sits with a grey button while everybody assumes somebody else is blocking it.

**A single comment is not a review.** Remarks written in the diff and left as individual comments are sent one at a time and carry no verdict. Grouping them into a review, then choosing Approve or Request changes, is what makes the outcome visible to the gate in Section 6. A reviewer who writes ten comments and never submits a review has, formally, not reviewed anything.

**Say it out loud:** "Only Approve and Request changes move the gate. Everything else is conversation."

---

## 6. The gate: why the merge button is grey

The button has one job: tell you whether every rule this repository was configured with is currently satisfied. When it is grey, exactly one of these is false.

| Condition | What it means | Who set it |
|---|---|---|
| **No conflicts with base** | Git can combine the two sides without a human choosing | always required — this one is Git, not policy |
| **Required checks passed** | CI finished and every required job is green | repo settings |
| **Enough approvals** | e.g. at least 1, or 2 | repo settings |
| **No outstanding "request changes"** | nobody is still holding the lock from Section 5 | repo settings |
| **Conversations resolved** | every review thread marked resolved | repo settings, optional |
| **CODEOWNERS approved** | the owner of a touched path has approved | the `CODEOWNERS` file |
| **Branch up to date with base** | compare contains the latest base commit | repo settings, optional |
| **You have permission to merge** | write access, or maintainer on the repo | repo settings |

Only the first is a fact about Git. **Everything else is policy** — a switch someone turned on. This is why the same PR would merge instantly in one repository and sit blocked for a day in another; the code is identical, the rules are not.

Two of these are worth a closer look.

**Checks are just programs.** A "check" is some machine, somewhere, that noticed your push, ran a command, and reported back pass or fail. Nothing more mysterious. It is `pre-push` from Task 21 moved onto a server that never gets tired and cannot be skipped with `--no-verify`. A **pending** check is not a failing check — nothing has reported yet, and the button is grey for lack of an answer rather than because of a bad one.

**"Branch is out of date"** — the strictest setting, and the one people find most annoying. It insists your compare branch already contains the tip of base *before* you merge, so the tests that passed were run on the actual combination that is about to land. Without it a green PR can still break `main`: your change passed, their change passed, and the two together do not. That is called a **semantic conflict** — nothing overlaps textually, so Git is perfectly happy, and the build fails anyway. Section 3's warning about the three-dot diff is the same fact seen from the other side.

**Say it out loud:** "Only 'no conflicts' is Git. Every other grey button is a rule someone chose."

---

## 7. The three merge buttons, drawn

When the gate opens, GitHub offers up to three ways to land the work. They are not styles of the same operation — they produce **genuinely different histories**. The repository's settings decide which are available.

For all three, the starting point is the picture from Section 2:

```
                    8aa453f ──── b17cc20      ← feature/greet
                   ╱
      ──── d5e5b26┘
             ╲
              ──── 11a9d5f                    ← main
```

### Create a merge commit

```
$ git log --oneline --graph --all
*   9aab251 Merge pull request #7 from asha/feature/greet
|\
| * b17cc20 Greet a name
| * 8aa453f Move greeting into a function
* | 11a9d5f Document the build command
|/
* d5e5b26 Initial project
```

A new commit is created with **two parents** — one on each side:

```
$ git cat-file -p main
tree b53b12e6c5218ff58a8502176e904308d4bcea5f
parent 11a9d5f5d2ed6f35624137bbfdc8eedc3d50e155     ← old main
parent b17cc20f028d01533aab78433ea5e787c2f70fb5     ← the PR branch
```

Two `parent` lines. Task 21 showed a commit with one; this is what a merge commit is, and it is the only kind of commit that has more than one. Asha's original commits survive untouched, with their original hashes. The history now records that a branch existed and when it came back.

### Squash and merge

```
* 4529aba Greet a name (#7)
* 11a9d5f Document the build command
* d5e5b26 Initial project
```

All the branch's commits are flattened into **one new commit** on `main`, with a single parent. `8aa453f` and `b17cc20` are not in `main`'s history at all — the new `4529aba` contains their combined effect under a new hash. `main` becomes a straight line, one commit per PR.

The original commits still exist as objects while the branch exists, but once the branch is deleted nothing points at them and they are eventually cleaned up.

### Rebase and merge

```
* 6f523d7 Greet a name
* c6fd0b9 Move greeting into a function
* 11a9d5f Document the build command
* d5e5b26 Initial project
```

Each commit is **replayed** on top of `main`, one at a time. You keep the separate commits *and* get a straight line — but look at the hashes. `8aa453f` became `c6fd0b9`, and `b17cc20` became `6f523d7`. Different parent, therefore different commit content, therefore different hash. Task 21's rule again: the hash comes from the contents, and a commit's contents include its parent.

**These are not the same commits moved. They are new commits with the same changes.**

### The fact that ties it together

```
merge commit  → tree b53b12e6c5218ff58a8502176e904308d4bcea5f
squash        → tree b53b12e6c5218ff58a8502176e904308d4bcea5f
rebase        → tree b53b12e6c5218ff58a8502176e904308d4bcea5f
```

**All three produce exactly the same tree.** Identical files, byte for byte. Check out `main` after any of the three and you cannot tell which button was pressed by looking at the code — only by looking at the history.

So the choice is never about the result. It is entirely about what you want the log to say afterwards:

| | History shape | Original commits | Good when |
|---|---|---|---|
| **Merge commit** | branching, honest | kept, same hashes | you want the true record; large or long-running work |
| **Squash** | one straight line | replaced by one | PRs are one small idea; messy "wip", "fix typo" commits you do not want kept |
| **Rebase** | straight line, several commits | replayed, **new hashes** | the individual commits are each worth keeping, but you dislike merge bubbles |

One warning about rebase and squash. Both **rewrite** the commits, so anybody who had that branch checked out now has commits that no longer exist anywhere. Harmless for a PR branch about to be deleted; painful if a colleague built work on top of it.

**Say it out loud:** "All three give the same files. They differ only in what the history remembers."

---

## 8. Conflicts

A conflict means: **both sides changed the same lines, and Git will not guess.** It is not an error, not a sign anybody did anything wrong, and not a comment on whether the code is correct.

Asha's branch changed the greeting to `"Hello, world\n"`. While her PR was open, a different PR changing the same line to `"Namaste\n"` was merged into `main`:

```
* 41963b6 Say Namaste          ← main
| * 995fa62 Greet the world    ← feature/greet
|/
* 1189799 Initial project
```

The same line of the same file, changed two different ways since the merge base. Git can pair up changes to *different* lines by itself; it cannot pick between two rewrites of *one* line, because both are valid and only a person knows which is meant.

**The author resolves it, not the reviewer.** Not out of politeness — the reviewer usually cannot. The fix has to be committed onto the compare branch, and only someone who can push to that branch can do it. (This is what "allow edits by maintainers" in Section 11 is for.)

Asha brings `main` into her branch:

```
$ git merge origin/main
Auto-merging greet.c
CONFLICT (content): Merge conflict in greet.c
Automatic merge failed; fix conflicts and then commit the result.
```

Git writes both versions into the file and stops:

```
#include <stdio.h>

int main(void)
{
<<<<<<< HEAD
    printf("Hello, world\n");
=======
    printf("Namaste\n");
>>>>>>> origin/main
}
```

Three markers, and the middle one is the one people misread:

| Marker | Means |
|---|---|
| `<<<<<<< HEAD` | below this is **your branch's** version |
| `=======` | the divider — **not** part of anything, delete it |
| `>>>>>>> origin/main` | above this is the **incoming** version |

`git status` shows the file as `UU` — unmerged on both sides:

```
$ git status --short
UU greet.c
```

Resolving means editing the file until it says what you want, **markers removed**, then `git add` and commit. You are not obliged to pick a side; here the honest answer is neither of the two:

```
    printf("Namaste, world\n");
```

```
$ git add greet.c
$ git commit --no-edit
$ git log --oneline --graph --all
*   718ff74 Merge remote-tracking branch 'origin/main' into feature/greet
|\
| * 41963b6 Say Namaste
* | 995fa62 Greet the world
|/
* 1189799 Initial project
```

Push that, and the PR goes mergeable again. Notice what the PR's diff has become:

```
$ git diff origin/main...feature/greet
@@ -2,5 +2,5 @@

 int main(void)
 {
-    printf("Namaste\n");
+    printf("Namaste, world\n");
 }
```

The merge base moved forward to include `41963b6`, so the three-dot diff is now measured from there. Ravi's `Namaste` is part of the baseline rather than part of the proposal, and the PR correctly shows the only thing still being *proposed*: adding `, world`. **Resolving a conflict usually makes a PR's diff smaller.**

There are two ways to update a branch, and they leave different marks:

| | What it does | Cost |
|---|---|---|
| **Merge base into branch** (above) | adds a merge commit to your branch | an extra commit in the PR; safe, never rewrites anything |
| **Rebase branch onto base** | replays your commits on the new base | clean straight line, but new hashes → force-push → outdated review comments (Section 4) |

GitHub's "Update branch" button does the first. Its web conflict editor can resolve simple cases in the browser and commits the result to your branch — the same operation, done for you.

**Say it out loud:** "A conflict means two edits to one line. Git is not confused — it is refusing to guess."

---

## 9. Protected branches, and what the merge button is allowed to do

Task 21 ended with a server-side `pre-receive` hook refusing a push:

```
remote: [server] REJECTED: main is protected, open a pull request
 ! [remote rejected] main -> main (pre-receive hook declined)
```

That is all a protected branch is. GitHub's version is a settings page rather than a shell script, but it runs in the same place, at the same moment, and does the same thing: examine the incoming ref update and decline it.

Which answers a question worth asking. **If nobody can push to `main`, how does the merge button push to `main`?**

Because the merge does not happen on your machine at all. When you press it, the **server** performs the merge in its own copy and updates `refs/heads/main` itself — as the forge, under the rule "an approved pull request that passed the gate may update this branch". Your laptop sends one HTTPS request saying "merge PR #7". No push leaves your machine.

That is the whole design, and it is the reason two things you might have tried do not work:

- **`--no-verify` cannot help.** It skips *client* hooks — `pre-commit`, `commit-msg`, `pre-push` — which live on your machine and are yours to skip. Branch protection is server-side. Your flag never reaches it.
- **Neither can `--force`.** A force-push is still a push, and protection normally refuses those specifically.

The rule to carry away from both tasks: **a check you can skip is a helpful reminder; a check on the server is a rule.** Teams put the fast, friendly checks locally so mistakes are caught early, and put the ones that actually matter on the server, where good intentions are not required.

**Say it out loud:** "The merge button works because the server merges, not me — and the server is the thing enforcing the rule."

---

## 10. After the merge

**The commits are now on base.** Depending on the button, either the originals (merge), or one new commit (squash), or replayed copies (rebase). `main` moved; that is what merging is.

**Deleting the branch deletes nothing.** The prompt appears right after merging and it is safe to take. Remember Task 21: a branch is a small file holding one hash. Deleting it removes the file, not the commits — those are reachable from `main` now, which is the whole point. The PR page keeps working afterwards and its diff still renders.

**`Closes #12` in the description closes issue 12** — automatically, at the moment the PR merges, and only if the PR merged into the repository's default branch. It also links the two so each shows the other. `Fixes` and `Resolves` behave identically; a bare `#12` links without closing.

**Merged is not the same as closed.** A PR ends in one of two states, and the difference matters:

| End state | Means | Reopenable |
|---|---|---|
| **Merged** | the commits are on the base branch | no — it happened |
| **Closed** | abandoned; nothing was merged | yes, while the branch still exists |

A closed PR left no trace in the code. If the branch is then deleted, the work is genuinely gone from the server.

**Undoing a merged PR: revert, do not delete.** GitHub's Revert button opens a *new* PR containing a commit that applies the opposite changes. History is not rewritten — the original merge stays in the log, followed by its undo. That is deliberate: rewriting a shared `main` breaks the repository for everyone who has already pulled it. Two visible commits are the correct price.

**Say it out loud:** "Merged and closed are different endings. Undo by adding a revert, never by rewriting main."

---

## 11. Fork-based pull requests

So far both people could push to the same repository. But you can open a PR against a project where you have **no write access at all** — this is how essentially all open-source contribution works, and the mechanism is worth understanding.

A **fork** is your own copy of someone else's repository, on the server, under your account. You have full write access to your copy and none to theirs.

```
      github.com/aikaryashala/jun-2026     ← upstream (you cannot push)
                    │  fork
                    ▼
      github.com/asha/jun-2026             ← origin   (yours; push freely)
                    │  clone
                    ▼
      ~/jun-2026 on your VM
```

Cloning your fork gives you one remote. Most people add the second by hand:

```
$ git remote -v
origin      https://github.com/asha/jun-2026.git       (fetch)
origin      https://github.com/asha/jun-2026.git       (push)
upstream    https://github.com/aikaryashala/jun-2026.git (fetch)
upstream    https://github.com/aikaryashala/jun-2026.git (push)
```

The names are pure convention — Git attaches no meaning to either — but the convention is universal:

| Remote | Points at | You push to it? |
|---|---|---|
| `origin` | **your fork** | yes |
| `upstream` | the **original** repository | no |

The flow: branch, commit, push to `origin`, then open a PR whose compare is `asha:feature/greet` and whose base is `aikaryashala:main`. **A pull request can cross repositories** — the base and compare in Section 2 were both in one repository only because that was the simple case. Nothing else about the PR changes: same diff, same review, same gate, same buttons.

Two practical points.

**Your fork does not update itself.** It was a copy taken at one moment. Weeks later `upstream/main` has moved and yours has not, so branch from a stale point and your PR carries other people's already-merged work. The fix is to fetch upstream and fast-forward before starting:

```
git fetch upstream
git switch main
git merge --ff-only upstream/main
git push origin main
```

(GitHub's "Sync fork" button does exactly this.)

**"Allow edits by maintainers"** is the checkbox on the PR form that lets the project's maintainers push to your PR branch. Without it they can only ask you to fix things; with it they can rebase your branch or resolve a small conflict themselves. It is worth leaving on — and it grants access to that one branch, not to your fork.

**Say it out loud:** "`origin` is my fork and I push to it; `upstream` is theirs and I only ever read from it."

---

## 12. Why teams work this way

The ritual costs real time. What is it buying?

**Nothing reaches `main` that nobody else has seen.** That is the actual product. Every other benefit — the discussion, the checks, the record — follows from that one rule.

Things that make a PR easy to review, all of which follow from what the earlier sections showed:

- **One idea per PR.** Not "one file", not "one day's work" — one reason. Two unrelated changes should be two PRs, because a reviewer who disagrees with half of one PR cannot approve the other half.
- **Small.** A 50-line diff gets real comments. A 2,000-line diff gets "LGTM" and nobody learns anything.
- **The description explains *why*.** The diff already says what changed — the reviewer can read it. What they cannot read is what you were trying to do, what else you tried, and what you are unsure about. Write that.
- **Draft PRs are for work in progress.** Open one early to show direction or trigger the checks; it cannot be merged until you mark it ready, and reviewers know not to spend time yet.
- **Link the issue.** `Closes #12` gives the next person the context that no diff can carry.
- **Branch from current base and push early.** Both keep the merge base recent, which keeps conflicts small (Section 8) and the diff honest (Section 3).
- **Reply to every comment.** Even "done" or "kept it, because…". A thread left silent stalls the PR, and, per Section 5, only the reviewer can lift a Request changes.

And from the reviewer's side: review promptly, because an open PR ages badly — `main` moves under it, conflicts accumulate, and the author has moved on. Say clearly whether you are blocking or just remarking. Approve when it is good enough, not when it is what you would have written.

**Say it out loud:** "The whole ritual buys one thing: nothing lands on main that only one person has seen."

---

## One-page reference

**Anatomy of a pull request**

| Term | Meaning |
|---|---|
| base | the branch the work should end up in (`main`) |
| compare / head | the branch holding the work (`feature/greet`) |
| merge base | newest commit both branches share — `git merge-base A B` |
| the diff | merge base ↔ tip of compare (a **three-dot** diff) |
| draft | open, but explicitly not ready to merge |
| the PR itself | a row in the forge's database; **no** Git object |

**Two dots vs three dots**

| Form | Compares | Answers |
|---|---|---|
| `main..feature` | tip ↔ tip | how the two branches differ |
| `main...feature` | **merge base** ↔ tip of feature | what feature adds — **this is the PR's Files changed** |

**The three merge buttons**

| | Parents | Original commits | History |
|---|---|---|---|
| Merge commit | **two** | kept, same hashes | shows the branch |
| Squash and merge | one | replaced by one new commit | straight line, one commit per PR |
| Rebase and merge | one | replayed, **new hashes** | straight line, commits kept |

All three produce the **same tree** — identical files. Only the history differs.

**Why the button is grey**

- conflicts with base *(the only Git-level reason)*
- a required check is failing — or still pending
- not enough approvals
- someone's "Request changes" is unresolved
- unresolved conversations
- CODEOWNERS has not approved
- branch is behind base *(if that rule is on)*
- you lack permission to merge

**Review actions that move the gate:** Approve, Request changes. Nothing else.

**PR end states**

| State | Commits on base? | Reopenable |
|---|---|---|
| Merged | yes | no |
| Closed | no | yes, while the branch exists |

**Remotes, when forking**

| Remote | Repository | Push? |
|---|---|---|
| `origin` | your fork | yes |
| `upstream` | the original | no — fetch only |

**Conflict markers**

| Marker | Meaning |
|---|---|
| `<<<<<<< HEAD` | your branch's version starts |
| `=======` | divider — delete it |
| `>>>>>>> origin/main` | incoming version ends |

**Rules to keep:**
- Git has no pull request. The push moved the commits; the PR is a note.
- A PR stores a **branch name**, so pushing to the branch updates the PR.
- "Files changed" is measured from the **merge base**, not from today's base.
- Only "no conflicts" is Git. Every other grey button is a rule someone chose.
- All three merge buttons give identical files and different histories.
- A conflict means two edits to one line — the **author** resolves it.
- Branch protection is a server-side hook; `--no-verify` cannot reach it.
- The merge button works because the **server** does the merge.
- Deleting a merged branch deletes a file holding a hash, not your commits.
- Undo a merged PR with a revert, never by rewriting shared history.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| pull request (PR) | పుల్ రిక్వెస్ట్ | "నా బ్రాంచ్‌ని మీ బ్రాంచ్‌లో కలపండి" అనే అభ్యర్థన |
| forge | ఫోర్జ్ | రిపోను హోస్ట్ చేసే వెబ్‌సైట్ (GitHub, GitLab) |
| base branch | బేస్ బ్రాంచ్ | పని చేరవలసిన బ్రాంచ్ (సాధారణంగా `main`) |
| compare branch | కంపేర్ బ్రాంచ్ | మీ పని ఉన్న బ్రాంచ్ |
| merge base | మెర్జ్ బేస్ | రెండు బ్రాంచ్‌లకూ ఉమ్మడిగా ఉన్న చివరి కమిట్ |
| diff | డిఫ్ | ఏమి మారిందో చూపే జాబితా |
| review | రివ్యూ | మరొకరు మీ కోడ్‌ను పరిశీలించడం |
| approve | అప్రూవ్ | "ఇది సరిగ్గా ఉంది" అని రివ్యూయర్ చెప్పడం |
| request changes | రిక్వెస్ట్ ఛేంజెస్ | "ఇంకా కాదు — మార్చండి" — మెర్జ్‌ను ఆపుతుంది |
| merge commit | మెర్జ్ కమిట్ | రెండు పేరెంట్‌లు ఉన్న ఏకైక రకం కమిట్ |
| squash | స్క్వాష్ | అన్ని కమిట్‌లను ఒక్క కమిట్‌గా చేయడం |
| rebase | రీబేస్ | కమిట్‌లను కొత్త బేస్ మీద మళ్ళీ ఆడించడం — కొత్త హాష్‌లు |
| conflict | కాన్‌ఫ్లిక్ట్ | ఒకే లైన్‌ను ఇద్దరు వేరువేరుగా మార్చడం |
| resolve | రిజాల్వ్ | కాన్‌ఫ్లిక్ట్‌ను సరిచేసి మార్కర్‌లు తొలగించడం |
| check / CI | చెక్ | పుష్ తర్వాత సర్వర్‌లో తనంతట తానే నడిచే పరీక్ష |
| protected branch | రక్షిత బ్రాంచ్ | నేరుగా పుష్ చేయనివ్వని బ్రాంచ్ |
| fork | ఫోర్క్ | వేరొకరి రిపోకు మీ సొంత కాపీ |
| upstream | అప్‌స్ట్రీమ్ | అసలు రిపో — చదవడమే, పుష్ కాదు |
| origin | ఆరిజిన్ | మీ సొంత కాపీ — దీనికి పుష్ చేస్తారు |
| draft PR | డ్రాఫ్ట్ పీఆర్ | ఇంకా పూర్తి కాలేదని గుర్తు పెట్టిన PR |
| revert | రివర్ట్ | వ్యతిరేక మార్పుతో కొత్త కమిట్ — చరిత్రను చెరపకుండా |
