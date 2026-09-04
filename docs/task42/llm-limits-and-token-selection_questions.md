# What an LLM Actually Does — Questions

Answer on paper, using the reading page's ideas: a language model is a **pure
function** from a sequence of tokens to a **probability distribution over the next
token**; it has **no memory, no live data, and no ability to act**, and **no
guarantee of correctness**; the pipeline is **deterministic throughout** — logits,
scaled by **temperature** *before* softmax, normalised by **softmax**, then
optionally truncated by **top-k / top-p** *after* it — and only the final
**weighted random draw** (or argmax) is random; and verification lives **before
the draw** (constrained decoding) or **after the fact** (code, model review,
human).

Answers are **not** in this file.

Parts C4–C6 and C9 need arithmetic. Keep `e = 2.71828` and these powers handy —
you may round each probability to the nearest whole per cent:

```
e^0.25 = 1.28    e^1.0 = 2.72    e^2.0 = 7.39
e^0.5  = 1.65    e^4.0 = 54.60
```

---

## Part A — Multiple Choice

**A1.** In one sentence, an LLM takes a sequence of tokens and produces:

- A) the next word of the answer
- B) a probability distribution over the next token
- C) a decision about which tool to call
- D) a shorter version of its input

**A2.** Chat, reasoning, summarisation, code generation and tool use are best
described as:

- A) five separate capabilities built into the model
- B) the same single operation, repeated, with different text in front of it
- C) five different models chosen by a router
- D) four language tasks plus one that is not

**A3.** What makes a run of messages *feel* like a conversation?

- A) a session object inside the model that stores earlier turns
- B) the model remembering the last few messages until the process restarts
- C) the client resending the full transcript on every turn
- D) a summary the model writes to itself after each reply

**A4.** "Weights are frozen at training time" means that, for the model, a fact
published after its training cutoff:

- A) is fetched from a store when needed
- B) does not exist
- C) is present but marked as low-confidence
- D) is available only if the user pastes it in — otherwise it is guessed

**A5.** The reading page says the model "cannot tell you what it doesn't know."
Why not?

- A) it is trained not to admit uncertainty
- B) not knowing and confidently generating produce the same internal state
- C) the uncertainty signal exists but is stripped out before the text is returned
- D) it can, but only at temperature 0

**A6.** When a model "calls a tool," what has actually happened?

- A) the model executed a function and read the result
- B) the model emitted a structured string that your code may choose to execute
- C) the API ran the function on the model's behalf and hid it from you
- D) the model opened a network connection

**A7.** "The model proposes; your application disposes" is a reason to treat model
output as:

- A) a trusted command
- B) an untrusted request
- C) a database transaction
- D) a log entry

**A8.** Memory, live data and action are called **missing attachments** because:

- A) they are things the model does badly and could be trained to do well
- B) they are things the model does not do at all, so the fix can sit outside it
- C) they were removed from the model to make it smaller
- D) they are optional features you pay extra for

**A9.** Correctness is said to differ **in kind** from the other three limits
because:

- A) it can be fixed with a large enough retrieval system
- B) it is a property of the training objective — the model was trained to produce
  *likely* text, and likely-true and likely-false have the same shape
- C) it only shows up at high temperature
- D) it is the easiest of the four to remove

**A10.** In the seven-step token loop, the only step where randomness enters is:

- A) Tokenize
- B) Forward pass
- C) Softmax
- D) Sample

**A11.** A **logit** is:

- A) a probability between 0 and 1
- B) a raw, unnormalised score — one number per vocabulary token
- C) the token id after tokenizing
- D) the position of a token in the sequence

**A12.** Softmax turns logits into a probability distribution by:

- A) dividing each logit by the largest logit
- B) subtracting the mean, then clipping negatives to zero
- C) exponentiating each value, then dividing by the sum of all the exponentials
- D) sorting the logits and assigning fixed percentages

**A13.** Lowering the temperature `T` (still above 0) does what to the
distribution?

- A) flattens it toward uniform
- B) sharpens it — inflates the gaps between logits
- C) leaves the probabilities unchanged, only the logits move
- D) removes the lowest-probability tokens

**A14.** The model is **confident** — one token sits at 97%. Compared with
`top-k = 5`, `top-p = 0.9` here will:

- A) keep more tokens
- B) keep about the same number of tokens
- C) keep fewer tokens — often just the one
- D) behave identically, because the model is confident

**A15.** In most APIs, `temperature = 0`:

- A) divides every logit by zero, which the library treats as 1
- B) is a flag that skips sampling and returns the argmax token
- C) makes every token equally likely
- D) raises an error

**A16.** Which verification location makes an illegal token **impossible to draw**
rather than merely detectable afterwards?

- A) deterministic code that checks the output
- B) a second model call that reviews the first
- C) constrained decoding — masking illegal logits to −∞ before softmax
- D) human review before an irreversible action

**A17.** A JSON grammar compiled into a state machine guarantees:

- A) that the values in the JSON are true
- B) that the JSON is structurally valid
- C) both structure and content
- D) neither — grammars only speed up decoding

**A18.** Where in the pipeline does **temperature** act?

- A) on the probabilities, after softmax, alongside top-k and top-p
- B) on the logits, before softmax
- C) on the token ids, before the forward pass
- D) on the token that was drawn, as a correction

**A19.** Applying temperature *after* softmax instead of before would:

- A) sharpen the distribution twice as hard
- B) do nothing — dividing every probability by the same constant and then
  renormalising returns the original distribution exactly
- C) raise an error, because probabilities may not be divided
- D) always flatten the distribution toward uniform

**A20.** Top-k and top-p can only run *after* softmax because they:

- A) need the logits to all be positive first
- B) need actual probabilities — a count of the highest few, or a running sum that
  crosses a threshold
- C) are applied by the API rather than by the model
- D) depend on the temperature value being known

---

## Part B — Fill in the Blanks

**B1.** A large language model is a pure ______________: same input, same
computation, every time.

**B2.** Everything the model should "know" must be present in the ______________
of the current call.

**B3.** The model has no internal signal separating "I actually learned this fact"
from "this pattern is statistically ______________."

**B4.** Every action taken on the model's behalf passes through ______________ that
you wrote.

**B5.** In the token loop, steps 1 through 5 are fully ______________; identical
input produces identical ______________ every time.

**B6.** Because softmax is ______________, small differences in logit space become
______________ differences in probability space.

**B7.** As `T → 0⁺` the distribution converges to ______________ on the
highest-scoring token; as `T → ∞` it becomes ______________ across the whole
vocabulary.

**B8.** **Top-k** truncates by ______________; **top-p** truncates by
______________.

**B9.** By default, token selection is a ______________ ______________ ______________
— not a selection of the highest-probability token.

**B10.** Constrained decoding sets the logit of every currently-illegal token to
______________, so after softmax those tokens sit at probability ______________.

**B11.** The four verification locations, in the order the page gives them, are:
constrained decoding (______________), deterministic code (______________), model
self-review (______________), and human review (______________).

**B12.** Model self-review is weak at catching ______________ wrongness, because
you are using the same ______________ that produced the error to judge it.

**B13.** The full pipeline order is: logits → ______________ → softmax →
______________ → draw.

**B14.** Scaling a logit by `1/T` becomes an exponent change on the unnormalised
weight: each weight is raised to the power ______________. At `T = 0.5` that is
______________; at `T = 2` it is a ______________.

---

## Part C — Scenarios and Working

**C1.** A developer says: "My chatbot remembered my name three messages later, so
the model clearly has some short-term memory." Using the reading page, explain
what actually happened, and name the component responsible for it.

**C2.** Two runs of the same prompt, same model, same settings, give two different
answers. Your colleague says "the model changed its mind." Correct the statement
using the words *distribution* and *draw*, and say which one of the seven loop
steps is responsible for the difference.

**C3.** For each task below, name the verification location from Part 6 you would
put **first**, and say in one line what it can catch that the others cannot:

- (a) the model must return JSON that your parser can load
- (b) the model claims a sentence appears in a retrieved document
- (c) the reply is factually fine but the tone is too blunt for a customer
- (d) the model's output will trigger a refund email that cannot be recalled

**C4.** A toy vocabulary has three tokens with these logits:

```
yes   = 2.0
no    = 0.5
maybe = 0.0
```

Compute the softmax probabilities at `T = 1.0`. Show the exponentials, the sum,
and each probability to the nearest whole per cent.

**C5.** Using the same three logits as C4, compute the distribution at `T = 0.5`
and again at `T = 2.0`. Then state, in one sentence each, what happened to
`maybe`'s share and why.

**C6.** A model has produced this distribution over five tokens:

```
the  = 0.50
a    = 0.25
one  = 0.15
this = 0.07
some = 0.03
```

- (a) Apply `top-k = 2`: list the surviving tokens and their renormalised
  probabilities.
- (b) Apply `top-p = 0.8` to the original distribution: list the surviving tokens
  and their renormalised probabilities.
- (c) One kept two tokens and one kept three. Explain why, in terms of what each
  method measures.

**C7.** Still using the C6 distribution: which token does `temperature = 0` return,
and how often? Then explain why that is *not* the same thing as "the `T → 0⁺`
limit" in principle, even though the page says the substitution is sound.

**C8.** For each situation, say whether you would use **greedy decoding** or
**sampling**, and give one reason:

- (a) extracting the invoice total from a scanned document into a fixed field
- (b) generating three different opening sentences for a blog post
- (c) classifying a support ticket as `billing`, `bug` or `other`
- (d) brainstorming names for a new product

**C9.** Two tokens have logits `3.0` and `1.0`. Use `e^2.0 = 7.39` and
`e^4.0 = 54.60`.

- (a) Their logits differ by 2. What is the ratio of their *unnormalised* softmax
  weights, `e^3.0 : e^1.0`?
- (b) Apply `T = 0.5`. Write the two scaled logits, their new difference, and the
  new ratio of unnormalised weights.
- (c) The underlying scores never changed. In one sentence, say what temperature
  did to the gap between these two tokens, and why top-k or top-p could not have
  produced the same effect.
