# Changing a Page with JavaScript — Question Bank

Answer in your notebook. For the scenarios in Part C, write the final page **line by line**, top to bottom, and mark which words are bold. Work it out on paper first. You may check in Ulaa afterwards.

Unless a question says otherwise, every snippet runs in the **Console** on this page, freshly opened (or reloaded with F5):

```html
<!DOCTYPE html>
<html>
<head>
  <title>Shop Page</title>
</head>
<body>
  <h1>Shop</h1>
  <p>Open 9 to 5</p>
</body>
</html>
```

---

## Part A — Multiple Choice

**A1.** Which of these **removes** everything already inside `<body>`?

- A. `document.body.appendChild(p)`
- B. `document.body.innerHTML += "<p>Hi</p>"`
- C. `document.body.innerHTML = "<p>Hi</p>"`
- D. `p.innerHTML = "<p>Hi</p>"`

**A2.** Where does `document.body.appendChild(p)` put the paragraph `p`?

- A. At the very top of the body
- B. At the end of the body
- C. Inside the `<head>`
- D. In place of the first element of the body

**A3.** After running only this line, what changes on the page?

```javascript
const note = document.createElement("p");
```

- A. An empty paragraph appears at the end
- B. An empty paragraph appears at the top
- C. Nothing on the page changes yet
- D. The body is cleared

**A4.** What does this add to the end of the page?

```javascript
const t = document.createElement("p");
t.textContent = "Price: <strong>50</strong>";
document.body.appendChild(t);
```

- A. Price: **50**
- B. Price: 50, with nothing bold
- C. The line `Price: <strong>50</strong>`, with the tags visible
- D. Nothing, because `textContent` cannot contain `<`

**A5.** Same as A4, but with `t.innerHTML` instead of `t.textContent`. What is added?

- A. Price: **50**
- B. Price: 50, with nothing bold
- C. The line `Price: <strong>50</strong>`, with the tags visible
- D. An error

**A6.** This line is run **three times**. How many elements does the body hold at the end?

```javascript
document.body.innerHTML = "<p>Fresh start</p>";
```

- A. 1
- B. 3
- C. 4
- D. 5

**A7.** This line is run **twice**. How many elements does the body hold at the end?

```javascript
document.body.innerHTML += "<p>New stock!</p>";
```

- A. 1
- B. 2
- C. 3
- D. 4

**A8.** You change the page a lot in the Console, then press **F5**. What do you see?

- A. The page with all your changes
- B. A blank page
- C. The page exactly as written in the file
- D. Only the changes, without the original content

**A9.** After running Console snippets on `shop.html`, what happens to the file `shop.html` on your disk?

- A. The new content is written into it
- B. It becomes empty
- C. Nothing. It is unchanged.
- D. It is renamed

**A10.** You open a file that is completely empty (0 bytes) and look at **Elements**. What do you see?

- A. Nothing at all
- B. Only `<body></body>`
- C. `<html>` with an empty `<head>` and an empty `<body>` inside
- D. An error message

**A11.** A file contains only the line `<p>Hello</p>`. In Elements, where is the `<p>`?

- A. Directly inside `<html>`, outside any body
- B. Inside `<head>`
- C. Inside `<body>`
- D. Nowhere. The browser ignores it without a skeleton.

**A12.** After `document.body.innerHTML = "";`, what does the browser tab's title show for `shop.html`?

- A. Shop Page
- B. Shop
- C. Nothing
- D. shop.html

**A13.** In Ulaa, where do you type a JavaScript snippet so it runs on the open page?

- A. The address bar
- B. The Elements tab
- C. The Console tab of DevTools
- D. Inside the HTML file, then press F5

**A14.** In the snippets, which expression refers to the page's `<body>` element?

- A. `document.createElement("body")`
- B. `document.body`
- C. `body.document`
- D. `document.innerHTML`

---

## Part B — Fill in the Blanks

**B1.** `document.__________("p")` makes a new paragraph element that is not yet on the page.

**B2.** `document.body.__________(p)` puts the element `p` at the end of the body.

**B3.** To put a string in as plain text, so that any tags in it are shown as characters, set the element's __________.

**B4.** To have tags like `<strong>` in a string become real elements, set the element's __________.

**B5.** `document.body.innerHTML =` __________ the existing content, while `document.body.innerHTML +=` __________ it.

**B6.** In Ulaa, the key that opens DevTools is __________.

**B7.** The DevTools tab that shows the page as a tree of `<html>`, `<head>`, `<body>` is called __________.

**B8.** Pressing __________ makes the browser read the file again and rebuild the page.

**B9.** The live copy of the page that the browser builds in memory, and that JavaScript changes, is called the __________.

**B10.** Even for an empty file, the browser creates the three elements `<______>`, `<______>` and `<______>`.

---

## Part C — Scenario Questions

**C1. A note and a phone number.** On a fresh `shop.html`, this is run:

```javascript
const note = document.createElement("p");
note.textContent = "Closed on Sunday";
document.body.appendChild(note);
document.body.innerHTML += "<p>Call 98480 12345</p>";
```

Write the final page, line by line.

**C2. Three lines, two signs.** On a fresh `shop.html`, this is run:

```javascript
document.body.innerHTML += "<p>New stock!</p>";
document.body.innerHTML = "<h1>Sale</h1>";
document.body.innerHTML += "<p>50% off</p>";
```

Write the final page, line by line. Which line of code decides that *Shop* and *Open 9 to 5* are no longer on the page?

**C3. A tip for the shopkeeper.** On a fresh `shop.html`, this is run:

```javascript
const tip = document.createElement("p");
tip.textContent = "Use <strong>bold</strong> wisely";
document.body.appendChild(tip);
```

Write the last line of the page **exactly** as it appears on screen. Is any word bold? Explain.

**C4. The same snippet, three times.** Snippet X is pasted and run three times on a fresh `shop.html`:

```javascript
const offer = document.createElement("p");
offer.innerHTML = "<strong>Diwali</strong> offer";
document.body.appendChild(offer);
```

On another fresh `shop.html`, Snippet Y is run three times:

```javascript
document.body.innerHTML = "<p>Fresh start</p>";
```

Write the final page for each. Explain why one grows and the other does not.

**C5. Closed for lunch.** On a fresh `shop.html`, this is run:

```javascript
document.body.innerHTML = "";
const back = document.createElement("p");
back.textContent = "Back soon";
document.body.appendChild(back);
```

(a) Write the final page. (b) What does the browser tab's title show? (c) Inside `<body>` in Elements, how many children are there?

**C6. Gone after a reload?** Ravi runs `document.body.innerHTML = "<h1>Hacked</h1>";` on `shop.html`. The page shows only *Hacked*. He panics, thinking he has damaged his file.

(a) Is his file damaged? (b) What should he press, and what will he see? (c) Explain in one or two sentences why.

**C7. Starting from nothing.** Priya opens a completely empty file, `blank.html`, in Ulaa and runs:

```javascript
const name = document.createElement("p");
name.textContent = "Pavan Lanka - The wanabe solo developer";
document.body.appendChild(name);
```

(a) Before she runs it, what does Elements show? (b) After she runs it, what is inside `<body>`? (c) Why did `document.body` work even though her file has no `<body>` tag?

**C8. The lost shop.** Kiran wanted to add a bold "**Offer** today" line to the **end** of `shop.html`, keeping everything. He ran:

```javascript
document.body.innerHTML = "<p><strong>Offer</strong> today</p>";
```

(a) What does the page show now? (b) What went wrong? (c) Write **two** different correct snippets that do what Kiran wanted, using two different ways from the guide.
