# Relative vs Absolute Paths — Answers with Reasoning

Answer key for `relative-vs-absolute-paths-questions.md`. Every answer
includes the reasoning — when checking a student's work, check the **why**,
not just the letter. A correct letter with a wrong reason is a lucky guess.

---

# Part A — Multiple Choice Questions

**Q1. Answer: (c) — `/c/Users/rohinibarla/Desktop`.**
The one-line rule: if it starts with `/`, it's absolute. (a) starts with a
name, (b) starts with `..`, (d) is `.` itself — all three are relative,
because all three only mean something once you know where you're standing.

**Q2. Answer: (b) — the parent folder, one step up.**
`.` is "here", `..` is "the folder above here". These two are the building
blocks of every relative move; the family-tree picture works because `..`
literally means "go to the parent."

**Q3. Answer: (b) — prints your current folder.**
`pwd` answers "where am I?" — path-finding only makes sense once you know
where you stand, which is why the worksheet says to run it constantly.

**Q4. Answer: (c) — `/c`.**
Git Bash puts a single root `/` at the very top; each Windows drive hangs
under it as a lowercase letter. That's why home is `/c/Users/rohinibarla`,
not `C:\Users\rohinibarla`.

**Q5. Answer: (b) — `cd ../Sita`.**
Ravi and Sita share the parent `Peddamma`, so they are siblings. One `..`
climbs to `Peddamma`; then step down into `Sita`. Siblings are always
`../<name>` — one up, one down. (c) climbs too far (to `Kutumbam`, which has
no child `Sita`), and (a) looks for a `Sita` *inside* `Ravi`.

**Q6. Answer: (d) — `Kutumbam`.**
Trace it: starting in `Ravi`, the first `..` lands on `Peddamma`, the second
on `Kutumbam` — the common grandparent of Ravi and Kiran. Only from there
can you walk down the other branch: `Pinni` → `Kiran`.

**Q7. Answer: (b) — same from anywhere.**
That is the defining property: an absolute path spells the full chain from
root, so it never depends on your current folder. It's often *longer* than
the relative path (so not (a)), and it never needs `..` (so not (d)).

**Q8. Answer: (c) — jumps back to the previous folder.**
`cd -` is the "undo" of your last move — handy when you're bouncing between
two work areas. Going up one level is `cd ..`; going home is `cd ~` or plain
`cd`.

**Q9. Answer: (d) — goes to your home folder.**
`cd` alone behaves like `cd ~`. It's the quickest way to get back to a known
starting point when you're lost.

**Q10. Answer: (b) — absolute, because it is valid no matter where you are standing.**
The full postal address works from any city in the world; likewise
`/c/Users/rohinibarla/Desktop/...` works from any current folder. The
relative counterpart in the analogy is "go up one floor, then the second
door" — meaningful only from where you stand. The *reason* matters: (d)'s
"because it is long" is not what makes a path absolute.

**Q11. Answer: (a) — lands you on `Bharath`.**
First `..`: `Vijayawada` → `Andhra`. Second `..`: `Andhra` → `Bharath`. Two
dots-pairs, two steps up. (You may chain as many `..` as there are levels
above you.)

**Q12. Answer: (b) — real Linux servers are case-sensitive.**
Windows forgives `kutumbam` for `Kutumbam`, so the habit *seems* harmless —
until the same command runs on a Linux machine, where `kutumbam` and
`Kutumbam` are two different names. The worksheet teaches exact
capitalisation now so the habit transfers (see scenario Q28).

---

# Part B — Fill in the Blanks

**Q13.** starts with **`/`**.
The whole absolute-vs-relative decision is that single first character.

**Q14.** `.` means **here / the current folder**; `..` means **the parent
folder (one step up)**.

**Q15.** **`/c/Users/rohinibarla`**.
`~` is the home folder, and in Git Bash the `C:` drive is `/c`. So
`~/Desktop` and `/c/Users/rohinibarla/Desktop` are the same place.

**Q16.** **siblings**; `cd **..**/<name>`.
Same parent → one step up reaches the shared parent, one step down reaches
the sibling.

**Q17.** `cd **../Kiran**`.
Geetha and Kiran share the parent `Pinni` — siblings, so `../<name>`.

**Q18.** the lowest **common ancestor**; the number of **`..`** (steps up).
Then spell the downward chain of child names to the target. This recipe
solves *every* "here → there" question the tree can pose.

**Q19.** `cd **../../Telangana/Hyderabad**`.
Vijayawada and Hyderabad are cousins: up to `Andhra`, up to `Bharath` (the
common ancestor), then down the other branch `Telangana` → `Hyderabad`.

**Q20.** **`ls`**.
Listing before moving prevents most typos — you copy the folder name you can
see instead of spelling it from memory.

**Q21.** **`/c/Users/rohinibarla/Desktop/Bharath/Telangana/Warangal`**.
Absolute = the full chain from `/`, no thinking about where you stand.

**Q22.** **`pwd`**.
Seeing the address change before and after each `cd` is what turns the
abstract `..` into something you can watch happen.

---

# Part C — Scenario Questions

**Q23. Anil's overshoot.**
From `Ravi`, the first `..` lands on `Peddamma` and the second on
`Kutumbam` — so `cd ../../Sita` asks for a `Sita` *directly inside
`Kutumbam`*, and no such folder exists there (`Kutumbam`'s children are
`Peddamma` and `Pinni`). Ravi and Sita are **siblings**, not cousins, so one
step up is enough: `cd ../Sita`. Counting the `..` is exactly counting the
steps up to the common ancestor — here, one.

**Q24. Works for Divya, fails for Suresh.**
`Kutumbam/Peddamma/Ravi` is a **relative** path, so it means "starting from
wherever I am now." Most likely Divya is standing in `Desktop` (the parent of
`Kutumbam`) and Suresh is standing somewhere else — perhaps home, or inside
the tree already. Each should run `pwd` first to see where they stand. Two
fixes for Suresh: (1) move to the right starting point first —
`cd ~/Desktop` — then reuse the relative path; or (2) use the absolute path
`cd /c/Users/<his-username>/Desktop/Kutumbam/Peddamma/Ravi`, which works from
anywhere.

**Q25. Bhavana's not-so-absolute instruction.**
An absolute path is independent of *where you stand*, but not of *which
machine and user* you are — and hers has her own username baked in:
`/c/Users/bhavana/...` doesn't exist on a teammate's laptop, where the home
folder is `/c/Users/<their-name>`. The fix is to route through each person's
own home with `~`: "run `cd ~/Desktop/Kutumbam`". `~` expands to the right
home folder on every machine, so the instruction becomes portable.

**Q26. Warangal → Tirupati by the recipe.**
Step 1: the lowest common ancestor of `Warangal` (under `Telangana`) and
`Tirupati` (under `Andhra`) is `Bharath`. Step 2: from `Warangal` that is two
steps up, so two `..` — the first lands on `Telangana`, the second on
`Bharath`. Step 3: walk down the other branch: `Andhra`, then `Tirupati`.
Final command: `cd ../../Andhra/Tirupati`.

**Q27. Three dots-pairs from Kiran.**
Start: `.../Desktop/Kutumbam/Pinni/Kiran`. First `..` → `Pinni`; second `..`
→ `Kutumbam`; third `..` → `Desktop`. You are now in
`/c/Users/rohinibarla/Desktop`. Each `..` removes exactly one folder from the
end of the `pwd` — which is why running `pwd` after the move confirms it
instantly.

**Q28. Meghana's lowercase habit.**
On the Ubuntu server, `cd kutumbam` will fail with `No such file or
directory`, because Linux treats `kutumbam` and `Kutumbam` as two entirely
different names — case-sensitivity is the rule, Windows' forgiveness is the
exception. Habits formed during practice are the ones that fire under
pressure, so the worksheet insists on exact capitalisation from day one:
practising the strict form costs nothing on Windows, but practising the
sloppy form breaks on every real server.

**Q29. Bouncing with `cd -`.**
After command 1 you are in `Bharath/Andhra` (the `~/Desktop/...` path is
absolute-via-home, so it works from `Ravi`). After the first `cd -` you are
back in `Kutumbam/Peddamma/Ravi` — `cd -` returns to the **previous**
folder. After the second `cd -` you are in `Andhra` again. `cd -` remembers
exactly one thing: the folder you were in before the last move — so repeated
`cd -` bounces you between the same two places.

**Q30. Ravi → Geetha, both ways.**
Relative: `cd ../../Pinni/Geetha` — two `..` (first lands on `Peddamma`,
second on `Kutumbam`, the common grandparent), then down `Pinni` → `Geetha`.
Absolute: `cd /c/Users/rohinibarla/Desktop/Kutumbam/Pinni/Geetha`. Starting
from `Sita` instead, the **absolute path is unchanged and still correct** —
that is its whole point. The relative path *happens* to still work from
`Sita` (she is also a child of `Peddamma`, so the same two `..` reach
`Kutumbam`), but that is luck of the tree, not a property of the path: from
`Kiran` or `Geetha` it would break, while the absolute path never would.
