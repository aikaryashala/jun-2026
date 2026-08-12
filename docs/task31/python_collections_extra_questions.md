# Collections in Python — Extra Question Bank: Real Contexts

Finish the main question bank first — this one continues from it, so the parts here keep their letters: **E to J**.

The main bank asked *what does this container do?* This one asks *where will you actually meet it?* Every question below is a shape you will hit in real backend or AI work, with the names and fields kept as close to the real thing as fits on a page.

One idea holds the whole bank together. Almost all real data is one of a small number of **shapes**:

| Shape | Means | Backend | AI |
|---|---|---|---|
| **list of dicts** | many records | rows from a database | a conversation |
| **dict** | one record | one user | one API response |
| **dict of lists** | grouping | orders per customer | chunks per document |
| **dict of ints** | counting | views per page | tokens per model |
| **dict of dicts** | indexed by key | users by id | tools by name |
| **set** | uniqueness / membership | a user's permissions | allowed labels |
| **tuple** | a fixed composite | a composite key | a `(model, version)` pair |

A student who can walk a **list of dicts** can read a database result, an HTTP response, a chat history and an evaluation set — because underneath they are the same thing.

Where a question says "write the code", write it in your notebook first, then type and run it. You may use `if` / `elif` / `else`, and everything from the worksheet.

Answers are **not** in this file.

---

# Part E — Backend: rows from a database query

Every question in this part uses this result. A query returns rows, and one row is one dictionary:

```python
users = [
    {"id": 1, "name": "Ravi",   "city": "Warangal",  "status": "active"},
    {"id": 2, "name": "Asha",   "city": "Hyderabad", "status": "inactive"},
    {"id": 3, "name": "Balaji", "city": "Warangal",  "status": "active"},
    {"id": 4, "name": "Sita",   "city": "Nizamabad", "status": "active"},
]
```

**E1.** What container is `users`? What container is `users[0]`? What does `len(users)` give, and what does `len(users[0])` give?

**E2.** Predict the output:
```python
print(len(users))
print(users[0]["city"])
print(users[3]["name"])
```

**E3.** Write the loop that prints every user's name, one per line.

**E4.** Write the loop that prints the name of every **active** user only.

**E5.** Write the code that counts how many users are active, and prints just the number.

**E6.** Write the code that collects every **distinct** city into a container, then prints how many there are. Which container did you choose, and why is it the right one?

**E7.** Write the code that builds a dictionary letting you look a user up **by id**, so that afterwards `by_id[3]["name"]` gives `Balaji`. What shape have you built?

**E8.** Write the code that produces a count of users per city, so the result is `{'Warangal': 2, 'Hyderabad': 1, 'Nizamabad': 1}`. What shape is the result?

**E9.** Write the code that groups the **names** by city, so the result is
`{'Warangal': ['Ravi', 'Balaji'], 'Hyderabad': ['Asha'], 'Nizamabad': ['Sita']}`.
What shape is this, and how does it differ from E8?

**E10.** The query is changed to return no rows at all, so `users = []`. For each of E3, E5 and E8, say what happens — output, or an error?

---

# Part F — Backend: an order, a cart, and permissions

```python
order = {
    "order_id": "A-1001",
    "customer": "Ravi",
    "items": [
        {"sku": "PEN",  "qty": 3, "price_paise": 1000},
        {"sku": "BOOK", "qty": 1, "price_paise": 25000},
        {"sku": "BAG",  "qty": 2, "price_paise": 45000},
    ],
}
```

*(Money is held in **paise** as whole numbers — a common real practice, and it keeps everything an int.)*

**F1.** Predict the output:
```python
print(order["customer"])
print(len(order["items"]))
print(order["items"][1]["sku"])
print(order["items"][2]["qty"])
```

**F2.** Describe the shape of `order` in words: what container is it, what is `order["items"]`, and what is each element of that?

**F3.** Write the code that prints the order total in paise. (Each line costs `qty × price_paise`.)

**F4.** Write the loop that prints, for each item, its sku, its quantity, and that line's total.

**F5.** Write the loop that prints the sku of every item ordered more than once.

**F6.** A second order arrives for the same customer. Sketch the container you would use to hold **all** the orders for a shop, and say what one element of it is.

Now permissions:

```python
roles = {
    "ravi":   {"admin", "editor"},
    "asha":   {"viewer"},
    "balaji": {"editor", "viewer"},
}
```

**F7.** What shape is `roles`? What container is `roles["ravi"]`, and why is that the right choice for a set of permissions — give two reasons.

**F8.** Predict the output:
```python
print("admin" in roles["ravi"])
print("admin" in roles["asha"])
print(len(roles["balaji"]))
```

**F9.** Write the loop that prints the name of every person who is an editor.

**F10.** Write the code that collects every distinct role used anywhere in the system, and prints them in a fixed, repeatable order. Why can you not simply print the container you collected them in?

**F11.** Someone proposes storing each person's roles as a **list** instead of a set. Give two concrete things that would go wrong.

---

# Part G — AI-native: the conversation

This is the single most common shape in AI work — a conversation is a **list of dicts**, and the order *is* the conversation:

```python
conversation = [
    {"role": "user",      "content": "What is a branch?"},
    {"role": "assistant", "content": "A file holding one hash."},
    {"role": "user",      "content": "Show me."},
    {"role": "assistant", "content": "Here is an example."},
    {"role": "user",      "content": "Thanks."},
]
```

**G1.** Predict the output:
```python
print(len(conversation))
print(conversation[0]["role"])
print(conversation[-1]["content"])
```

**G2.** Why must this be a **list** and not a set? Give the reason in terms of the container's properties.

**G3.** Write the loop that prints every turn as `role : content`.

**G4.** Write the code that counts how many turns came from the user.

**G5.** Write the code that produces a count of turns per role — `{'user': 3, 'assistant': 2}`. What shape is that?

**G6.** Write the code that finds the **last** message sent by the user and prints its content. (No new syntax is needed — think about what the loop variable holds after the loop.)

**G7.** Write the line that adds a new assistant turn saying `You are welcome.` to the end of the conversation. Then say what `len(conversation)` becomes.

**G8.** A richer message has **content blocks** instead of plain text:
```python
message = {
    "role": "assistant",
    "content": [
        {"type": "text",     "text": "Let me check."},
        {"type": "tool_use", "name": "get_weather", "input": {"city": "Warangal"}},
    ],
}
```
Predict the output:
```python
print(len(message["content"]))
print(message["content"][0]["type"])
print(message["content"][1]["name"])
print(message["content"][1]["input"]["city"])
```

**G9.** For `message` above, describe every level of the shape, from the outside in.

**G10.** Write the loop that prints the `type` of each content block in `message`.

---

# Part H — AI-native: tools, tokens and evaluations

**H1.** An API reply reports what it used:
```python
usage = {"input_tokens": 812, "output_tokens": 96}
```
Predict the output:
```python
print(usage["input_tokens"])
print(usage["input_tokens"] + usage["output_tokens"])
print(len(usage))
```

**H2.** A program makes three calls and records each one:
```python
calls = [
    {"input_tokens": 100, "output_tokens": 20},
    {"input_tokens": 250, "output_tokens": 60},
    {"input_tokens": 80,  "output_tokens": 15},
]
```
Write the code that prints the total input tokens, the total output tokens, and the grand total.

**H3.** Tools given to a model are a list of dicts:
```python
tools = [
    {"name": "get_weather", "required": ["city"]},
    {"name": "send_email",  "required": ["to", "subject"]},
]
```
Predict the output:
```python
print(len(tools))
print(tools[1]["required"][1])
```
Then write the loop that prints each tool's name and how many required arguments it has.

**H4.** Write the code that turns `tools` into a dictionary keyed by tool name, so you can check `"get_weather" in by_name` instantly. What shape have you built, and why is it better than scanning the list every time a tool call arrives?

**H5.** An evaluation run produces one dict per test case:
```python
results = [
    {"case": "c1", "score": 90, "label": "pass"},
    {"case": "c2", "score": 40, "label": "fail"},
    {"case": "c3", "score": 75, "label": "pass"},
    {"case": "c4", "score": 55, "label": "fail"},
]
```
Write the code that prints how many cases passed, out of how many.

**H6.** For the same `results`, write the code that prints the total of all scores, the average (whole number — use `//`), and the case name with the highest score.

**H7.** For the same `results`, write the code that sorts every case into `high` (70 and above), `medium` (50 to 69) and `low` (below 50), and prints a count of each. What shape is your result?

**H8.** A retrieval step returns chunks, and the same passage has come back twice:
```python
chunks = [
    {"doc": "d1", "text": "Git stores objects", "score": 92},
    {"doc": "d2", "text": "A branch is a file", "score": 88},
    {"doc": "d1", "text": "Git stores objects", "score": 92},
]
```
Write the code that prints how many chunks were returned and how many **distinct documents** they came from. Which container does the second half need?

**H9.** A model must only ever answer with one of a fixed set of labels:
```python
allowed = {"pass", "fail"}
outputs = ["pass", "maybe", "fail", "pass"]
```
Write the loop that prints each output followed by `ok` or `REJECTED`, and afterwards prints how many were rejected. Why is `allowed` a set rather than a list?

**H10.** Name the shape used by each of these, using the table at the top of this bank:
(a) a conversation (b) `usage` (c) `by_name` from H4 (d) `allowed` from H9 (e) the buckets from H7 (f) `results` from H5

---

# Part I — Choosing the container

For each, name the container (or arrangement of containers) you would use, and give the **one property** that decides it. There is often more than one defensible answer — the reason matters more than the name.

**I1.** The rows returned by `SELECT id, name, city FROM users`.

**I2.** The set of user ids that have already been sent today's notification email, checked before each send.

**I3.** The turns of a chat conversation being sent to a model.

**I4.** The HTTP headers of one incoming request.

**I5.** How many times each error type occurred in a log file.

**I6.** All orders belonging to each customer.

**I7.** The `(latitude_degrees, longitude_degrees)` of a delivery address, used as a key in a lookup of delivery zones.

**I8.** The list of stop sequences to pass to a model.

**I9.** The set of tags on a blog post, where the same tag must never appear twice and the display order does not matter.

**I10.** A cache mapping a prompt to the response it produced last time.

**I11.** The five retrieved passages for a RAG query, ranked most relevant first.

**I12.** Every distinct label the model produced across 1,000 test cases, to check none was invented.

---

# Part J — Spot the bug

Each snippet is realistic code with one mistake. For each, say **what happens** (the exact error name, or the wrong output), **why**, and **how to fix it**. Watch out — not all of them crash, and those are the important ones.

**J1.**
```python
users = [{"id": 1, "name": "Ravi"}, {"id": 2, "name": "Asha"}]
print(users["name"])
```

**J2.**
```python
tally = {}
for u in users:
    tally[u["city"]] = tally[u["city"]] + 1
print(tally)
```

**J3.** Intended to group names by city.
```python
by_city = {}
for u in users:
    by_city[u["city"]] = [u["name"]]
print(by_city)
```
*(Using the four users from Part E. The result is `{'Warangal': ['Balaji'], 'Hyderabad': ['Asha'], 'Nizamabad': ['Sita']}` — Ravi has vanished.)*

**J4.** Intended to delete every inactive user.
```python
users = [
    {"name": "Ravi",   "status": "active"},
    {"name": "Asha",   "status": "inactive"},
    {"name": "Gita",   "status": "inactive"},
    {"name": "Balaji", "status": "active"},
]
for u in users:
    if u["status"] == "inactive":
        users.remove(u)
```
*(Afterwards, `users` still contains Gita.)*

**J5.**
```python
tally = {"Warangal": 2}
for c in tally:
    tally["Hyderabad"] = 1
```

**J6.** Intended to collect each distinct `(city, status)` combination.
```python
seen = set()
for u in users:
    seen.add([u["city"], u["status"]])
```

**J7.** Names and scores kept in two lists, matched by position.
```python
names  = ["Ravi", "Asha", "Balaji"]
scores = [90, 40, 75]
names.append("Sita")
for i in range(len(names)):
    print(names[i], scores[i])
```
(Also: what single change to the *data structure* would make this class of bug impossible?)

**J8.**
```python
seen = ("user",)
for m in conversation:
    seen.append(m["role"])
```

**J9.** Intended to check whether a user record holds the name Ravi.
```python
user = {"id": 1, "name": "Ravi"}
if "Ravi" in user:
    print("found")
else:
    print("not found")
```

**J10.**
```python
for i in range(len(users) + 1):
    print(users[i]["name"])
```

**J11.** Intended to print each key and value of an API response.
```python
resp = {"model": "claude", "stop": "end_turn"}
for k, v in resp:
    print(k, v)
```

**J12.** Compare J4 with J5. In one, Python raised an error; in the other it did not. Which behaviour is more dangerous for a program that runs in production, and why?
