# JavaScript Colors — From Hex Colors to 24-bit RGB

**Goal.** Every colour on your screen is made of three numbers: how much **red**, **green** and **blue** light to shine. Today you will set colours by hand, learn to read `#RRGGBB`, generate random colours, pack the three numbers into one 24-bit number with the bitwise operators you met in Task 10, and finally make the page change colour every second.

**You need:** the **Ulaa** browser, a text editor, a notebook, and a pencil. As in Task 43, you open files directly in Ulaa and use the **Console** (F12). There is no server.

Read the guide first, then do the iterations.

---

## The guide — JavaScript Colors: From Hex Colors to 24-bit RGB

### 1. Manually Set the Background Color

We can change the background color of the page using `style.backgroundColor`.

```javascript
document.body.style.backgroundColor = "#FF0000";
```

Try different colors:

```javascript
document.body.style.backgroundColor = "#00FF00";
document.body.style.backgroundColor = "#0000FF";
document.body.style.backgroundColor = "#FFFF00";
document.body.style.backgroundColor = "#FFFFFF";
document.body.style.backgroundColor = "#000000";
```

Only the **last statement** will be visible because each statement changes the same property.

### 2. Experiment with `#RRGGBB`

A color in hexadecimal format has three parts:

```text
#RRGGBB
```

Each part represents 8 bits:

```text
#RR GG BB
 │  │  │
 │  │  └── Blue
 │  └───── Green
 └──────── Red
```

For example:

```javascript
document.body.style.backgroundColor = "#FF8040";
```

Here:

```text
RR = FF
GG = 80
BB = 40
```

Try changing individual components:

```javascript
document.body.style.backgroundColor = "#FF0000";  // Red
document.body.style.backgroundColor = "#00FF00";  // Green
document.body.style.backgroundColor = "#0000FF";  // Blue
```

Now experiment:

```javascript
document.body.style.backgroundColor = "#FF8000";
document.body.style.backgroundColor = "#8000FF";
document.body.style.backgroundColor = "#008080";
document.body.style.backgroundColor = "#404040";
```

### 3. Generate a Random Color

Instead of manually choosing `R`, `G`, and `B`, generate each value randomly.

```javascript
function randomColor()
{
  const r = Math.floor(Math.random() * 256);
  const g = Math.floor(Math.random() * 256);
  const b = Math.floor(Math.random() * 256);

  return `rgb(${r}, ${g}, ${b})`;
}
```

We can use it like this:

```javascript
document.body.style.backgroundColor = randomColor();
```

### 4. Represent RGB as a 24-bit Number

Each color component uses 8 bits:

```text
RRRRRRRR GGGGGGGG BBBBBBBB
   8 bits    8 bits    8 bits

         24 bits
```

We can combine the three values into one number using bitwise operators.

```javascript
function randomColor()
{
  const r = Math.floor(Math.random() * 256);
  const g = Math.floor(Math.random() * 256);
  const b = Math.floor(Math.random() * 256);

  return (r << 16) | (g << 8) | b;
}
```

For example:

```text
r = FF
g = 80
b = 40

RGB = FF 80 40
```

The operations:

```javascript
(r << 16)
(g << 8)
b
```

put each component into its correct 8-bit position.

### 5. Convert the 24-bit Number to a CSS Color

CSS can use the hexadecimal `#RRGGBB` format.

```javascript
function changeColor()
{
  const color = randomColor();

  document.body.style.backgroundColor =
    "#" + color.toString(16).padStart(6, "0");
}
```

Finally, change the color every second:

```javascript
function start()
{
  setInterval(changeColor, 1000);
}

window.addEventListener("load", start);
```

#### Complete Program

```javascript
function randomColor()
{
  const r = Math.floor(Math.random() * 256);
  const g = Math.floor(Math.random() * 256);
  const b = Math.floor(Math.random() * 256);

  return (r << 16) | (g << 8) | b;
}

function changeColor()
{
  const color = randomColor();

  document.body.style.backgroundColor =
    "#" + color.toString(16).padStart(6, "0");
}

function start()
{
  setInterval(changeColor, 1000);
}

window.addEventListener("load", start);
```

#### The progression

```text
Manual color
    ↓
"#FF8040"
    ↓
Understand R, G, B
    ↓
Generate random R, G, B
    ↓
Pack R, G, B into 24 bits
    ↓
(r << 16) | (g << 8) | b
    ↓
Convert number to hexadecimal
    ↓
"#RRGGBB"
    ↓
Change every second
```

---

## Setting up

Create a folder `task44`. As you learnt in Task 43, a practice file does not need the full skeleton, so save this one line as `colors_lab.html`:

```html
<h1>Colour Lab</h1>
```

Open it in Ulaa, press **F12**, and click the **Console** tab. (If Ulaa blocks your first paste, type `allow pasting` and press Enter.)

> **The golden rule of today**
> A colour is **three bytes**: red, green and blue, each from **0 to 255**. The screen *adds light*, so more means brighter. `#RRGGBB` writes the three bytes in hex. `rgb(r, g, b)` writes them in decimal. `(r << 16) | (g << 8) | b` packs them into **one 24-bit number**. These are all the same three bytes, written three ways.

---

## Iteration 1 — Set colours by hand

**a. What we set up**

`colors_lab.html` is open, with the Console ready.

**b. Task**

Type these **one at a time**, pressing Enter after each. After each one, write in your notebook the colour you see.

```javascript
document.body.style.backgroundColor = "#FF0000";
document.body.style.backgroundColor = "#00FF00";
document.body.style.backgroundColor = "#0000FF";
document.body.style.backgroundColor = "#FFFF00";
document.body.style.backgroundColor = "#FFFFFF";
document.body.style.backgroundColor = "#000000";
```

**c. Observation (what you should find)**

| Code | Colour |
|---|---|
| `#FF0000` | red |
| `#00FF00` | green (bright) |
| `#0000FF` | blue |
| `#FFFF00` | **yellow** |
| `#FFFFFF` | white |
| `#000000` | black |

The page's whole background changes each time, and the `<h1>` stays. `document.body.style.backgroundColor` is a **property** of the body, and each line puts a new value into it.

**Takeaway to say out loud:** *"`document.body.style.backgroundColor = "..."` paints the page. A new value replaces the old one."*

---

## Iteration 2 — Last one wins

**a. What we set up**

The same six lines from Iteration 1.

**b. Task**

This time, **paste all six together** and press Enter once. Predict first: will the page flash through six colours, or show just one?

**c. Observation (what you should find)**

You see only **black**, the last line. All six lines ran, but they ran so fast that you could not see the in-between colours, and each line overwrote the same property. Only the last value remains.

**Takeaway to say out loud:** *"Six assignments to the same property leave only the last value."*

---

## Iteration 3 — Mixing light, not paint

**a. What we set up**

Look back at your table from Iteration 1. In school painting, red and green mixed together give a muddy brown.

**b. Task**

`#FFFF00` has red **full** and green **full**, with blue off. Why is it yellow and not brown? Now predict, then run:

```javascript
document.body.style.backgroundColor = "#FF00FF";
document.body.style.backgroundColor = "#00FFFF";
```

**c. Observation (what you should find)**

| Code | Lights on | Colour |
|---|---|---|
| `#FFFF00` | red + green | yellow |
| `#FF00FF` | red + blue | magenta (pink-purple) |
| `#00FFFF` | green + blue | cyan (sky blue) |
| `#FFFFFF` | all three | white |
| `#000000` | none | black |

A screen does not mix paint. Each dot on it has three tiny **lights**, red, green and blue. Light **adds**: turning more lights on makes the colour **brighter**, so all three full gives **white**, and all off gives **black**.

**Takeaway to say out loud:** *"The screen adds light. Red plus green is yellow, all three is white, none is black."*

---

## Iteration 4 — Two hex digits = one byte

**a. What we set up**

Each pair in `#RRGGBB` is two hex digits. Recall from Task 10 that hex digits run 0–9, then A–F (A=10 … F=15), and that two hex digits make one byte, **00 to FF**, which is **0 to 255**.

**b. Task**

Convert by hand: `FF`, `80`, `40`, `00` into decimal. (Use first digit × 16 + second digit.) Then run:

```javascript
document.body.style.backgroundColor = "#FF8040";
document.body.style.backgroundColor;
```

The second line has no `=`, so it just **reads** the value back. Then try the same colour in lowercase, `"#ff8040"`, and read it back again.

**c. Observation (what you should find)**

| Hex | Working | Decimal |
|---|---|---|
| `FF` | 15×16 + 15 | 255 |
| `80` | 8×16 + 0 | 128 |
| `40` | 4×16 + 0 | 64 |
| `00` | 0 | 0 |

The page turns orange, and the Console answers:

```
'rgb(255, 128, 64)'
```

The browser stored your hex colour as the **same three numbers in decimal**. Lowercase gives exactly the same answer, because hex letters can be upper or lower case.

**Takeaway to say out loud:** *"`#FF8040` and `rgb(255, 128, 64)` are the same colour: red 255, green 128, blue 64."*

---

## Iteration 5 — Brightness and grey

**a. What we set up**

A channel's number says **how bright** that light is. 255 is full, 0 is off.

**b. Task**

Run each line, and write down what changes:

```javascript
document.body.style.backgroundColor = "#FF0000";
document.body.style.backgroundColor = "#800000";
document.body.style.backgroundColor = "#400000";
```

Then run these, where all three channels are **equal**:

```javascript
document.body.style.backgroundColor = "#C0C0C0";
document.body.style.backgroundColor = "#808080";
document.body.style.backgroundColor = "#404040";
```

Finally, predict the colour of each of the guide's experiments **before** running them: `#FF8000`, `#8000FF`, `#008080`.

**c. Observation (what you should find)**

- `FF` → `80` → `40` in red alone gives a red that gets **darker** each time: a lower number means a dimmer light, not a different colour.
- When R = G = B, the result is **grey**. `C0` (192) is light grey, `80` (128) is middle grey, and `40` (64) is dark grey. Equal at 255 is white, and equal at 0 is black.
- `#FF8000` is full red with half green, which gives **orange**. `#8000FF` is half red with full blue, which gives **purple**. `#008080` is half green with half blue, which gives a dark **teal**.

**Takeaway to say out loud:** *"A bigger number is a brighter light. Equal R, G and B gives a grey."*

---

## Iteration 6 — Fewer than six digits

**a. What we set up**

Every colour so far had exactly **6** hex digits. What if you make a mistake and type fewer? To see clearly whether a line did anything, each test starts from a **blue** page.

**b. Task**

For each wrong colour below, first run the blue line, then the wrong one, then read the value back:

```javascript
document.body.style.backgroundColor = "#0000FF";
document.body.style.backgroundColor = "#FF";
document.body.style.backgroundColor;
```

Do this for each of: `"#FF"` (2 digits), `"#F80"` (3), `"#FF8"` (3), `"#FF80"` (4), `"#FF804"` (5). Before each one, write down what you **expect**: perhaps it fills the missing digits with 0 on the right, or on the left, or something else. Then record what you **see** and what the Console reads back.

**c. Observation (what you should find)**

| You typed | Digits | Page | Read back |
|---|---|---|---|
| `#FF` | 2 | still **blue** | `rgb(0, 0, 255)` |
| `#F80` | 3 | **orange** | `rgb(255, 136, 0)` |
| `#FF8` | 3 | **pale yellow** | `rgb(255, 255, 136)` |
| `#FF80` | 4 | **white**, with no colour at all | `rgba(255, 255, 136, 0)` |
| `#FF804` | 5 | still **blue** | `rgb(0, 0, 255)` |

Nothing is filled with zeros. CSS accepts only certain lengths, and it reads each one differently:

- **3 digits (`#RGB`)** is a short form. Each digit is **doubled**: `#F80` means `#FF8800`, so R = FF (255), G = 88 (136), B = 00. It is *not* `#F80000` or `#000F80`. So `#FF8` means `#FFFF88`, a pale yellow, and not the red you might have expected from "FF".
- **4 digits (`#RGBA`)** is the short form plus a 4th digit for **transparency** (how see-through the colour is). In `#FF80` that 4th digit is `0`, which means *completely see-through*, so you see the white behind the page and none of the colour. Read back as `rgba(…, 0)`, the last number is that transparency.
- **2 or 5 digits** are not valid colours. The browser **silently ignores** the line, with no error message, and the old colour (blue) stays. That is why we started from blue: on a white page you could not have told that anything went wrong.

So a colour with missing digits is either **ignored** or **read in a different way** than you meant, and it is never what you intended. Always write all **6** digits.

**Takeaway to say out loud:** *"Three digits are doubled, the fourth digit means see-through, and other short lengths are ignored. A colour needs all six digits."*

---

## Iteration 7 — Random R, G, B

**a. What we set up**

Paste the guide's first `randomColor` function (Section 3, the one that returns `rgb(...)`) into the Console.

**b. Task**

1. Type `randomColor()` and press Enter. Do it **five** times and copy the results.
2. Then run `document.body.style.backgroundColor = randomColor();` a few times.
3. Think: `Math.random()` gives a number from 0 up to, but **not** including, 1. What is the **largest** value `Math.floor(Math.random() * 256)` can give? What if the code said `* 255`?

**c. Observation (what you should find)**

Each call prints a different string, such as `'rgb(37, 201, 118)'` (yours will differ), and the page takes a new colour each time.

The largest `Math.random()` gives is just under 1, such as 0.9999. Then 0.9999 × 256 = 255.97…, and `Math.floor` drops the fraction, giving **255**. The smallest result is 0. So each channel covers exactly **0 to 255**, one byte. With `* 255` the largest would be 254, and full brightness could never come up.

**Takeaway to say out loud:** *"`Math.floor(Math.random() * 256)` gives a whole number from 0 to 255, exactly one byte."*

---

## Iteration 8 — Pack three bytes into 24 bits

**a. What we set up**

In JavaScript, as in C, a number can be written in hex with `0x`, so `0xFF` is 255.

**b. Task**

Work on paper first, in hex, for r = `0xFF`, g = `0x80`, b = `0x40`:

```
r << 16  = 0x______
g << 8   = 0x______
b        = 0x______
OR them  = 0x______
```

Then check in the Console:

```javascript
(0xFF << 16) | (0x80 << 8) | 0x40;
((0xFF << 16) | (0x80 << 8) | 0x40).toString(16);
```

Finally, see what the number inside `toString( )` does. Predict each answer, then run:

```javascript
(255).toString(10);
(255).toString(16);
(255).toString(2);
(10).toString(16);
(16).toString(16);
```

**c. Observation (what you should find)**

Shifting left by 8 bits moves a byte over by **two hex digits**:

```
r << 16  = 0xFF0000
g << 8   = 0x008000
b        = 0x000040
           --------
OR       = 0xFF8040
```

Each byte lands in its own slot, and the slots do not overlap, so `|` simply puts them side by side.

The Console prints `16744512` for the first line. That is the same number in **decimal**: 255 × 65536 + 128 × 256 + 64. The second line prints `'ff8040'`, which is our colour.

**What `toString(16)` means.** A number inside the computer is just bits. It has no "decimal" or "hex" of its own. Those are only ways of **writing it down as text**. `toString(base)` turns the number into a **string of digits** in the base you ask for:

| Code | Base | Digits it may use | Result |
|---|---|---|---|
| `(255).toString(10)` | 10 (decimal) | 0–9 | `'255'` |
| `(255).toString(16)` | 16 (hex) | 0–9, a–f | `'ff'` |
| `(255).toString(2)` | 2 (binary) | 0, 1 | `'11111111'` |
| `(10).toString(16)` | 16 | | `'a'` |
| `(16).toString(16)` | 16 | | `'10'` |

So `toString(16)` means *write this number using **hex digits***. Each hex digit stands for 4 bits, so our 24-bit colour becomes exactly the 6 hex digits `ff8040`, which is two for red, two for green and two for blue. The letters come out in **lowercase**, which CSS accepts just as well. The result is a **string** (note the quotes `'...'`), which is why we can stick `"#"` in front of it with `+`.

**Takeaway to say out loud:** *"`<< 16` moves red to the top byte and `<< 8` moves green to the middle. `|` joins all three into one 24-bit number. `toString(16)` writes that number as hex digits."*

---

## Iteration 9 — Why `padStart(6, "0")`?

**a. What we set up**

The colour r = 0, g = 0x0F, b = 0xFF should be a strong blue.

**b. Task**

Run:

```javascript
const c = (0x00 << 16) | (0x0F << 8) | 0xFF;
c.toString(16);
c.toString(16).padStart(6, "0");
```

Then set the background **both** ways and compare:

```javascript
document.body.style.backgroundColor = "#" + c.toString(16);
document.body.style.backgroundColor = "#" + c.toString(16).padStart(6, "0");
```

**c. Observation (what you should find)**

| Line | Result |
|---|---|
| `c.toString(16)` | `'fff'` (only 3 digits) |
| `.padStart(6, "0")` | `'000fff'` |

`toString(16)` does not write leading zeros, just as you write 42 and not 0042. Without padding the colour string is `"#fff"`, which CSS reads as the 3-digit short form from Iteration 6: each digit doubled, so `#ffffff`, **white**. That is completely wrong. With padding it is `"#000fff"`, the intended blue. `padStart(6, "0")` adds zeros on the **left** until there are 6 digits, so that red, green and blue are each in their right place.

**You have seen this before in C.** `printf` can write a number in hex with `%x`, and it has the same problem and the same fix. Python uses the same format codes:

| Language | Code | Prints |
|---|---|---|
| C | `printf("%x", 0x000FFF);` | `fff` |
| C | `printf("%6x", 0x000FFF);` | `   fff` (padded with **spaces**) |
| C | `printf("#%06x", 0x000FFF);` | `#000fff` |
| Python | `"#%06x" % 0x000FFF` | `#000fff` |
| Python | `f"#{0x000FFF:06x}"` | `#000fff` |
| JavaScript | `"#" + (0x000FFF).toString(16).padStart(6, "0")` | `#000fff` |

Look carefully at `%6x` and `%06x`. The `6` sets the **width** to 6 characters, but on its own it fills the gap with **spaces**, and `"#   fff"` is not a colour. It is the `0` in `%06x` that says *fill with zeros*. JavaScript has no `%06x`, so `toString(16)` + `padStart(6, "0")` is its way of saying the same thing: hex, at least 6 wide, zero-filled.

**Takeaway to say out loud:** *"`toString(16)` drops leading zeros. `padStart(6, "0")` puts them back so the string is always RRGGBB."*

---

## Iteration 10 — Every second

**a. What we set up**

Press **F5** to start fresh. Copy the guide's **Complete Program**.

**b. Task**

1. Paste the Complete Program into the Console and press Enter. Wait five seconds. Does the colour change?
2. Now type `start();` and press Enter. What happens? Press **F5** to stop it.
3. Make a new file, `colors_auto.html`, containing only this (no `<html>`, `<head>` or `<body>` needed):

```html
<h1>Colour every second</h1>
<script>
  // paste the Complete Program here
</script>
```

Open it in Ulaa.

**c. Observation (what you should find)**

1. **Nothing happens.** The last line says *run `start` when the page finishes loading*, but the page finished loading long before you pasted. That moment has passed, so `start` is never called.
2. Calling `start()` yourself starts it: the colour changes **every 1000 ms** (one second), because `setInterval` calls `changeColor` again and again. F5 rebuilds the page and stops it.
3. In `colors_auto.html`, the script is part of the file, so it runs **while the page loads**, the `load` event comes **after** it, and the colours start changing by themselves.

**Takeaway to say out loud:** *"`setInterval` repeats a function every so many milliseconds. A `load` listener only works if it is added before the page finishes loading."*

---

## Practice

**P1.** Set the page to a colour with red 0, green 128, blue 255. Write the hex form first.
*Self-check:* read `document.body.style.backgroundColor` back; it should be `rgb(0, 128, 255)`.

**P2.** Unpack `#123456` into r, g and b in decimal, by hand.
*Self-check:* set the colour and read it back.

**P3.** Pack r = 18, g = 52, b = 86 by hand into one hex number, then into decimal.
*Self-check:* `(18 << 16) | (52 << 8) | 86` in the Console.

**P4.** Make `colors_auto.html` change colour **twice** a second.
*Self-check:* count the changes in 5 seconds; you should see about 10.

**P5.** Without running it, write the 6-digit form and the `rgb(...)` of `"#08F"`. Then write a 2-digit "colour" and say what the page will do.
*Self-check:* start from a blue page, set each one, and read it back.

---

## One-page reference

| Idea | Example |
|---|---|
| Set the background | `document.body.style.backgroundColor = "#FF8040";` |
| Read it back | `document.body.style.backgroundColor` → `'rgb(255, 128, 64)'` |
| One channel | 2 hex digits = 1 byte = 0 to 255 |
| Hex to decimal | first digit × 16 + second: `80` = 128 |
| Screen colours add | R+G = yellow, R+B = magenta, G+B = cyan, all = white, none = black |
| Brightness | bigger number = brighter: `#FF0000` > `#800000` > `#400000` |
| Grey | R = G = B: `#404040`, `#808080`, `#C0C0C0` |
| Fewer digits | 3 → each doubled (`#F80` = `#FF8800`), 4 → 4th is transparency, 2 or 5 → ignored |
| Random byte | `Math.floor(Math.random() * 256)` → 0 … 255 |
| Pack | `(r << 16) \| (g << 8) \| b` |
| Number → hex string | `n.toString(16)` writes `n` in hex digits (0–9, a–f): `(255).toString(16)` → `'ff'` |
| Keep 6 digits | `.padStart(6, "0")`: `fff` → `000fff` |
| Same idea in C / Python | `printf("%06x", n)` · `"%06x" % n` · `f"{n:06x}"` (`%6x` pads with spaces, not zeros) |
| Repeat | `setInterval(fn, 1000)`: every 1000 ms |
| After loading | `window.addEventListener("load", fn)` must be in the file, not typed later |

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| background colour | నేపథ్య రంగు | పేజీ వెనుక భాగం రంగు |
| property | లక్షణం | ఒక ఎలిమెంట్‌కి ఉండే విలువ — కొత్త విలువ పాతదాన్ని మార్చేస్తుంది |
| channel | ఛానెల్ | రంగులోని ఒక భాగం — ఎరుపు, ఆకుపచ్చ లేదా నీలం |
| hexadecimal | షోడశాంశ (హెక్స్) | 0–9, A–F అంకెలతో రాసే సంఖ్యా విధానం |
| additive (light) | కలిపే కాంతి | కాంతులు కలిస్తే ఇంకా ప్రకాశవంతం — అన్నీ కలిస్తే తెలుపు |
| brightness | ప్రకాశం | కాంతి ఎంత బలంగా ఉంది — సంఖ్య పెద్దదైతే ఎక్కువ |
| grey | బూడిద రంగు | ఎరుపు, ఆకుపచ్చ, నీలం సమానంగా ఉన్న రంగు |
| random | యాదృచ్ఛికం | ముందే తెలియని, ప్రతిసారీ మారే విలువ |
| shift | జరపడం | బిట్‌లను ఎడమకు/కుడికి కదపడం — `<<` |
| pack | ఒకటిగా కూర్చడం | మూడు బైట్‌లను ఒకే సంఖ్యలో పెట్టడం |
| pad | ముందు నింపడం | అంకెలు తక్కువైతే ఎడమవైపు సున్నాలు చేర్చడం |
| transparency | పారదర్శకత | రంగు ఎంత కనిపించకుండా (వెనుకది కనిపించేలా) ఉంది — 0 అంటే పూర్తిగా కనిపించదు |
| shorthand | సంక్షిప్త రూపం | 3 అంకెల రంగు — ప్రతి అంకె రెండుసార్లు: `#F80` = `#FF8800` |
| interval | విరామం | మళ్ళీ మళ్ళీ జరిగే పని మధ్య సమయం |
| millisecond | మిల్లీసెకను | సెకనులో వెయ్యో వంతు — 1000 ms = 1 s |
| event | సంఘటన | బ్రౌజర్‌లో జరిగేది — పేజీ లోడ్ అవ్వడం లాంటిది |
| listener | వినేది | ఒక సంఘటన జరిగినప్పుడు నడిచే ఫంక్షన్ |
