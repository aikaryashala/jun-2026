# Build a Mini HTML Parser — Quiz

A quick check of the basics before (and during) the assignment. Try each question first, **then** click **Show answer** to check yourself. Read the reason, not just the letter.

---

## OpenCode

**1. OpenCode has a Plan mode and a Build mode. What is the difference?**

- A. Plan mode is faster; Build mode is more accurate
- B. Plan mode only discusses and proposes; Build mode is allowed to change your files
- C. Plan mode writes the tests; Build mode writes the code
- D. There is no difference; they are two names for the same mode

<details><summary>Show answer</summary>

**B.** In Plan mode the agent can read your project and suggest *how* it would do something, but it does not edit your files. Build mode lets it make the changes. Plan first, read the plan, *then* build — that is how you stay in charge of the design.

</details>

**2. What is the `AGENTS.md` file that OpenCode can generate for a project?**

- A. A list of every person who worked on the project
- B. A log of every prompt you have typed
- C. A description of the project — its structure and conventions — that the agent reads to understand it
- D. A licence file

<details><summary>Show answer</summary>

**C.** `AGENTS.md` tells the agent what this project is and how its code is organised. It belongs in Git with your code. The agent writes a first version, but **you** should read it and correct anything wrong — a wrong `AGENTS.md` misleads every later session.

</details>

**3. Which prompt is the better one to give OpenCode in Phase 5?**

- A. `make the parser better`
- B. `finish the assignment`
- C. `Add attribute parsing: store each name="value" pair on the element in source order, and print them as [name="value", ...]. Don't change the tree printer's branch logic.`
- D. `fix everything`

<details><summary>Show answer</summary>

**C.** It names **one** feature, says exactly what correct looks like, and says what must **not** change. The others give the agent no way to know when it is done — so it guesses, and you get code you did not design.

</details>

**4. OpenCode writes a test, and the test passes. What must you still check?**

<details><summary>Show answer</summary>

That the test's **expected output is actually right**. If the agent misunderstood the rules, it can write wrong code *and* a test that expects the same wrong answer — and it will pass. Work out the expected output by hand (or compare with the sample files in the assignment) before you trust the test.

</details>

---

## Entire

**5. In Entire, what is a checkpoint?**

- A. A backup copy of your whole hard disk
- B. A Git branch for each new feature
- C. A code change linked to the agent session behind it — the prompt, the conversation, and the tool calls
- D. A test that must pass before you can commit

<details><summary>Show answer</summary>

**C.** A checkpoint pairs **what changed** (your commit) with **why and how it changed** (the agent session). That pairing is the whole point: Git alone has the first half only.

</details>

**6. Where does Entire keep the checkpoint data?**

- A. Inside your source files, as comments
- B. In your own Git repository, but separate from your source branches
- C. In your commit messages
- D. Only in the browser, never on your machine

<details><summary>Show answer</summary>

**B.** Checkpoints live in your own repository, stored apart from your normal branches, so your code history stays clean and the context travels with the repository when you push. You can also browse them on entire.io.

</details>

**7. Why must Entire be enabled from the first commit, not at the end?**

<details><summary>Show answer</summary>

Entire can only record sessions **while they happen**. Turn it on at the end and every earlier decision, wrong attempt, and reason is already gone — you would have checkpoints only for the last few commits, the least interesting ones.

</details>

**8. Fill in the blanks:** Git records **______** changed. Entire also records **______** it changed — the prompt and the conversation behind the change.

<details><summary>Show answer</summary>

Git records **what** changed. Entire also records **why** (and how) it changed. A diff shows the new line; it cannot show the three alternatives you rejected before choosing it.

</details>

---

## Git and GitHub

**9. Which of these are meaningful commit messages?**

1. `update`
2. `Add recursive tree printer`
3. `final`
4. `Detect unexpected closing tags`
5. `changes`

<details><summary>Show answer</summary>

**2 and 4.** Each says what changed, in a few words, in the imperative. `update`, `final`, and `changes` would fit *any* commit in *any* project — so they tell a reader nothing.

</details>

**10. Why commit at the end of every phase instead of once at the end?**

<details><summary>Show answer</summary>

Small commits make the history tell the story of how the program grew, let you go back to the last working state when something breaks, and give Entire a checkpoint per step — so each piece of context sits next to the change it explains.

</details>

---

## The parser

**11. Your program prints exactly the right tree for every sample, but it never builds a tree — it just re-indents the HTML text. Does it meet the requirement?**

<details><summary>Show answer</summary>

**No.** The output must come from **walking a DOM tree in memory**. Re-indenting text can match the samples and still have no tree to query (`--find-class` needs one) and nothing to validate against. Right output from the wrong method still fails.

</details>

**12. While parsing, you keep a list of the elements that are open right now. A closing tag arrives. Which open element is the only one it may close?**

<details><summary>Show answer</summary>

The **innermost** one — the one opened most recently. That is why a **stack** (last in, first out) fits: push on an opening tag, pop on a matching closing tag. In `<b><i>Bold italic</b></i>`, `</b>` arrives while `<i>` is innermost — so it is an error: `Unexpected closing tag: </b> (expected </i>)`.

</details>

**13. Draw the tree your program must print for this file:**

```html
<ol>
  <li class="done">Read</li>
  <li>Write</li>
</ol>
```

<details><summary>Show answer</summary>

```text
.
└── ol
    ├── li [class="done"]
    │   └── "Read"
    └── li
        └── "Write"
```

The first `li` has a sibling after it, so it gets `├──` and its child line starts with `│   `. The second `li` is the last child, so it gets `└──` and its child line starts with four spaces. The whitespace between the tags is ignored.

</details>
