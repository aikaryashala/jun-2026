# Check, Style & Submit — Every Program

**Goal.** In Task 12 you set up the CS50 tools and used them on the **sum** problem. Now you will do the same for every program below: check that it is correct, fix its style, and submit it. The commands never change — only the problem name and the file name do.

**You need:** your Linux VM (WSL) with the CS50 tools installed (Task 12), and your finished C programs in `~/c-programs`.

> **The one pattern**
> Every problem uses the same four tools with the same slug shape. For a problem called `<name>`:
> - the C file you wrote is **`<name>.c`**
> - the slug is **`aikaryashala/algorithms/june-2026/<name>/c`**

---

## The template (memorize this shape)

From inside `~/c-programs`, for any problem `<name>`:

```
check50 --local aikaryashala/algorithms/june-2026/<name>/c
style50 <name>.c
style50 -i <name>.c
style50 <name>.c
submit50 aikaryashala/algorithms/june-2026/<name>/c
```

Check correctness first, fix the style, look again, then submit. Never submit before `check50 --local` and `style50` are both clean.

---

## Worked example — `product`

**1. Go to your C programs directory**

```
cd ~/c-programs
```

**2. Check correctness locally**

```
check50 --local aikaryashala/algorithms/june-2026/product/c
```

Read every check. A green `:)` means that check passed; a red `:(` tells you what went wrong — fix `product.c` and run this again until all checks are green.

**3. Check the code style**

```
style50 product.c
```

`style50` shows how many changes it would make. `0` changes means your style is already clean; any other number means read on to step 4.

**4. Automatically fix the style**

```
style50 -i product.c
```

The `-i` edits `product.c` in place, applying the style fixes for you.

**5. Check the style again**

```
style50 product.c
```

Confirm it now reports no changes — your file follows the style guide.

**6. Submit**

```
submit50 aikaryashala/algorithms/june-2026/product/c
```

Once correctness and style are both clean, submit for the final evaluation.

---

## Now do the rest yourself

For each problem below, run the **same five commands** as `product` — just swap in the name. The expected C file and the slug are given so you can't slip.

| # | Problem `<name>` | C file | slug for `check50 --local` / `submit50` |
|---|---|---|---|
| 1 | `natural_numbers_up_to_n` | `natural_numbers_up_to_n.c` | `aikaryashala/algorithms/june-2026/natural_numbers_up_to_n/c` |
| 2 | `even_numbers_up_to_n` | `even_numbers_up_to_n.c` | `aikaryashala/algorithms/june-2026/even_numbers_up_to_n/c` |
| 3 | `odd_numbers_up_to_n` | `odd_numbers_up_to_n.c` | `aikaryashala/algorithms/june-2026/odd_numbers_up_to_n/c` |
| 4 | `n_even_numbers` | `n_even_numbers.c` | `aikaryashala/algorithms/june-2026/n_even_numbers/c` |
| 5 | `n_even_numbers_v2` | `n_even_numbers_v2.c` | `aikaryashala/algorithms/june-2026/n_even_numbers_v2/c` |
| 6 | `n_odd_numbers` | `n_odd_numbers.c` | `aikaryashala/algorithms/june-2026/n_odd_numbers/c` |
| 7 | `n_odd_numbers_v2` | `n_odd_numbers_v2.c` | `aikaryashala/algorithms/june-2026/n_odd_numbers_v2/c` |

For example, the first one is exactly:

```
check50 --local aikaryashala/algorithms/june-2026/natural_numbers_up_to_n/c
style50 natural_numbers_up_to_n.c
style50 -i natural_numbers_up_to_n.c
style50 natural_numbers_up_to_n.c
submit50 aikaryashala/algorithms/june-2026/natural_numbers_up_to_n/c
```

Tick off each problem only when both `check50 --local` is all green and `style50` reports no changes, and the submission went through.

---

## One-page reference

| Command | What it does |
|---|---|
| `cd ~/c-programs` | move into the folder holding your C programs |
| `check50 --local <slug>` | run the correctness checks on your machine (green `:)` = pass) |
| `style50 <name>.c` | report how many style changes are needed (`0` = clean) |
| `style50 -i <name>.c` | fix the style automatically, editing the file in place |
| `submit50 <slug>` | send your program to CS50's server for the final evaluation |

**Rules to keep:** always `check50 --local` and `style50` **before** `submit50` · the file is `<name>.c` · the slug is `aikaryashala/algorithms/june-2026/<name>/c` · a submission counts only after correctness and style are both clean.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| check50 | చెక్50 | ప్రోగ్రామ్ సరిగ్గా పనిచేస్తోందో పరీక్షించే సాధనం |
| style50 | స్టైల్50 | కోడ్ శైలి (అమరిక) సరిచూసే/సరిచేసే సాధనం |
| submit50 | సబ్మిట్50 | ప్రోగ్రామ్‌ను మూల్యాంకనానికి పంపే సాధనం |
| slug | స్లగ్ | సమస్యను గుర్తించే పొడవైన పేరు (దారి) |
| local check | స్థానిక పరీక్ష | పంపే ముందు మీ మెషిన్‌లోనే చేసే పరీక్ష (`--local`) |
| in place (`-i`) | అక్కడికక్కడే | ఫైల్‌ను అదే చోట మార్చడం |
