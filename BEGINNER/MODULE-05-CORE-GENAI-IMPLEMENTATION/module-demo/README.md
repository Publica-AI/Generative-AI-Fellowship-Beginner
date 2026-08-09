# Module Demo — Transformer from Scratch
### NaijaLingo: A Mini Translation Engine, Built From the Ground Up

**Companion to:** `notebook.ipynb`

This is Week 9's capstone demo. It doesn't introduce any new math — every function used here was already built, explained, and tested in Topics 1, 2, and 4's individual demo notebooks. What's new is running them all together, in the order a real encoder actually uses them, on one continuous example.

---

## The Story

You're prototyping for **NaijaLingo**, a Nigerian language-tech startup building translation tools. Before reaching for an off-the-shelf Transformer library, the goal is to prove — line by line — an understanding of what's actually happening inside one. This notebook is that proof, built entirely in NumPy, on the sentence pair used throughout the week:

**English:** `"I am going to the market"`
**Yoruba:** `"Mo n lọ si ọja"`

---

## How the Notebook Maps Back to the Week

| Notebook Section | What It Does | Reused From |
|---|---|---|
| **Section 1 — Tokenize** | Splits the sentence into tokens, assigns each a starting embedding vector | New setup, same pattern as Topics 1–2 |
| **Section 2 — Scaled Dot-Product Attention** | Runs single-head self-attention: Score → Scale → Normalise → Blend | Topic 1's `scaled_dot_product_attention()`, unchanged |
| **Section 3 — Multi-Head Attention + Positional Encoding** | Splits into 2 heads, runs attention per head, combines and projects; adds sine/cosine positional encoding before attention | Topic 2's `multi_head_attention()` and `positional_encoding()`, unchanged |
| **Section 4 — Full, Stacked Transformer Block** | Wraps attention and a feed-forward network in residual connections + layer normalisation; stacks 2 independent blocks | Topic 4's `layer_norm()`, `feed_forward()`, and `transformer_block()`, unchanged |
| **Section 5 — Inspect the Final Output** | Prints the encoder's final output vectors and measures how much each word's representation changed from where it started | New synthesis step, tying the whole pipeline together |

Every function is reused exactly as written in its original topic notebook — nothing was rewritten or simplified further for this demo. If a function's behaviour is unclear here, the original topic's demo guide has the full, cell-by-cell explanation:

- **Topic 1's demo guide** — for `softmax()`, `softmax_rows()`, `scaled_dot_product_attention()`
- **Topic 2's demo guide** — for `split_into_heads()`, `concatenate_heads()`, `multi_head_attention()`, `positional_encoding()`
- **Topic 4's demo guide** — for `layer_norm()`, `feed_forward()`, `transformer_block()`

---

## What to Notice While Running It

- **Section 2 → Section 3:** the jump from single-head to multi-head attention with positional encoding is the same jump Topic 2 walked through — watch how the attention weight patterns differ once there are 2 heads instead of 1, and once position is added to the mix.
- **Section 4:** this is Topic 3's architecture (residual connections, layer normalisation, stacked layers) made concrete — everything Topic 3's slides described conceptually is now something actually running, 2 layers deep.
- **Section 5's final check:** the "mean absolute change" printout is a simple way to confirm the pipeline actually did something meaningful — every word's representation should have moved noticeably from its starting embedding, shaped by attention, position, and two full blocks of processing.

---

## Where This Would Go Next (Beyond This Week)

This notebook builds NaijaLingo's **encoder** — the half of a translation model that reads and understands the English sentence. A complete translation system would also need:

- A **decoder**, generating the Yoruba translation one token at a time, using masked self-attention (Topic 3, Slide 9)
- **Cross-attention**, letting the decoder look back at this encoder's output (Topic 3, Slide 10)
- **Training** — everything in this week's notebooks used random weights standing in for what a model would actually *learn*; a real system would train these weights on translation examples

Those pieces are outside this week's scope (Manual + Tutor, hands-on math only) — but conceptually, they're exactly what Topic 3's slides walked through. This notebook is the encoder half of that picture, fully built and running.

---

## A Note on Notebook Style

Consistent with Topics 1, 2, and 4: this notebook favours plain loops over NumPy's more compact indexing tricks (like `matrix[:, start:end]` or `axis=`/`keepdims=` broadcasting) anywhere a new concept is being introduced. `@` (matrix multiply) is used freely for projections and linear layers, since Topic 1 already proved — with real numbers — that it's equivalent to writing the underlying math out by hand.
