# Why We Should Follow Type Hints in Python — Questions

Every question below shows a small piece of working Python with **no type hints**.

Your job is the same every time:

> **Copy the code into your notebook and write it again — this time with the type
> hints.**

Rules for this question bank:

1. Write out the **whole code**, not just the type. `list[int]` on its own is not an
   answer; `marks: list[int] = [80, 90, 85, 72]` is.
2. Annotate **every** variable that is created in the sample, including empty ones.
3. For a container, always say what is **inside** it — `list[int]`, not `list`.
4. For a function, annotate **each parameter and the return type**.
5. Do not change what the code does. Only add the hints.

---

## Part A — Variables and Collections

**A1.** A student's basic details.

``` python
name = "Ravi"
age = 20
average = 78.5
```

---

**A2.** Marks in one subject, and the totals worked out from them.

``` python
marks = [80, 90, 85, 72]
total = 327
count = 4
```

---

**A3.** The names as they were entered, and the same names with the repeats removed.

``` python
names = ["Ravi", "Sita", "Anil", "Ravi"]
unique_names = {"Ravi", "Sita", "Anil"}
```

---

**A4.** One city record.

``` python
city = {
    "name": "Vizag",
    "state": "Andhra Pradesh",
    "country": "India"
}
```

---

**A5.** How many times each word appeared in a file.

``` python
word_counts = {"the": 12, "and": 7, "python": 3}
most_common = "the"
```

---

**A6.** A screen size and a colour.

``` python
resolution = (1920, 1080)
colour = (255, 128, 0)
```

---

**A7.** Two rows read out of a database table.

``` python
rows = [
    {"name": "Ravi", "city": "Vizag"},
    {"name": "Sita", "city": "Guntur"}
]
```

---

**A8.** The marks for each subject, kept under the subject's name.

``` python
subject_marks = {
    "maths": [80, 90, 85],
    "science": [70, 75, 88]
}
```

---

## Part B — Function Signatures

**B1.** Adding two whole numbers.

``` python
def add(a, b):
    return a + b
```

---

**B2.** Working out the average of a list of marks.

``` python
def average(marks):
    return sum(marks) / len(marks)
```

---

**B3.** Printing a greeting a fixed number of times.

``` python
def greet(name, times):
    for i in range(times):
        print("Hello,", name)
```

---

**B4.** Pulling one field out of every row.

``` python
def city_names(rows):
    names = []
    for row in rows:
        names.append(row["city"])
    return names
```
