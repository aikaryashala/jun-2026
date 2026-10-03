# JavaScript Colors — Question Bank

Answer in your notebook. Work on paper first, and show your hex arithmetic. You may check in Ulaa's Console afterwards.

---

## Part A — Multiple Choice

**A1.** These three lines are pasted together and run. What colour is the page?

```javascript
document.body.style.backgroundColor = "#00FF00";
document.body.style.backgroundColor = "#FFFFFF";
document.body.style.backgroundColor = "#0000FF";
```

- A. Green
- B. White
- C. Blue
- D. A mix of all three

**A2.** What colour is `#00FFFF`?

- A. Brown
- B. Cyan (sky blue)
- C. Yellow
- D. Black

**A3.** In `#FF8040`, the green part `80` is which decimal number?

- A. 80
- B. 64
- C. 128
- D. 255

**A4.** Which 6-digit colour is the same as `#F80`?

- A. `#F80000`
- B. `#000F80`
- C. `#FF8800`
- D. `#F08000`

**A5.** The page is red. Then `document.body.style.backgroundColor = "#FF804";` runs. What is the page now?

- A. Red
- B. Orange
- C. White
- D. Black

**A6.** What is the largest value `Math.floor(Math.random() * 256)` can give?

- A. 1
- B. 254
- C. 255
- D. 256

**A7.** What is `((0x12 << 16) | (0x34 << 8) | 0x56).toString(16)`?

- A. `'563412'`
- B. `'123456'`
- C. `'12'`
- D. `'1234560'`

**A8.** You paste the Complete Program, which ends with `window.addEventListener("load", start);`, into the Console of an open page. Why does the colour never change?

- A. `setInterval` does not work in the Console
- B. The page had already finished loading, so the `load` event will not come again
- C. `randomColor` returns a number, not a colour
- D. The Console blocks `document.body`

---

## Part B — Fill in the Blanks

**B1.** Two hex digits are ______ bits, so one colour channel ranges from 0 to ______.

**B2.** When red, green and blue are all equal, such as `#808080`, the colour is ______.

**B3.** `g << 8` moves the green byte left by ______ hex digits.

**B4.** `n.toString(______)` writes the number `n` in hex.

**B5.** `(255).toString(16).padStart(6, "0")` gives `'______'`.

**B6.** In C, `printf("#%______", 0x00ABCD);` prints `#00abcd`. (Fill in the format so it pads with zeros to 6 hex digits.)

**B7.** `setInterval(changeColor, 2000)` calls `changeColor` once every ______ seconds.

---

## Part C — Scenario Questions

**C1. Packing by hand.** r = `0x0A`, g = `0xB0`, b = `0x0C`.
(a) Write `r << 16`, `g << 8` and `b` in hex, and OR them together.
(b) What does `toString(16)` give for the result?
(c) What happens to the page if the code sets `"#" + n.toString(16)` without `padStart`? What happens with `padStart(6, "0")`?

**C2. Sita's dark green.** Sita wants a **dark** green. She thinks "0F is a small number", so she types `"#0F0"`. Then she tries `"#0F00"`.
(a) What does the page show for each, and why?
(b) Write a 6-digit colour that really is a dark green.

**C3. Ravi's paint box.** Ravi says: "`#FFFF00` is red plus green, so the page should be brown, like mixing paints." What does the page actually show? Explain why he is wrong, and predict the colour of `#FF00FF`.

**C4. Brightness.**
(a) Put these in order from darkest to lightest: `#E0E0E0`, `#202020`, `#808080`.
(b) Which is brighter, `#800000` or `#FF0000`? Are they different colours or different brightnesses?

**C5. The program that never starts.** Kiran pastes the guide's Complete Program into the Console and waits. Nothing changes. Give **two** different ways to make the colour change every second.
