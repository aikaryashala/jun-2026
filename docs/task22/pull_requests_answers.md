# Pull Requests — Answer Key

Check the **reasoning**, not just the letter. If you picked the right option for the wrong reason you will get the next question wrong, and the reasoning is the only part that transfers to a real pull request.

Everything here comes from five facts: a PR is a **row in the forge's database** naming two branches; its diff is measured from the **merge base**; it tracks a **branch name**, not a snapshot; the three merge buttons give the **same files** and different histories; and branch protection runs on the **server**.

---

# Part A — Multiple Choice

**A1 — C) zero.**
The commits reached the server when the author ran `git push`. Opening the PR writes a row in GitHub's database — base, compare, author, state — and touches no Git object and no branch. This is why opening a PR is instant, and why closing and reopening one has no effect on your code.

**A2 — C) the name of the compare branch.**
Not the files (A), not a frozen commit list (B), not a patch (D). Everything the PR displays is looked up from whatever `refs/heads/feature/greet` points to *at the moment you load the page*. A4, A5 and A6 are all consequences of this one fact.

**A3 — A) the newest commit that both branches have.**
The point where the two branches last agreed — the fork point. Not the repository's first commit (B): that is only the answer when the branches shared nothing after it. In the reference page's example it is `d5e5b26`, the "Initial project" commit.

**A4 — B) `git diff main...feature`.**
Three dots. The tab answers "what would merging change?", which is measured from the merge base, not from the tip of `main`. Option A (two dots) is the one that produces the misleading output in C1; option D lists commits rather than changes.

**A5 — B) only the files this PR's branch changed.**
Because the diff starts at the merge base, which is behind all forty of those merges. Their work is simply not part of the comparison. Note this is a strength *and* the trap in C5: your PR's diff can be green and honest and still not tell you what `main` will look like afterwards.

**A6 — C) do nothing — it already shows it.**
The PR holds a branch name (A2). Push, and the branch file on the server holds a new hash, so the PR redraws itself. There is no "Update pull request" button because there is nothing to update — which is why "push a fix to the same branch" is the whole answer to review feedback.

**A7 — D) Review: Request changes.**
It is a lock held by one specific person. It clears when *that* reviewer approves, or when someone with permission dismisses their review — pushing a fix does not clear it, because only the reviewer can judge whether the fix answers the objection. A and B carry no verdict; C is the opposite of blocking.

**A8 — C) a conflict with the base branch.**
The only condition on the list that Git itself imposes: it genuinely cannot combine the two sides without a person choosing. A, B and D are all switches somebody turned on in the repository's settings — which is why an identical PR merges instantly in one repository and sits blocked in another.

**A9 — C) two parents.**
Task 21 showed a normal commit carrying one `parent` line and the very first commit carrying none. A merge commit is the only kind with more than one, and the two parents are exactly the two branch tips that were joined.

**A10 — B) have the same changes but new hashes.**
Rebase **replays** each commit onto a new base. A commit's contents include its parent, and its hash is computed from its contents (Task 21), so a new parent forces a new hash. The reference page's `8aa453f` became `c6fd0b9` and `b17cc20` became `6f523d7`. These are new commits carrying the same changes, not the original commits moved.

**A11 — B) the files are identical, and the histories differ.**
Verified directly in the reference page: all three resulting commits have tree `b53b12e6…`. Check out `main` afterwards and no inspection of the code can tell you which button was pressed — only the log can. The choice of button is a choice about the record, never about the result.

**A12 — B) the divider between the two versions, to be deleted.**
It separates your version (above, under `<<<<<<< HEAD`) from the incoming one (below, ending at `>>>>>>>`). It is not content and not a hint; resolving means all three marker lines are gone from the file.

**A13 — B) the author, on the compare branch.**
Not politeness — permission. The fix has to be a commit on the compare branch, and typically only the author can push there. (This is exactly what the "allow edits by maintainers" checkbox exists to change.) The reviewer usually *cannot* resolve it even if willing.

**A14 — D) none of these.**
`--no-verify` skips **client** hooks — `pre-commit`, `commit-msg`, `pre-push` — which live on your machine and are yours to skip. Branch protection runs on the server, where your flags never arrive. `--force` is still a push, and protection normally refuses force-pushes specifically. Task 21's rule: client hooks are a reminder, server hooks are a rule.

**A15 — B) on the server, by GitHub.**
Your browser sends one request saying "merge PR #7"; the server does the merge in its own copy and updates `refs/heads/main` itself, as the forge, under the rule that an approved PR may do so. Nothing is pushed from your machine — which is how a branch nobody can push to still gets updated (C10c).

**A16 — B) the original repository, which you only fetch from.**
`origin` is your fork (yours, pushable); `upstream` is the project you forked (read-only to you). Git attaches no meaning to either name — the convention is entirely human, but it is universal.

---

# Part B — Fill in the Blanks

**B1** — **forge** (the hosting website: GitHub, GitLab, Gitea, Bitbucket). `git pull-request` is not a Git command in any version.

**B2** — **base** and **compare** (compare is also called *head*). Base is where the work should end up; compare is where it currently is.

**B3** — the **merge base**. Found with `git merge-base main feature`.

**B4** — **two** dots (`main..feature`) compares tip with tip; **three** dots (`main...feature`) compares from the merge base. The three-dot form is the PR's.

**B5** — a **branch name** (a ref). Not a snapshot, not a commit list.

**B6** — **Approve** and **Request changes**. Plain comments — single or bundled as "Review: Comment" — carry no verdict and move nothing.

**B7** — **`parent`**. Two `parent` lines is the signature of a merge commit.

**B8** — **squash** ("Squash and merge").

**B9** — the same **tree**. `b53b12e6…` in all three cases in the reference page.

**B10** — **`=======`**. Order in the file: `<<<<<<<` (yours), `=======` (divider), `>>>>>>>` (incoming).

**B11** — the **server**. `--no-verify` only reaches hooks running on your own machine.

**B12** — **`Closes`** (`Fixes` and `Resolves` behave identically). A bare `#12` links the issue without closing it.

---

# Part C — Scenario Questions

### C1 — the two-dot diff that invents a deletion

**(a)** No. Asha changed only `greet.c`. She has never edited `README.md` and has never even had Ravi's two lines in her working copy.

**(b)** Two dots compares the **tip of `main`** with the **tip of `feature/greet`** as two piles of files, with no regard for how either got there. `main`'s pile contains the build lines (Ravi added them after Asha branched); Asha's pile does not. Something present on the left and absent on the right is displayed as a deletion. The output is arithmetically correct — the *question* was wrong.

**(c)** `git diff main...feature/greet` — three dots. It compares the **merge base** with Asha's tip, so the baseline is the shared commit from before Ravi's change, and Ravi's lines are absent from both sides of the comparison. `README.md` therefore disappears from the output entirely and only `greet.c` remains. This is what the PR's Files changed tab shows.

### C2 — 23 commits and 40 files

**(a)** The branch was created from the wrong starting point — most likely from another feature branch, or from a `main` that had not been updated in a long time, so everything on that base that is missing from today's `main` is now being proposed as part of this PR.

**(b)** The **merge base**. They assumed it was the current tip of `main`; it is actually far further back. The commit count and the diff both start there, so both are inflated. Base, compare and the diff all look right — the merge base is the one that moved.

**(c)** Fetch and update `main` first, then branch from it:
```
git switch main
git pull
git switch -c feature/whatever
```
The fix once it has happened is to rebase the branch onto current `main`, or to start a clean branch from `main` and move just the intended commits onto it.

### C3 — eight comments and no review

**(a)** No. Formally he has left eight individual comments and submitted nothing. The PR has zero reviews.

**(b)** He needed to **submit a review** — bundling the comments and choosing a verdict, either **Approve** or **Request changes**. Individual comments are posted one at a time and carry no verdict, so nothing in the gate ever changed state.

**(c)** **Approve** and **Request changes**. Those two are the only review outcomes the merge gate looks at; every other kind of remark is conversation. Note the practical cost of the confusion: Asha was waiting for a state change that could never arrive, so the PR aged for days for no reason.

### C4 — one approval, green checks, grey button

**(a)** Any three of: a **conflict** with `main`; an unresolved **"Request changes"** from another reviewer; **unresolved conversations**, if the repository requires them resolved; a **CODEOWNERS** approval still missing for a path the PR touches; the **branch being behind base**, if "require branches to be up to date" is on; the person looking at the button lacking **permission** to merge; or a required check that is **pending** rather than passed.

**(b)** The **conflict**. It is the only one that is a fact about the two branches rather than a switch in the settings — Git genuinely cannot combine them without a human choosing. Every other entry could be turned off, and in a repository with no protection at all the button would be green.

**(c)** Yes, blocked — but not failing. Pending means no result has been reported yet: the job is queued or still running. The gate needs a *pass*, and "no answer" is not a pass. The difference matters because a failing check needs you to fix something, while a pending one usually needs you to wait — and if it stays pending forever, the CI system never started, which is an infrastructure problem, not a problem with your code.

### C5 — two green PRs that break the build

**(a)** No conflict. PR-1 edits the files that define and call `greet`; PR-2 edits a different file that PR-1 never touches. No line is changed by both sides, so Git combines them happily. Conflicts are about **overlapping lines**, nothing more.

**(b)** The build fails. `main` now defines `welcome` (PR-1 renamed it everywhere it could see) and contains a call to `greet()` that PR-2 introduced in a file PR-1 had no reason to look at. The function no longer exists under that name.

**(c)** A **semantic conflict** — the two changes are textually independent and logically incompatible. The setting that catches it is **"require branches to be up to date before merging"**: PR-2 would have had to take PR-1's merge into its branch first, and the checks would then have run on the actual combination that was about to land, going red before the merge instead of after. This is the same fact as A5 seen from the other side — the three-dot diff shows what your branch adds, not what `main` will look like.

### C6 — a branch with a `wip` commit

**(a)** **Squash and merge.** All three commits are flattened into one new commit on `main`, so `wip` and `fix typo` never appear in the permanent history.

**(b)** They are **not** part of `main`'s history. The single new squash commit has one parent (the old tip of `main`) and contains their combined effect under an entirely new hash. The originals continue to exist as objects for as long as the branch points at them; once the branch is deleted nothing references them and they are eventually cleaned up.

**(c)** **Rebase and merge.** All three stay as separate commits and `main` stays a straight line with no merge commit — but each is **replayed onto the new base**, so all three get **new hashes** (A10). They are new commits carrying the same changes.

### C7 — the force-push that hid the review

**(a)** Rebasing replays every commit onto the new base, producing new commits with new hashes; the force-push then pointed the branch at those and abandoned the originals. Ravi's comments were anchored to lines in commits that the branch no longer contains, so GitHub can no longer show them in place and marks them outdated.

**(b)** No. Nothing was lost. The comments still exist and can be expanded; the code is all present, in the replayed commits. The damage is entirely to the *review* — Ravi has to re-read a diff he had already worked through.

**(c)** **A reason to do it:** it keeps history a clean straight line and removes an out-of-date merge base, which can be worth it before a final merge, or on a PR nobody has reviewed yet. **A reason not to:** mid-review it discards the reviewer's place in the work and costs them the whole read again. The usual etiquette is to update with a plain merge while a review is in progress, and save any rebasing for before the review starts or after it finishes. If you must force-push, `--force-with-lease` at least refuses to clobber commits you have not seen — though it does nothing about the rudeness.

### C8 — undoing a merged PR

**(a)** Yes, still reachable. A branch is a small file holding one hash (Task 21); deleting the branch deletes that file, not the commits. After the merge those commits are reachable from `main` itself, which is the entire point of having merged them. Deleting a merged branch is always safe.

**(b)** A **new pull request**, containing a new commit that applies the opposite changes — every added line removed, every removed line restored. History is not rewritten: the original merge stays in the log, followed later by its revert. Being a normal PR, it can be reviewed and must pass the same gate.

**(c)** Because `main` is shared. Removing commits from its history would rewrite it, and every person who has already pulled `main` would have a version that no longer matches the server — forcing them all to repair their clones by hand, and breaking anything that referred to those commit hashes. Two visible commits in the log is a small, honest price. The general rule: **never rewrite history that other people already have.**

### C9 — the fork that went stale

**(a)** `origin` is **their fork**, `github.com/<student>/jun-2026` — the one they clone and push to. `upstream` is **`aikaryashala/jun-2026`**, the original, which they can only fetch from.

**(b)** A fork is a copy taken at one instant, and it does not update itself. Three weeks later `upstream/main` has moved on and the fork's `main` has not. Branching from that stale `main` put the merge base three weeks in the past, so everything merged upstream since — including four other people's commits — falls between the merge base and their branch tip and is counted as part of their proposal. Same underlying error as C2: the merge base is not where they thought.

**(c)** Before creating the branch:
```
git fetch upstream
git switch main
git merge --ff-only upstream/main
git push origin main
git switch -c feature/whatever
```
(GitHub's "Sync fork" button does the middle three.) `--ff-only` is deliberate: it refuses rather than creating a merge commit, which tells you immediately if you have accidentally committed to your fork's `main`.

### C10 — the refused force-push

**(a)** **Branch protection**, running on the **server** — GitHub's equivalent of the `pre-receive` hook from Task 21. It inspects the incoming ref update before any ref changes and declines it. Note that a force-push is refused by protection specifically, and would be refused even if a normal push were allowed.

**(b)** It would not help at all. `--no-verify` skips **client-side** hooks — `pre-commit`, `commit-msg` and `pre-push` — which run on the developer's own machine and are theirs to skip. The rule that refused this push runs on a different computer, which never sees the flag. That is precisely why serious rules are put on the server.

**(c)** Because the merge button does not push from anyone's machine. The **server** performs the merge in its own copy and updates `refs/heads/main` itself, under its own rule: *an approved pull request that passed the gate may update this branch* (A15). The protection is not being bypassed — it is being satisfied. "Nobody may push to `main`" and "an approved PR may be merged into `main`" are two different rules about two different routes in.

---

# Part D — Read the History

**D1 — Create a merge commit.**
Two giveaways. The `|\` immediately under the top commit means it has **two parents**, and the two lines of history run side by side (`| *` and `* |`) before rejoining at `d5e5b26`. Asha's `8aa453f` and `b17cc20` are present with their **original hashes**, and the graph records that a branch existed and when it came back.

**D2 — Squash and merge.**
`main` is a straight line, the branch's two commits are nowhere in it, and one new commit `4529aba` has appeared carrying their combined effect. The `(#7)` in the message is GitHub's default squash title, which appends the PR number. `4529aba` has exactly one parent, `11a9d5f`.

**D3 — Rebase and merge.**
Both commits are still there separately and there is no merge commit — but the hashes have changed: `8aa453f` → `c6fd0b9` and `b17cc20` → `6f523d7`. Each was **replayed** on top of `11a9d5f`. A commit's contents include its parent and its hash is computed from its contents (Task 21), so a new parent forces a new hash. These are new commits carrying the same changes, not the originals relocated.

**D4 —**
**(a)** Create a merge commit.
**(b)** The **two `parent` lines**. Only a merge commit has more than one; a normal commit has one and the very first has none.
**(c)** `11a9d5f` was the tip of **`main`** (the base branch), and `b17cc20` was the tip of **`feature/greet`** (the compare branch). A merge commit's parents are exactly the two branch tips that were joined, in that order — first parent is the branch merged *into*.

**D5 —**
**(a)** The files are **identical** in all three copies, byte for byte. A tree is the complete snapshot of the project (Task 21), so the same tree hash means the same content throughout — check out `main` in any of the three and nothing distinguishes them.
**(b)** Only what the **history records**: whether a branch is visible in the graph, whether the individual commits survive, and whether their hashes are the originals. The button decides what the log remembers, never what the code becomes.

**D6 —**
**(a)** `1189799` ("Initial project") — the newest commit both branches have. `41963b6` is only on `main`; `995fa62` is only on `feature/greet`.
**(b)** No. Both commits change the **same line** of `greet.c` since the merge base, and Git will not choose between two rewrites of one line. GitHub shows "This branch has conflicts that must be resolved" and the button is grey.
**(c)** **Smaller.** Resolving means bringing `main` into the branch, which moves the merge base forward to include `41963b6`. Ravi's `Namaste` is then part of the baseline instead of part of the proposal, so the diff shrinks to only what is still genuinely being proposed — in the reference page's example, just the addition of `, world`. A PR's diff is always measured from the merge base, so moving the merge base forward necessarily removes from the diff anything the base already has.

---

## The pattern to notice

Nearly every question here is one of five facts wearing a different hat.

**The PR stores a branch name, not a snapshot** — so it updates on push (A6), so a force-push moves the ground under a reviewer (C7), so deleting the branch empties the PR.

**The diff is measured from the merge base** — so a two-dot diff invents deletions (C1), so a stale branch point inflates a PR to 40 files (C2, C9), so resolving a conflict makes a diff smaller (D6), and so a green PR can still break the build (C5).

**The hash comes from the contents, and the contents include the parent** — so rebasing renumbers everything (A10, D3), so a squash commit is a new commit rather than a moved one (C6b).

**All three buttons write the same tree** — so the choice is only ever about the record (A11, D5).

**Client hooks are a reminder; server rules are rules** — so `--no-verify` never reaches branch protection (A14, C10), and the merge button works because the server, not you, performs the merge (A15).

Learn the five and the rest is derivable — including the situations this question bank did not cover.
