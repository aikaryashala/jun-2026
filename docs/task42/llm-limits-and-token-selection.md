# What an LLM Actually Does, and Why That Determines Everything Else

## The one-sentence model

A large language model takes a sequence of tokens and produces a probability distribution over the next token.

That is the whole thing. Chat, reasoning, summarisation, code generation, tool use — none of these are separate capabilities inside the model. They are all the same operation, repeated, with different text in front of it.

Almost every technique in applied LLM engineering exists because that single operation has four structural limits. Understanding the limits is more useful than memorising the techniques, because the limits are stable and the techniques change every six months.

---

## Part 1 — The four limits

### 1. No memory

The model is a pure function. Same input, same computation, every time. There is no session object, no variable inside the model holding your previous message, nothing that persists between calls.

What feels like a conversation is an illusion maintained entirely by the client. Your application resends the full transcript on every single turn. The model reads it fresh each time and has no idea it has seen any of it before.

**Consequence:** conversation state is your problem, not the model's. Everything the model should "know" must be present in the input of the current call.

### 2. No live data

Weights are frozen at training time. Knowledge is not looked up from a store — it is compressed, lossily, into billions of parameters during training.

Two things follow. First, anything after the training cutoff does not exist to the model. Second, and more troubling, the model has no internal signal distinguishing "I actually learned this fact" from "this pattern is statistically plausible." Both feel identical from the inside.

**Consequence:** the model cannot tell you what it doesn't know, because not knowing and confidently generating produce the same internal state.

### 3. No ability to act

The output is text. Only text. The model cannot query a database, call an API, write a file, or send an email.

Function calling does not change this. When a model "calls a tool," it emits a structured string that happens to look like a function call. Your code parses that string and decides whether to execute anything. The model proposes; your application disposes. That boundary is where all your safety controls live.

**Consequence:** every action taken on the model's behalf passes through code you wrote. Treat model output as an untrusted request, not a command.

### 4. No guarantee of correctness

This one differs in kind from the other three, and the difference matters for design.

Memory, live data, and action are **missing attachments**. They are not things the model does badly — they are things it does not do at all. The deficit sits outside the model, so the fix can sit outside too: a transcript store, a retrieval system, an execution layer.

Correctness is not a missing attachment. It is a property of the training objective. The model was trained to produce *likely* text. Likely-and-true and likely-and-false are the same shape to it. There is no separate truth module to bolt on, because being wrong is not a gap in the system — it is the system working exactly as trained.

You can push the error rate down substantially. Retrieval grounds output in real documents. Tools let the model compute instead of guess. Prompting narrows the space. Evaluation catches regressions. But every one of these changes the *input*. None changes the objective. The floor never reaches zero.

**Consequence:** fluency carries no information about accuracy. Confidence in the output tells you nothing, because confident phrasing and correct content are produced by the identical mechanism.

---

## Part 2 — The token generation loop

To reason about where interventions are possible, you need the mechanics of a single token.

| Step | What happens |
|---|---|
| 1. Tokenize | Text becomes integer IDs from a fixed vocabulary (roughly 50k–200k entries). Tokens are subword fragments, not words. |
| 2. Embed | Each ID becomes a vector, combined with positional information so order carries meaning. |
| 3. Forward pass | Vectors flow through N transformer layers. Each layer mixes information across positions (attention) and transforms each position independently (feed-forward). |
| 4. Logits | The final layer emits one raw score per vocabulary entry — a vector as long as the vocabulary. |
| 5. Softmax | Scores become a probability distribution summing to 1. |
| 6. Sample | One token is drawn from that distribution. Temperature, top-k and top-p operate here. |
| 7. Append and repeat | The chosen token joins the input; the loop runs again until a stop token or length cap. |

Steps 1 through 5 are fully deterministic. Identical input produces identical logits, every time. The only randomness in the entire system enters at step 6.

That last sentence is worth holding onto. When output varies between runs, the model did not "change its mind." The distribution was the same; the draw was different.

---

## Part 3 — Logits and softmax

### Logits

A logit is the raw, unnormalised score the network produces for each vocabulary token. One number per token. It may be negative, zero, or large. It is not a probability — the values do not sum to anything meaningful. Read it as evidence strength before normalisation.

Take a toy vocabulary of four tokens. The model has seen `the cat`:

| Token | Logit |
|---|---|
| sat | 3.0 |
| ran | 1.0 |
| is | 0.0 |
| purple | −1.0 |

### Softmax

Softmax converts logits into a valid probability distribution in two steps: exponentiate each value, then divide by the sum of all exponentials.

```
e^3.0  = 20.09
e^1.0  =  2.72
e^0.0  =  1.00
e^-1.0 =  0.37
         ------
sum    = 24.18
```

| Token | Probability |
|---|---|
| sat | 83% |
| ran | 11% |
| is | 4% |
| purple | 2% |

Two properties matter here.

Exponentiating makes every value positive, so negative logits are handled naturally — a negative logit becomes a small positive probability, never an invalid one.

And because the transform is exponential, small differences in logit space become large differences in probability space. A two-point lead produced an 83% to 11% gap. This is why models often appear far more decisive than their underlying scores suggest.

---

## Part 4 — Reshaping the distribution

Three knobs modify the distribution before the draw. All three operate between steps 5 and 6.

### Temperature

Temperature divides every logit by T before softmax is applied.

Low T inflates the gaps between logits, sharpening the distribution. High T compresses them, flattening it. Using the same four logits above:

| Token | T = 0.5 | T = 1.0 | T = 2.0 |
|---|---|---|---|
| sat | 97.9% | 83% | 58% |
| ran | 1.8% | 11% | 21% |
| is | 0.2% | 4% | 13% |
| purple | 0.03% | 2% | 8% |

Same model, same context, same logits. Only the reshaping changed.

The two limits are instructive:

- **T → 0⁺** — every gap divided by T grows without bound, so after exponentiation the largest logit dominates by an unbounded factor. The distribution converges to 1.0 on the highest-scoring token and 0.0 everywhere else.
- **T → ∞** — every logit divides down toward zero, all exponentials approach 1, and the distribution becomes uniform across the entire vocabulary. The model's opinion is discarded entirely.

So temperature interpolates between "always take the model's top pick" and "ignore the model."

### Top-k

Top-k truncates by count. Keep the k highest-probability tokens, discard the rest, renormalise over what remains.

With k = 2 on the distribution above: keep `sat` and `ran`, drop `is` and `purple`, renormalise to 88% and 12%. The discarded tokens now have probability zero — they cannot be drawn.

### Top-p (nucleus sampling)

Top-p truncates by cumulative probability mass instead of count. Sort tokens descending, accumulate probabilities, and stop once the running total crosses p. Keep that set, renormalise.

With p = 0.9: `sat` (0.83), then `ran` (running total 0.94, threshold crossed). Keep both.

### Why the difference matters

Top-k applies a fixed budget regardless of the distribution's shape. Top-p adapts to how certain the model is.

When the model is confident — one token at 97% — top-k = 5 still admits four low-quality candidates. Top-p stops at one. When the model is genuinely uncertain across forty plausible continuations, top-k = 5 amputates most of the reasonable set, while top-p keeps it.

---

## Part 5 — The draw

By default, token selection is a **weighted random draw**, not a selection of the highest-probability token.

Picture a dartboard where each token occupies an arc proportional to its probability. `sat` gets 83% of the circumference, `purple` gets 2%. Throw one dart. You will usually hit `sat` — but not always. This is the entire source of run-to-run variation in LLM output.

The exception is **greedy decoding**: take the argmax, always. This is what `temperature = 0` means in most APIs. It is not a genuine temperature value — division by zero is undefined — but a flag that skips sampling and returns the highest-scoring token directly. Since the T → 0⁺ limit converges on exactly that behaviour, the substitution is mathematically sound.

Greedy decoding is deterministic and is usually correct for extraction, classification, and structured output. Sampling is preferable where variation is desirable: open-ended generation, brainstorming, creative work.

---

## Part 6 — Where verification can live

Since correctness cannot be guaranteed inside the model, something outside must decide whether output is acceptable. There are four locations, and they differ in what they can catch.

### Constrained decoding — prevention

The strongest option, and the only one that operates *inside* the generation loop rather than after it.

Before softmax, set the logit of every currently-illegal token to negative infinity. After softmax those tokens sit at probability zero. Not unlikely — zero. The sampler cannot draw them at any temperature, on any run.

Something must supply the legal set at each step. This is typically a grammar or JSON schema compiled into a state machine: after `{` only a quote is legal; after a key only `:`; and so on. The machine advances with each generated token and hands the sampler a fresh mask.

The boundary is important. A grammar guarantees structurally valid JSON. It cannot guarantee the values inside are true. Structure is enforceable; content is not.

### Deterministic code — verification

Does the JSON parse? Does the SQL execute? Do the generated tests pass? Does that citation actually appear in the retrieved document?

Cheap, fast, and — unlike the model — impossible to fool with fluent prose. This should be your default layer for anything mechanically checkable.

### Model self-review — critique

A second call reviews the first, or the model critiques its own output.

Useful for tone, completeness, and obvious gaps. But note the weakness: you are using the same mechanism that produced the error to judge the error. There is no independent ground to stand on. It catches sloppiness reliably and confident wrongness poorly.

### Human review — judgement

Slow and expensive, so it is spent where errors are costly. The approve-before-send gate before an irreversible action.

### Composing them

Production systems stack all four: constrain the shape, validate with code, use the model to review substance, escalate to a human at the risky edge. Each layer catches a different failure class, and the cost rises as you move down the list — which is the right ordering, since the cheap layers filter most of the volume.

---

## Summary

The model is a stateless function producing a distribution over next tokens. It has no memory, no live data, and no ability to act — three gaps that are filled by external machinery. It also has no guarantee of correctness, which is not a gap but a property of what it was trained to do, and therefore reducible but never removable.

Token selection is deterministic through logits and softmax, then reshaped by temperature, top-k, or top-p, then resolved by a weighted random draw — or by argmax, if you disable sampling.

Constrained decoding is the only intervention that makes bad output impossible rather than detectable, because it acts on the distribution before the draw. Everything else in the verification stack is detection after the fact.

---

## Where this goes next

Three limits remain open: memory, live data, and action. Each has a standard bolt-on — conversation state management, retrieval-augmented generation, and tool execution. Composed together with a control loop, those three are what people mean by an agent.
