# Relative vs Absolute Paths — Question Bank

Answer these **after** finishing the Task-3 worksheet (Relative vs Absolute
Paths). Write your answers in your notebook first. For every path answer,
also say **why** — name the folders you climb through, out loud.

Assume the two practice trees from the worksheet exist on the Desktop:

```
Desktop/
├── Kutumbam/
│   ├── Peddamma/
│   │   ├── Ravi/
│   │   └── Sita/
│   └── Pinni/
│       ├── Kiran/
│       └── Geetha/
└── Bharath/
    ├── Andhra/
    │   ├── Vijayawada/
    │   └── Tirupati/
    └── Telangana/
        ├── Hyderabad/
        └── Warangal/
```

Home is `/c/Users/rohinibarla`, so the Desktop is
`/c/Users/rohinibarla/Desktop`. **Predict first** — verify in Git Bash only
after writing your prediction down.

---

# Part A — Multiple Choice Questions

Choose the one best option.

**Q1.** Which of these is an **absolute** path?

- (a) `Kutumbam/Peddamma`
- (b) `../Pinni`
- (c) `/c/Users/rohinibarla/Desktop`
- (d) `.`

**Q2.** What does `..` mean in a path?

- (a) The current folder
- (b) The parent folder — one step up
- (c) The home folder
- (d) The root of the drive

**Q3.** What does the `pwd` command do?

- (a) Changes your password
- (b) Prints the folder you are currently standing in
- (c) Lists the files in the current folder
- (d) Jumps to the previous folder

**Q4.** In Git Bash on Windows, how does the `C:` drive appear?

- (a) `C:`
- (b) `/C:`
- (c) `/c`
- (d) `~`

**Q5.** You are in `Ravi`
(`/c/Users/rohinibarla/Desktop/Kutumbam/Peddamma/Ravi`). Which relative path
takes you to `Sita`?

- (a) `cd Sita`
- (b) `cd ../Sita`
- (c) `cd ../../Sita`
- (d) `cd ./Peddamma/Sita`

**Q6.** Still starting in `Ravi`, the move to `Kiran` is
`cd ../../Pinni/Kiran`. Which folder does the second `..` land you on?

- (a) `Peddamma`
- (b) `Desktop`
- (c) `Pinni`
- (d) `Kutumbam`

**Q7.** Which statement about an **absolute** path is true?

- (a) It is always shorter than the relative path
- (b) It is the same, and works the same, no matter which folder you are standing in
- (c) It only works when you are inside the Desktop
- (d) It must contain at least one `..`

**Q8.** What does `cd -` do?

- (a) Goes up one level
- (b) Goes home
- (c) Jumps back to the **previous** folder you were in
- (d) Deletes the current folder

**Q9.** What does `cd` typed **alone**, with no argument, do?

- (a) Nothing — it prints an error
- (b) Goes up one level
- (c) Stays where you are and prints the path
- (d) Goes to your home folder

**Q10.** In the worksheet's postal analogy, "House 12, Gandhi Street,
Vijayawada, Andhra, Bharath" corresponds to which kind of path — and why?

- (a) Relative, because it names real places
- (b) Absolute, because it is valid no matter where you are standing
- (c) Relative, because you still have to travel there
- (d) Absolute, because it is long

**Q11.** You are in `Vijayawada`. What does `cd ../..` do?

- (a) Lands you on `Bharath`
- (b) Lands you on `Andhra`
- (c) Lands you on `Desktop`
- (d) Error — you cannot use `..` twice

**Q12.** The worksheet warns: "teach the exact capitalisation." Why do the
capital letters in `Kutumbam` matter?

- (a) Git Bash refuses lowercase letters
- (b) Real Linux servers are case-sensitive, even though Windows is forgiving
- (c) Folders must always start with a capital letter
- (d) They don't — capitalisation never matters anywhere

---

# Part B — Fill in the Blanks

Write the exact missing word, symbol, or command.

**Q13.** One-line rule: if a path starts with __________ it is absolute;
otherwise it is relative.

**Q14.** In a path, `.` means __________ and `..` means __________.

**Q15.** In Git Bash, `~` is a shortcut for the folder __________ (write the
full absolute path for user `rohinibarla`).

**Q16.** Folders with the **same parent** are called __________, and moving
between them is always `cd ______/<name>`.

**Q17.** You are in `Geetha`. The relative path to her sibling `Kiran` is:
`cd __________`.

**Q18.** The recipe for any relative path: first find the lowest
__________ __________ of "here" and "there"; the number of steps up to it is
the number of __________ you need.

**Q19.** You are in `Vijayawada` and want `Hyderabad`. The relative path is
`cd __________`.

**Q20.** The command __________ lists what is inside the current folder —
"look around before you leap."

**Q21.** The absolute path of the `Warangal` folder is __________.

**Q22.** The worksheet says to run __________ before and after every `cd`,
so you can watch your address change.

---

# Part C — Scenario Questions

Answer in 2–4 sentences each. Name the folders you pass through.

**Q23.** Anil is in `Ravi` and wants to reach `Sita`. He types
`cd ../../Sita` and gets `No such file or directory`. Trace where his path
actually pointed, explain the mistake, and give the correct command.

**Q24.** Two students both type `cd Kutumbam/Peddamma/Ravi`. For Divya it
works; for Suresh it fails with `No such file or directory` — yet the
`Kutumbam` tree definitely exists on both machines. What is the most likely
difference between them, and what single command should each run first to
find out? Give Suresh two different ways to fix his situation.

**Q25.** Bhavana writes helpful notes for her team that say: "to reach the
practice folder, run `cd /c/Users/bhavana/Desktop/Kutumbam`". Her teammates
report the command fails on their laptops. Why does an absolute path — which
is supposed to "work from anywhere" — fail here? Rewrite the instruction so
it works for every teammate (assume everyone built `Kutumbam` on their own
Desktop).

**Q26.** Use the worksheet's recipe to go from `Warangal` to `Tirupati`.
Name the lowest common ancestor, say how many `..` you need and which folder
each one lands on, and write the final command.

**Q27.** `pwd` shows `/c/Users/rohinibarla/Desktop/Kutumbam/Pinni/Kiran`.
You type `cd ../../..`. Where are you now? Walk through it one `..` at a
time.

**Q28.** On her Windows laptop, Meghana types `cd kutumbam` (all lowercase)
and it works, so she says capitalisation is a waste of attention. Next month
the class moves to a real Ubuntu server. What will happen to her habit
there, and why does the worksheet insist on exact capitalisation from day
one?

**Q29.** You are in `Ravi`. You run `cd ~/Desktop/Bharath/Andhra`, and then
`cd -`, and then `cd -` again. Where do you end up after each of the three
commands? What is `cd -` actually remembering?

**Q30.** The worksheet's challenge: from `Ravi`, reach `Geetha` with a
relative path. Write the command, state how many `..` you needed and which
folder they land you on — and then write the absolute-path version. Which of
the two would still be correct if you started from `Sita` instead, and why?
