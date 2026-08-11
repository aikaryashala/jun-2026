# Pull Requests — Question Bank

Answer on paper, using the reference page's ideas: a pull request is a **record kept by a website**, not a Git command; it names a **base** and a **compare** branch and stores a *branch name*, not a snapshot; its diff is measured from the **merge base**; the merge button offers three histories of the same files; and a protected branch is a rule enforced on the **server**.

Answers are **not** in this file.

---

# Part A — Multiple Choice

**A1.** Opening a pull request on GitHub creates how many new objects in the repository's `.git/objects/`?

- A) one per commit on the branch
- B) one — the pull request object
- C) zero
- D) two — a tree and a commit

**A2.** A pull request stores which of these?

- A) a copy of the changed files
- B) the list of commit hashes, frozen when the PR was opened
- C) the name of the compare branch
- D) a patch file generated at open time

**A3.** `git merge-base main feature` prints:

- A) the newest commit that both branches have
- B) the first commit of the repository
- C) the tip of `main`
- D) the commit that will be created by merging

**A4.** GitHub's "Files changed" tab shows the same thing as:

- A) `git diff main..feature`
- B) `git diff main...feature`
- C) `git diff feature..main`
- D) `git log main..feature`

**A5.** A PR has been open for two weeks and forty other PRs have merged into `main` in that time. Its "Files changed" tab shows:

- A) every file changed by all forty PRs as well
- B) only the files this PR's branch changed
- C) nothing, until the branch is updated
- D) the forty PRs' changes shown as deletions

**A6.** Asha pushes a new commit to `feature/greet` while PR #7 is open. To make the PR show it, she must:

- A) close PR #7 and open a new one
- B) press "Update pull request"
- C) do nothing — it already shows it
- D) re-request review, which re-reads the branch

**A7.** Which review action prevents the merge until the same reviewer changes their mind?

- A) a single line comment
- B) Review: Comment
- C) Review: Approve
- D) Review: Request changes

**A8.** Which of these is the only reason a merge button is grey that comes from **Git** rather than from repository settings?

- A) not enough approvals
- B) a required check is failing
- C) a conflict with the base branch
- D) conversations are unresolved

**A9.** A commit created by "Create a merge commit" is distinguished by having:

- A) no parent
- B) one parent
- C) two parents
- D) no tree

**A10.** After "Rebase and merge", the commits that land on `main`:

- A) are the original commits, with their original hashes
- B) have the same changes but new hashes
- C) are combined into one commit
- D) keep their hashes but gain a second parent

**A11.** The same pull request is merged three times over in three copies of the repository — once with each button. Comparing the resulting `main` in the three copies:

- A) the files differ, and the histories differ
- B) the files are identical, and the histories differ
- C) the files differ, and the histories are identical
- D) both the files and the histories are identical

**A12.** In a conflicted file, the line `=======` marks:

- A) the end of the incoming version
- B) the divider between the two versions, to be deleted
- C) a line both sides agree on
- D) the merge base's version of the line

**A13.** A pull request has a conflict with `main`. Whose job is it to resolve it?

- A) the reviewer, before approving
- B) the author, on the compare branch
- C) the repository owner
- D) whoever merged the conflicting change

**A14.** `main` is a protected branch. Which of these lets you push directly to it anyway?

- A) `git push --no-verify`
- B) `git push --force`
- C) `git commit --no-verify` then push
- D) none of these

**A15.** When you press "Merge pull request", the merge is performed:

- A) on your machine, then pushed automatically
- B) on the server, by GitHub
- C) on your machine, bypassing the protection with a special flag
- D) by the CI system that ran the checks

**A16.** In a fork-based workflow, the remote conventionally named `upstream` is:

- A) your fork, which you push to
- B) the original repository, which you only fetch from
- C) your local clone
- D) the branch your PR targets

---

# Part B — Fill in the Blanks

**B1.** The Git command `git pull-request` does not exist, because a pull request is provided by the ____________, not by Git.

**B2.** The two branches named by every pull request are the ____________ branch and the ____________ branch.

**B3.** The newest commit that both branches already share is called the ____________.

**B4.** A diff written with ____________ dots compares tip with tip, while one written with ____________ dots compares from the merge base.

**B5.** A pull request updates itself when you push, because it stores a ____________ rather than a list of commits.

**B6.** The two review actions that affect whether a PR can be merged are ____________ and ____________.

**B7.** A merge commit is the only kind of commit that has more than one ____________ line.

**B8.** Merging with ____________ flattens all of a branch's commits into a single new commit on the base.

**B9.** All three merge buttons produce exactly the same ____________, so the files on the base branch are identical whichever is used.

**B10.** The three markers Git writes into a conflicted file are `<<<<<<<`, ____________ and `>>>>>>>`.

**B11.** A protected branch is enforced on the ____________, which is why the `--no-verify` flag cannot get past it.

**B12.** Writing ____________ `#12` in a pull request's description closes issue 12 when the PR merges.

---

# Part C — Scenario Questions

**C1.** Asha branches off `main` and changes only `greet.c`. While she works, Ravi pushes a commit to `main` adding two lines to `README.md`. Asha then runs `git diff main..feature/greet` and the output claims her branch **deletes** two lines from `README.md`.
(a) Has Asha deleted anything?
(b) Explain why the command printed that.
(c) Which command shows what her PR actually proposes, and why does its output differ?

**C2.** A student opens a PR expecting it to contain 2 commits and 1 changed file. GitHub says "wants to merge 23 commits into main" and shows 40 changed files. The code they wrote is genuinely small.
(a) What has most likely gone wrong?
(b) Which of the four names from Section 2 is not where they assumed?
(c) What should they have done before starting the branch?

**C3.** Ravi leaves eight comments in the Files changed tab and closes the browser. Days later he complains the PR has not moved; Asha says she is waiting for his review.
(a) Formally, has Ravi reviewed the PR?
(b) What did he need to do that he did not?
(c) Which two review outcomes actually move the gate?

**C4.** A PR has one approval and all checks green, but the merge button is still grey. The repository requires one approval and passing checks.
(a) Give three different conditions that could still be blocking it.
(b) Which single one of the greyed-button conditions is a fact about Git rather than a setting?
(c) A check shows as **pending** rather than failing. Is the PR blocked? Explain the difference.

**C5.** Two PRs are open. PR-1 renames the function `greet` to `welcome` everywhere. PR-2 adds one new call to `greet()` in a file PR-1 does not touch. Both show green checks. PR-1 is merged, then PR-2 is merged.
(a) Will Git report a conflict when PR-2 merges? Why or why not?
(b) What happens when someone next builds `main`?
(c) What is this situation called, and which repository setting would have caught it?

**C6.** Asha's PR branch has three commits: `add function`, `wip`, and `fix typo`. The team wants `main` to read as one clear commit per change, and does not want `wip` in the permanent history.
(a) Which merge button should they use?
(b) What happens to the three original commits?
(c) If instead they wanted all three kept as separate commits but with no merge bubble in the graph, which button, and what changes about those commits?

**C7.** Asha rebases her PR branch onto the latest `main` and force-pushes. Ravi, who had already reviewed it, finds that his comments are marked "outdated" and hidden.
(a) Why did that happen — what is different about the commits now?
(b) Was any work lost?
(c) Give one reason to do this anyway, and one reason not to during an active review.

**C8.** A PR was merged into `main` yesterday and its branch was deleted. Today the change turns out to be wrong and must come out.
(a) Are the merged commits still reachable after the branch was deleted? Explain using what a branch is.
(b) What does GitHub's Revert button actually produce?
(c) Why is that preferred over removing the commits from `main`'s history?

**C9.** A student has forked `aikaryashala/jun-2026`, cloned their fork, and worked for three weeks. Their new PR shows commits by four other people that were merged upstream during that time.
(a) Which repository is their `origin`, and which is `upstream`?
(b) Why do the other people's commits appear in their PR?
(c) Write the sequence of commands that would have prevented it.

**C10.** A team turns on branch protection for `main` requiring two approvals. A developer, in a hurry, tries `git push --force origin main` and it is refused.
(a) Which mechanism refused it, and on which machine did that mechanism run?
(b) The developer asks whether `--no-verify` would help. Answer, and explain what `--no-verify` actually skips.
(c) The merge button still updates `main` successfully. Explain why that is not a contradiction.

---

# Part D — Read the History

For each, say **which merge button** produced the result, and give the reason in the graph that tells you.

**D1.**
```
*   9aab251 Merge pull request #7 from asha/feature/greet
|\
| * b17cc20 Greet a name
| * 8aa453f Move greeting into a function
* | 11a9d5f Document the build command
|/
* d5e5b26 Initial project
```

**D2.**
```
* 4529aba Greet a name (#7)
* 11a9d5f Document the build command
* d5e5b26 Initial project
```

**D3.** The branch before merging held `8aa453f` and `b17cc20`; afterwards `main` reads:
```
* 6f523d7 Greet a name
* c6fd0b9 Move greeting into a function
* 11a9d5f Document the build command
* d5e5b26 Initial project
```
(Say which button, and explain the two hashes.)

**D4.** Here is a commit from the base branch:
```
tree b53b12e6c5218ff58a8502176e904308d4bcea5f
parent 11a9d5f5d2ed6f35624137bbfdc8eedc3d50e155
parent b17cc20f028d01533aab78433ea5e787c2f70fb5
author Ravi Kumar <ravi@example.com> 1786...
```
(a) Which button? (b) Which line proves it? (c) What was each parent before the merge?

**D5.** The three merges in D1, D2 and D3 were each done from the same starting point. All three resulting `main` commits have tree `b53b12e6c5218ff58a8502176e904308d4bcea5f`.
(a) What does that tell you about the files in the three copies?
(b) So what exactly did the choice of button decide?

**D6.** Given this state, say what the PR's "Files changed" tab shows, and why:
```
* 41963b6 Say Namaste          ← main
| * 995fa62 Greet the world    ← feature/greet
|/
* 1189799 Initial project
```
Both commits change the same line of `greet.c`.
(a) Which commit is the merge base?
(b) Can this PR be merged as it stands?
(c) After the author resolves it, does the PR's diff normally get bigger or smaller? Explain.
