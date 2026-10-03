# Changing a Page with JavaScript — Appending vs Replacing the Body

**Goal.** A web page on your screen is not just a file. When the browser opens an HTML file, it builds a live copy of the page in memory, called the **DOM**, and JavaScript can change that copy while you watch. Today you will learn four ways to put new content into the page's `<body>`. Three of them **add** to what is already there and one **wipes it out first**. By the end you should be able to look at any of the four and say, before running it, what the page will look like afterwards.

**You need:** the **Ulaa** browser, a text editor (Notepad or VS Code), a notebook, and a pencil. No server and no terminal today. You open the HTML files directly in Ulaa.

Start with the big picture just below: where the DOM came from, and what the browser does with your HTML. Then read the guide, and then do the iterations, which make you run every snippet in the guide and look closely at what it does.

---

## The big picture — from HTML file to DOM tree

### Where the word "DOM" comes from

**1989 — documents for scientists.** At **CERN**, the physics laboratory in Geneva, thousands of researchers from many countries had to share papers, notes and results. These were spread over different computers that could not easily read each other's files. **Tim Berners-Lee**, who worked there, proposed a system of **linked documents**: any page could point to any other page, on any computer. By the end of 1990 he had written the first web server, the first browser, and a simple language for writing these documents: **HTML**, the *HyperText Markup Language*. "Hypertext" means text with links, and "markup" means tags like `<h1>` and `<p>` that mark which part of the text is a heading and which is a paragraph.

So from the very start, a web page was a **document**: headings, paragraphs and links, much like a research paper.

**1995 — pages that can change.** Netscape, the most popular browser of the time, added a small programming language that runs **inside the page**: **JavaScript**. Microsoft's Internet Explorer soon did the same. For a script to change a page, the page has to exist as something a program can reach: not as text, but as **objects** with properties (like `textContent`) and actions (like `appendChild`). Each browser invented its own way of doing this, so a script written for one browser often broke in the other.

**1998 — one agreed model.** To end that mess, the **W3C** (the group that writes web standards) published the **Document Object Model, Level 1**: one agreed description of how a page is offered to programs. The name says exactly what it is:

| Word | Meaning |
|---|---|
| **Document** | the page, which since 1989 has been a document |
| **Object** | every part of it (the body, each heading, each paragraph) is an object a program can use |
| **Model** | an agreed description of how those objects fit together, as a **tree** |

**How HTML grew.** The first HTML (1991) had only about 18 tags, enough for headings, paragraphs, lists and links. The first official standard, HTML 2.0 (1995), included images and forms. Later versions added tables (HTML 3.2, 1997), and the separation of *structure* (HTML) from *style* (CSS) (HTML 4, 1997–1999). **HTML5** (2014) added tags for video, audio and drawing, and was designed for whole **applications** running in the browser, not just documents. Today HTML and the DOM are kept as **"living standards"** by the **WHATWG**, a group of browser makers, and are updated continuously instead of in numbered versions. Gmail, YouTube and every page you open in Ulaa are all still built on the same idea: a document, made of objects, arranged as a tree.

### What Ulaa does with your file

When you open an HTML file, Ulaa does not show the file directly. It goes through these steps:

```
  body_demo.html                      (text on your disk)
        │
        │  1. read the file, character by character
        ▼
  ┌──────────────┐
  │    PARSER    │   finds the tags: <html> … <body> … <h1> … </h1> …
  └──────────────┘
        │
        │  2. build a tree of objects in memory
        ▼
  ┌──────────────┐
  │   DOM TREE   │◄──────────────────────┐
  └──────────────┘                       │
        │                                │  4. JavaScript changes
        │  3. draw the tree on screen    │     the tree
        ▼                                │     (the Console, or a <script>)
  ┌──────────────┐                ┌──────────────┐
  │  THE SCREEN  │                │  JavaScript  │
  └──────────────┘                └──────────────┘
        ▲                                │
        └──── 5. the tree changed, so the screen is drawn again
```

- **Parsing** (1) happens **once**, when the page is opened, or again when you press **F5**.
- After that, the **file is not used again**. What you see on screen is drawn from the **DOM tree** (3).
- JavaScript never edits the file. It edits the **tree** (4), and the browser redraws the screen from the changed tree (5).

This is the single most important picture of today. Every snippet in the guide is a way of changing the tree.

### Example — parsing a small page into a tree

Here is a short HTML file:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Colour Lab</title>
</head>
<body>
  <h1>Light</h1>
  <p>Screens <strong>add</strong> light.</p>
  <p>Paint takes it away.</p>
</body>
</html>
```

The parser reads it from top to bottom. Every time a tag **opens inside** another tag, the new element becomes that tag's **child**. The text between tags becomes a **text node**. The result is this tree:

```
html
├── head
│   └── title
│       └── "Colour Lab"
└── body
    ├── h1
    │   └── "Light"
    ├── p
    │   ├── "Screens "
    │   ├── strong
    │   │   └── "add"
    │   └── " light."
    └── p
        └── "Paint takes it away."
```

Read the tree with family words:

- `html` is the **root**, the top of the tree. It has two **children**: `head` and `body`.
- `body` is the **parent** of `h1`, `p` and `p`. Those three are **siblings**.
- The first `p` has three children: the text `"Screens "`, the element `strong`, and the text `" light."`. The bold word is a **child of the paragraph**, because in the file `<strong>` opened inside `<p>`.
- Only what is inside `body` is drawn on the page. `head` holds information *about* the page, such as the title shown on the browser tab.

(The real tree also holds tiny text nodes made of just the spaces and new lines between tags. The Elements tab hides them, and so do we.)

Now open the **Elements** tab on any page and you will recognise this tree, drawn the same way: each `▸` arrow opens a node to show its children, indented one step to the right.

This also explains the difference between two words in the guide. `innerHTML` **runs the parser** on your string, so `"<strong>balanced.</strong>"` becomes a real `strong` node in the tree. `textContent` **does not parse**. It makes one plain text node, so the `<` and `>` stay as characters.

### Why order matters — and what `appendChild` does

The children of a node are kept **in order**: first child, second child, third child. The browser draws the body's children **in that order, from top to bottom**. So *where* a node sits in the list decides *where* it appears on the screen.

`appendChild` means: **add this node as the last child**. Here is the tree of `body_demo.html` before and after Snippet 1:

```
  BEFORE                              AFTER  document.body.appendChild(name)

  body                                body
   ├── h1  "My Page"                   ├── h1  "My Page"
   └── p   "This paragraph was…"       ├── p   "This paragraph was…"
                                       └── p   "Pavan Lanka - …"   ◄── new last child
```

The new paragraph becomes the **last child** of `body`, so it is drawn **last**, at the bottom of the page. Nothing already in the tree is moved or removed.

Compare `document.body.innerHTML = "…"`. That **cuts off every child** of `body`, then parses the new string and hangs the resulting nodes on `body` instead. The branches are new, while `body` itself stays.

> **Hold this picture in your head:** HTML file → **parse** → **DOM tree** → screen. JavaScript changes the **tree**, and the screen follows. The tree keeps children **in order**, and `appendChild` always adds at the **end**.

---

## The guide — JavaScript DOM: Replacing and Appending Body Content

### 1. Create a Paragraph and Append to `<body>`

```javascript
const name = document.createElement("p");
name.textContent = "Pavan Lanka - The wanabe solo developer";

document.body.appendChild(name);
```

The existing content remains, and the new paragraph is added to the end of `<body>`.

### 2. Create a Paragraph with HTML and Append to `<body>`

```javascript
const description = document.createElement("p");

description.innerHTML =
  "Inner Engineering makes you <strong>balanced.</strong>";

document.body.appendChild(description);
```

The existing content remains. The word **balanced.** appears in bold.

### 3. Replace the Entire Contents of `<body>`

```javascript
document.body.innerHTML = `
  <h1>Welcome</h1>
  <p>Inner Engineering makes you <strong>balanced.</strong></p>
  <p>IE will enable you to do your best.</p>
`;
```

All existing content inside `<body>` is removed and replaced with the HTML above.

### 4. Append HTML to the Existing Contents of `<body>`

```javascript
document.body.innerHTML += `
  <p>Inner Engineering makes you <strong>balanced.</strong></p>
  <p>IE will enable you to do your best.</p>
`;
```

The existing content remains, and both paragraphs are added to the end of `<body>`.

### Quick Comparison

| Operation                                         | Existing Content |
| ------------------------------------------------- | ---------------- |
| `document.createElement()` + `appendChild()`      | ✅ Kept           |
| `createElement()` + `innerHTML` + `appendChild()` | ✅ Kept           |
| `document.body.innerHTML =`                       | ❌ Replaced       |
| `document.body.innerHTML +=`                      | ✅ Kept           |

In the iterations below the four snippets are called **Snippet 1**, **Snippet 2**, **Snippet 3** and **Snippet 4**, numbered as in the guide.

---

## Setting up — the page and the Console

**1. Make the page.** Create a folder called `task43` somewhere easy to find, such as your Desktop. Open your editor, type this, and save it in that folder as `body_demo.html`:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Body Demo</title>
</head>
<body>
  <h1>My Page</h1>
  <p>This paragraph was here first.</p>
</body>
</html>
```

If you use Notepad, set **Save as type** to **All files** before saving. Otherwise Notepad quietly saves the file as `body_demo.html.txt`.

**2. Open it in Ulaa.** Drag `body_demo.html` into an Ulaa window. You can also right-click it and choose **Open with → Ulaa**. The address bar starts with `file:///`, which means you are looking at a file from your own disk with no server involved.

**3. Open the Console.** Press **F12**, or right-click on the page and choose **Inspect**. A panel opens with tabs along its top. You will use two of them:

- **Elements** shows the page's DOM as a tree: `<html>`, `<head>`, `<body>` and everything inside them.
- **Console** is where you type or paste JavaScript. Pressing **Enter** runs it on the page.

**4. The first paste.** The first time you paste into the Console, Ulaa may block it with a warning that asks you to type `allow pasting`. Type exactly `allow pasting` and press **Enter**, then paste again. You only need to do this once.

**Two small things about the Console:**
- After a snippet runs, the Console often prints something back, such as `<p>…</p>` or the text you assigned. Ignore it. Look at the **page**.
- To type more than one line by hand, press **Shift + Enter** between lines. A plain **Enter** runs what you have typed so far. Pasted snippets run whole when you press Enter.

**Starting fresh.** Pressing **F5** reloads the page, so it goes back to exactly how it looked when you first opened it. Iteration 7 explains why. Every iteration starts with **F5**.

> **The golden rule of today**
> The browser reads your file **once** and builds the DOM, a live copy of the page. JavaScript changes only that copy. **Appending** (`appendChild`, `innerHTML +=`) adds to the **end** of `<body>` and keeps everything already there. **Replacing** (`innerHTML =`) throws away everything in `<body>` and puts the new content in its place.

---

## Iteration 1 — Append once, then append again

**a. What we set up**

`body_demo.html` is open in Ulaa, freshly reloaded with **F5**, with the Console open. The page shows one heading and one paragraph.

**b. Task**

Before running anything, answer in your notebook:
1. After Snippet 1 runs, how many paragraphs will the page have? Will the new one be at the top or at the bottom?
2. If you run Snippet 1 **a second time** without reloading, what happens?

Now paste Snippet 1 into the Console and press Enter. Look at the page. Then press the **Up arrow** in the Console to bring the snippet back, and press Enter again.

**c. Observation (what you should find)**

After the first run, the page has the original heading and paragraph, and **below them** a new paragraph: *Pavan Lanka - The wanabe solo developer*.

After the second run the same line appears **twice** at the bottom. Each run makes a **brand-new** `<p>` with `createElement` and adds it to the end. The browser never checks whether the same text is already on the page.

Open the **Elements** tab and expand `<body>`. You will see the four elements in order: `h1`, `p`, `p`, `p`.

**Takeaway to say out loud:** *"`createElement` makes a new element, and `appendChild` puts it at the end of the body. Run it twice and I get two."*

---

## Iteration 2 — Bold, or the tags themselves?

**a. What we set up**

Press **F5** so the page starts fresh. Have Snippet 2 ready, plus this variant. Its text is the same, but it uses `textContent` instead of `innerHTML`:

```javascript
const plain = document.createElement("p");
plain.textContent = "Inner Engineering makes you <strong>balanced.</strong>";
document.body.appendChild(plain);
```

**b. Task**

Run Snippet 2 and look at the new line. Before running the variant, predict how it will look. Will *balanced.* be bold? Will you see the `<strong>` tags? Write your guess, then run it.

**c. Observation (what you should find)**

Snippet 2 adds: Inner Engineering makes you **balanced.** The word is bold and no tags are visible.

The variant adds this line, exactly as written:

```
Inner Engineering makes you <strong>balanced.</strong>
```

You see the tags as plain characters, and nothing is bold.

- `innerHTML` **reads the string as HTML**, so `<strong>` becomes a real bold element.
- `textContent` **puts the string in as plain text**, so every `<` and `>` is shown just as you typed it.

**Takeaway to say out loud:** *"`innerHTML` turns tags into real elements. `textContent` shows the tags as text."*

---

## Iteration 3 — Replace the whole body

**a. What we set up**

Press **F5**. The page shows *My Page* and *This paragraph was here first.*

**b. Task**

Look at Snippet 3. It uses `=`, not `+=`. Predict what happens to *My Page* and the first paragraph. Then run it.

**c. Observation (what you should find)**

*My Page* and *This paragraph was here first.* are **gone**. The page now shows only:

- **Welcome** (a heading)
- Inner Engineering makes you **balanced.**
- IE will enable you to do your best.

Expand `<body>` in **Elements** and you will find exactly three children: `h1`, `p`, `p`. Nothing from the original page is left. Look at the **browser tab**, though: the title still says *Body Demo*. The title lives in `<head>`, and `document.body.innerHTML` reaches only inside `<body>`.

**Takeaway to say out loud:** *"`innerHTML =` empties the body and puts in the new HTML. The head is not touched."*

---

## Iteration 4 — Append HTML with `+=`

**a. What we set up**

Press **F5**.

**b. Task**

Snippet 4 also uses `innerHTML`, but with `+=`. Predict which content survives and where the new paragraphs go. Then run it.

**c. Observation (what you should find)**

Everything survives. The page reads, top to bottom:

- **My Page**
- This paragraph was here first.
- Inner Engineering makes you **balanced.**
- IE will enable you to do your best.

`+=` means *take what is already in the body and add this to the end of it*. It works like `count += 1` in Python: the old value plus the new piece.

**Takeaway to say out loud:** *"`innerHTML +=` keeps the old body and adds the new HTML after it."*

---

## Iteration 5 — Twice in a row

**a. What we set up**

Press **F5**.

**b. Task**

Part 1: run Snippet 3 twice in a row, using the Up arrow and Enter for the second run. Count the elements on the page.

Part 2: press **F5**, then run Snippet 4 twice in a row. Count again.

Before you run either part, write your predicted count for each.

**c. Observation (what you should find)**

| What you ran | Elements on the page afterwards |
|---|---|
| Snippet 3, twice | 3: Welcome, balanced, do your best |
| Snippet 4, twice | 6: My Page, first paragraph, balanced, do your best, balanced, do your best |

Snippet 3 gives the **same page** however many times you run it. Each run wipes the body and writes the same three elements again. Snippet 4 makes the page **longer every time**, because each run keeps everything and adds two more paragraphs.

**Takeaway to say out loud:** *"Run `=` again and nothing changes. Run `+=` again and the page grows."*

---

## Iteration 6 — Order matters

**a. What we set up**

Press **F5**.

**b. Task**

Part 1: run Snippet 1, then Snippet 3.
Part 2: press **F5**, then run Snippet 3, then Snippet 1.

Before each part, write down where Pavan's paragraph will be at the end, or whether it will be there at all.

**c. Observation (what you should find)**

| Order | Final page |
|---|---|
| 1 then 3 | Welcome, balanced, do your best. **Pavan is gone.** |
| 3 then 1 | Welcome, balanced, do your best, **Pavan Lanka…** |

In Part 1, Pavan's paragraph *was* added, but then Snippet 3 replaced the whole body, and it went with everything else. In Part 2, the replacing happened first, and Snippet 1 then appended to the **new** body.

**Takeaway to say out loud:** *"A replace wipes out everything added before it. Anything appended after a replace stays."*

---

## Iteration 7 — Did the file change?

**a. What we set up**

Press **F5**, then run Snippets 1, 3 and 4 in any order you like, so the page looks quite different from your file.

**b. Task**

1. Open `body_demo.html` in your **editor** (not in Ulaa). Has any of the new text been written into it?
2. Go back to Ulaa and press **F5**. What does the page show?

**c. Observation (what you should find)**

The file in your editor is **exactly as you saved it**: *My Page* and one paragraph. No Welcome, no Pavan.

After **F5**, Ulaa shows the original page again, and all your changes are gone.

When Ulaa opened the file, it read it once and built the **DOM** in memory. Your snippets changed that **DOM**, which is what you see on the screen. They never touched the file. **F5** throws the DOM away, reads the file again, and builds a fresh DOM.

**Takeaway to say out loud:** *"JavaScript in the Console changes the DOM, not my file. F5 rebuilds the page from the file."*

---

## Iteration 8 — Empty the body

**a. What we set up**

Press **F5**. Here is a one-line snippet:

```javascript
document.body.innerHTML = "";
```

**b. Task**

This is Snippet 3's `=` with an **empty** string. Predict what you will see, then run it. Afterwards, check two places: the **Elements** tab and the **browser tab's title**.

**c. Observation (what you should find)**

The page goes **completely blank**.

In **Elements**, `<body>` is still there, but it is empty: `<body></body>`. `<head>` is untouched, and the tab title still says *Body Demo*.

Replacing with nothing is still a replace. Everything inside the body is removed and nothing is put back. The `<body>` element itself remains, ready for you to append to again.

**Takeaway to say out loud:** *"`innerHTML = ""` clears the body. The body itself and the head stay."*

---

## Iteration 9 — A completely empty file

**a. What we set up**

In the same `task43` folder, create a new file called `empty.html` and save it **with nothing in it**: no tags and no text, a file of 0 bytes.

**b. Task**

Open `empty.html` in Ulaa. The page will be blank. Before you open **Elements**, predict whether the DOM will be empty too, or whether the browser will have put something there. Then press F12 and look at **Elements**.

**c. Observation (what you should find)**

The DOM is **not** empty. Elements shows:

```
<html>
  <head></head>
  <body></body>
</html>
```

Your file contained nothing at all, yet the browser built the full skeleton itself. Every page in a browser has an `<html>`, a `<head>` and a `<body>`. If the file does not supply them, the browser adds them. (The tab shows the file name `empty.html` as its title, because there is no `<title>` to use.)

**Takeaway to say out loud:** *"Even an empty file gets html, head and body. The browser always builds the skeleton."*

---

## Iteration 10 — Content only, no skeleton

**a. What we set up**

Since the browser builds the skeleton anyway, you do not have to write it every time you practise. Create a second file, `content_only.html`, containing **only** these two lines, with no `<!DOCTYPE>`, `<html>`, `<head>` or `<body>`:

```html
<h1>My Notes</h1>
<p>Today I learnt the DOM.</p>
```

**b. Task**

Open `content_only.html` in Ulaa. Does the heading show as a big heading and the paragraph as normal text? Now open **Elements**. Where did your `<h1>` and `<p>` end up?

**c. Observation (what you should find)**

The page looks just as if you had written a full HTML file: a big **My Notes** and a paragraph under it.

In Elements:

```
<html>
  <head></head>
  <body>
    <h1>My Notes</h1>
    <p>Today I learnt the DOM.</p>
  </body>
</html>
```

The browser wrapped your content in the skeleton and put the heading and paragraph **inside `<body>`**, where visible content belongs.

So for quick practice files, writing just your headings and paragraphs is enough. (For real pages you will still write the full structure, because the `<head>` is where things like the page title go.)

**Takeaway to say out loud:** *"I can write only the content. The browser puts it inside the body for me."*

---

## Iteration 11 — Build a page from nothing

**a. What we set up**

Open `empty.html` again (blank page, empty `<body>`), with the Console open. You will build the same page as `content_only.html`, and a little more, using only the ways from the guide.

**b. Task**

Run these three pieces one after another. Before each one, say which way from the guide it is and whether it keeps what is already on the page.

```javascript
const heading = document.createElement("h1");
heading.textContent = "My Notes";
document.body.appendChild(heading);
```

```javascript
const intro = document.createElement("p");
intro.innerHTML = "Today I learnt the <strong>DOM</strong>.";
document.body.appendChild(intro);
```

```javascript
document.body.innerHTML += `
  <p>createElement + appendChild keeps the page.</p>
  <p>innerHTML = replaces it.</p>
`;
```

Then look at **Elements**, and compare the `<body>` you built with the `<body>` of `content_only.html`.

**c. Observation (what you should find)**

The page now reads:

- **My Notes** (a heading)
- Today I learnt the **DOM**.
- createElement + appendChild keeps the page.
- innerHTML = replaces it.

`createElement` works for any tag, not only `"p"`: `"h1"` gave you a heading. The first piece is Way 1, the second is Way 2 (which makes *DOM* bold), and the third is Way 4. All three **append**, so each piece stayed when the next was added.

In Elements, `<body>` holds `h1`, `p`, `p`, `p`, built entirely by JavaScript. `content_only.html` got its `h1` and `p` from the file. Either way they end up as elements inside `<body>`.

**Takeaway to say out loud:** *"The body can be filled by the file or by JavaScript. Either way it is the same DOM."*

---

## Practice

For each task, press **F5** on `body_demo.html` first (or use `empty.html` where it says so). Write your snippet in the notebook **before** typing it, then run it and check.

**P1. Add your name.** Add a paragraph with your full name to the end of the page, using `createElement` and `textContent`.
*Self-check:* *My Page* and the first paragraph are still there, and your name is the last line.

**P2. Bold college.** Add one paragraph that says *I study at* followed by your college name in **bold**, keeping everything already on the page.
*Self-check:* only the college name is bold, and no `<strong>` characters are visible.

**P3. A page about you.** Make the page show **only** a heading with your name and one paragraph about your hometown. Nothing from *My Page* should remain.
*Self-check:* Elements shows exactly one `h1` and one `p` inside `<body>`.

**P4. Three at once.** With **one** paste, add three paragraphs to the end of the page: your favourite food, colour and place.
*Self-check:* the page has 2 + 3 = 5 elements in the body, and the original two are on top.

**P5. Predict, then run.** On a fresh `body_demo.html`, someone runs Snippet 4, then Snippet 1, then Snippet 3, then Snippet 2. Write the final page, line by line, in your notebook. Then run the four snippets in that order and compare.
*Self-check:* count the lines on your paper against the page. Does Pavan appear?

**P6. Fix the bug.** A friend wanted to add a **bold** "Exam on Monday" line to the end of the page and wrote:

```javascript
const notice = document.createElement("p");
notice.textContent = "<strong>Exam on Monday</strong>";
document.body.appendChild(notice);
```

Run it and describe what went wrong. Then fix it by changing **one word**.
*Self-check:* the fixed line shows **Exam on Monday** in bold, with no tags visible.

**P7. From empty to notes.** Open `empty.html` and, using only Console snippets, make its page look exactly like `content_only.html`.
*Self-check:* the `<body>` in Elements matches `content_only.html`'s: one `h1`, then one `p`.

---

## One-page reference

**Where things are in Ulaa**

| What | How |
|---|---|
| Open a file | Drag it into Ulaa, or right-click → Open with → Ulaa |
| Open DevTools | **F12**, or right-click → Inspect |
| See the DOM tree | **Elements** tab |
| Run JavaScript | **Console** tab: paste, then Enter |
| First paste is blocked | type `allow pasting`, Enter |
| New line without running | **Shift + Enter** |
| Re-run the last snippet | **Up arrow**, then Enter |
| Rebuild the page from the file | **F5** |

**The four ways**

| Way | Code shape | Existing body | Where new content goes | Tags in the string |
|---|---|---|---|---|
| 1 | `createElement` + `textContent` + `appendChild` | kept | end | shown as text |
| 2 | `createElement` + `innerHTML` + `appendChild` | kept | end | become elements |
| 3 | `document.body.innerHTML = …` | **removed** | becomes the whole body | become elements |
| 4 | `document.body.innerHTML += …` | kept | end | become elements |

**Things to remember**

| Fact | Example |
|---|---|
| Running an append again adds again | Snippet 1 twice gives two Pavan lines |
| Running a replace again gives the same page | Snippet 3 twice still gives three elements |
| A replace wipes out earlier appends | Snippet 1 then 3: Pavan is gone |
| `innerHTML = ""` clears the body | `<body></body>`, head untouched |
| The Console changes the DOM, not the file | F5 brings back the original page |
| The browser always builds html/head/body | an empty file gives `<html><head></head><body></body></html>` |
| A file can contain just content | `<h1>…</h1><p>…</p>` ends up inside `<body>` |
| `createElement` takes any tag name | `"p"`, `"h1"` |

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| DOM | డామ్ | బ్రౌజర్ మెమరీలో తయారుచేసే పేజీ యొక్క ప్రత్యక్ష కాపీ — JavaScript మార్చేది దీన్నే |
| Document Object Model | డాక్యుమెంట్ ఆబ్జెక్ట్ మోడల్ | పేజీని (డాక్యుమెంట్) ఆబ్జెక్ట్‌ల చెట్టుగా చూపించే ఒప్పందం — DOM పూర్తి పేరు |
| HTML (HyperText Markup Language) | హైపర్‌టెక్స్ట్ మార్కప్ లాంగ్వేజ్ | లింకులతో కూడిన పత్రాలను ట్యాగ్‌లతో రాసే భాష |
| hypertext | హైపర్‌టెక్స్ట్ | లింకులు ఉన్న టెక్స్ట్ — ఒక పేజీ నుండి ఇంకో పేజీకి |
| markup | మార్కప్ | టెక్స్ట్‌లో ఏది హెడ్డింగ్, ఏది పేరా అని గుర్తు పెట్టే ట్యాగ్‌లు |
| parse / parser | విశ్లేషించడం / విశ్లేషకం | HTML టెక్స్ట్‌ను చదివి ట్యాగ్‌లను గుర్తించి చెట్టుగా మార్చడం |
| tree | చెట్టు (వృక్ష నిర్మాణం) | పై నుండి కొమ్మలుగా విడిపోయే డేటా నిర్మాణం |
| node | నోడ్ | చెట్టులో ఒక స్థానం — ఎలిమెంట్ లేదా టెక్స్ట్ |
| text node | టెక్స్ట్ నోడ్ | ట్యాగ్‌ల మధ్య ఉన్న అక్షరాలు మాత్రమే ఉన్న నోడ్ |
| root | మూలం | చెట్టు పైభాగం — `html` |
| parent | తల్లిదండ్రి నోడ్ | ఒక నోడ్‌ను తన లోపల పెట్టుకున్న నోడ్ |
| child | పిల్ల నోడ్ | ఇంకో నోడ్ లోపల ఉన్న నోడ్ — క్రమంలో ఉంటాయి |
| sibling | తోబుట్టువు | ఒకే తల్లిదండ్రి కింద ఉన్న నోడ్‌లు |
| render | చిత్రించడం | DOM చెట్టు నుండి స్క్రీన్‌పై పేజీని గీయడం |
| standard | ప్రమాణం | అందరూ ఒకేలా పాటించే ఒప్పందం — W3C, WHATWG రాస్తాయి |
| element | ఎలిమెంట్ | పేజీలో ఒక భాగం — `<p>`, `<h1>` లాంటివి |
| tag | ట్యాగ్ | `<` `>` మధ్య రాసే పేరు — `<strong>` |
| body | బాడీ | పేజీలో కనిపించే భాగమంతా ఉండే ఎలిమెంట్ |
| head | హెడ్ | కనిపించని సమాచారం ఉండే భాగం — టైటిల్ ఇక్కడే |
| skeleton | అస్థిపంజరం / ఆకారం | `html`, `head`, `body` — ప్రతి పేజీకి ఉండే ప్రాథమిక నిర్మాణం |
| append | చివర కలపడం | ఉన్నదాన్ని ఉంచి, కొత్తదాన్ని చివరలో జోడించడం |
| replace | మార్చివేయడం | ఉన్నదంతా తీసేసి, దాని స్థానంలో కొత్తది పెట్టడం |
| existing content | ఉన్న విషయం | మనం మార్చకముందే పేజీలో ఉన్నది |
| create | తయారుచేయడం | కొత్త ఎలిమెంట్‌ను తయారుచేయడం — `createElement` |
| plain text | సాధారణ అక్షరాలు | ట్యాగ్‌లు కూడా అక్షరాలుగానే కనిపిస్తాయి — `textContent` |
| bold | బోల్డ్ / మందపాటి అక్షరాలు | `<strong>` తో గట్టిగా కనిపించే అక్షరాలు |
| Console | కన్సోల్ | JavaScript రాసి వెంటనే నడిపే చోటు |
| Elements tab | ఎలిమెంట్స్ ట్యాబ్ | పేజీ యొక్క DOM చెట్టును చూపించే చోటు |
| DevTools | డెవ్‌టూల్స్ | F12 తో తెరుచుకునే డెవలపర్ పనిముట్లు |
| reload | మళ్ళీ లోడ్ చేయడం | F5 — ఫైల్‌ను మళ్ళీ చదివి పేజీని కొత్తగా కట్టడం |
| snippet | చిన్న కోడ్ ముక్క | ఒకేసారి నడిపే కొన్ని లైన్ల కోడ్ |
| empty | ఖాళీ | ఏమీ లేనిది — `""` |
