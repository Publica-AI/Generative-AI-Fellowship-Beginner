# Demo Guide — Topic 3: Encoder & Decoder Architecture

**Companion to:** *(no notebook this topic — see below)*
**AI-Use Mode:** Manual + Tutor — work through the diagrams and mappings yourself. Use AI to ask "why does this piece go here?" or to check your understanding, not to generate the walkthrough for you.

---

## Why There's No Notebook This Topic

Every other topic this week paired its slides with a notebook because the slides introduced a formula or a worked numeric example that was worth actually running. Topic 3 doesn't — its own closing slide says so directly: *"Nothing Here Is New Math."* Self-attention, cross-attention, masking, positional encoding, feed-forward, residual connections, layer normalisation — every mechanism this topic's slides reference was already built and explained, from scratch, in Topics 1, 2, and 4.

So instead of new code, this guide does two things:

1. **Walks through the encoder-decoder architecture** — diagrammatically, layer by layer, using the same English → Yoruba running example as the slides
2. **Maps every architectural piece back to a function that already exists** — so "learning" Topic 3 means *recognising* familiar code in a new position, not writing anything new

By the end, you should be able to look at any box in a Transformer architecture diagram and say, immediately, "that's `transformer_block()`" or "that's `scaled_dot_product_attention()` with a mask."

---

## Part 1 — The Encoder Stack, Mapped to Code You Already Have

Topic 3's Slides 4–7 describe the encoder layer by layer:

| Architectural Piece (Slide) | What It Is | Code You Already Built |
|---|---|---|
| One encoder layer (Slide 4) | Multi-head self-attention, then feed-forward, each wrapped in residual + norm | This is **exactly** `transformer_block()` from Topic 4 — nothing added, nothing missing |
| Residual connections (Slide 5) | `output = sublayer(x) + x` | Already inside `transformer_block()`: `layer_norm(X + attn_output)` and `layer_norm(X_after_attention + ff_output)` |
| Layer normalisation (Slide 6) | Rescale each token's values into a stable range | `layer_norm()` from Topic 4, called twice per block |
| Stacking N layers (Slide 7) | Run the same block structure N times, each with its own weights | Topic 4's Part 5 — the loop that fed `X_stacked` through `block_params` |

**In other words:** if you've run Topic 4's notebook, you've already built and run a complete encoder stack. Topic 3 didn't ask you to build anything new here — it asked you to see that `transformer_block()`, stacked, *is* "the encoder" the slides describe.

---

## Part 2 — The Decoder Stack: One New Idea, Zero New Functions

The decoder (Slide 8) has three sublayers instead of two: **masked self-attention**, **cross-attention**, and **feed-forward**. The feed-forward sublayer needs no discussion — it's the identical `feed_forward()` from Topic 4. The other two deserve a closer look, because the *idea* is new even though the *code* isn't.

### Masked Self-Attention

**The idea (Slide 9):** when the decoder is generating a translation one word at a time, it shouldn't be allowed to see words it hasn't generated yet.

**The mechanism:** this reuses `scaled_dot_product_attention()` (or `attention_from_qkv()`, for the multi-head version) exactly as built in Topic 1 — with one small addition. Before the softmax step, positions that represent "the future" get their score forced down to negative infinity, so after softmax they receive essentially 0% attention weight.

**Illustrative pseudocode** — not a new function to run, just showing how little actually changes:

```python
def masked_attention_from_qkv(Q, K, V):
    d_k = Q.shape[-1]
    raw_scores = Q @ K.T
    scaled_scores = raw_scores / math.sqrt(d_k)

    # NEW: block attention to any position that comes "after" this one
    seq_len = scaled_scores.shape[0]
    for row in range(seq_len):
        for col in range(row + 1, seq_len):   # everything to the right = the future
            scaled_scores[row][col] = float("-inf")

    weights = softmax_rows(scaled_scores)   # -inf becomes ~0% after softmax
    output = weights @ V
    return output, weights
```

Everything else — Score, Scale, Normalise, Blend — is identical to Topic 1. The only change is a handful of scores getting overwritten before softmax runs.

### Tracing It By Hand

Say the decoder is generating NaijaLingo's Yoruba translation and has produced `"Mo n lọ si"` so far, and needs to predict the 5th word:

| Position asking ("Query") | Can attend to | Blocked from |
|---|---|---|
| `Mo` (1st) | `Mo` | `n`, `lọ`, `si`, position 5 |
| `n` (2nd) | `Mo`, `n` | `lọ`, `si`, position 5 |
| `lọ` (3rd) | `Mo`, `n`, `lọ` | `si`, position 5 |
| `si` (4th) | `Mo`, `n`, `lọ`, `si` | position 5 |
| **position 5 (being predicted)** | `Mo`, `n`, `lọ`, `si` | *(nothing after it exists yet)* |

The model predicts `"ọja"` using only what's to its left — plus, via cross-attention, the encoder's understanding of the full English sentence. It never gets to "peek" at the answer.

**Why this matters — and why it wasn't needed in Topics 1–2:** the encoder reads the *entire* English sentence at once, so there's nothing to hide — every word is already available. The decoder is different: it's generating output step by step, and at generation time, future words genuinely don't exist yet. Masking makes training match that reality, so the model never learns to rely on information it won't have later.

### Cross-Attention

**The idea (Slide 10):** this is how the decoder actually "looks at" the English sentence.

**The mechanism:** still the exact same attention function — the only change is *where* Query, Key, and Value come from.

| | Self-Attention (Topic 1) | Cross-Attention (Topic 3) |
|---|---|---|
| Query from | same sequence | the decoder |
| Key, Value from | same sequence | **the encoder's output** |

**Illustrative pseudocode:**

```python
def cross_attention(decoder_X, encoder_output, W_Q, W_K, W_V):
    Q = decoder_X @ W_Q          # Query comes from the DECODER
    K = encoder_output @ W_K     # Key comes from the ENCODER's final output
    V = encoder_output @ W_V     # Value comes from the ENCODER's final output
    d_k = Q.shape[-1]
    raw_scores = Q @ K.T
    scaled_scores = raw_scores / math.sqrt(d_k)
    weights = softmax_rows(scaled_scores)
    output = weights @ V
    return output, weights
```

Notice this function's *body* is identical to `scaled_dot_product_attention()` from Topic 1. The only difference is that `K` and `V` are computed from a *different* input (`encoder_output`) than `Q` is (`decoder_X`). That's the entire mechanical difference between self-attention and cross-attention — nothing about Score, Scale, Normalise, or Blend changes.

---

## Part 3 — The Full Flow, Traced Through Existing Code

Putting Parts 1–2 together, here's what a complete translation pass would call, using functions you've already built:

```python
# 1. Encoder: read the full English sentence (Topics 1-2-4, exactly as run in the Module Demo)
encoder_output = X_pos
for block_weights in encoder_block_params:
    encoder_output, _ = transformer_block(encoder_output, *block_weights, num_heads)

# 2. Decoder: generate Yoruba one token at a time
decoder_tokens = []   # starts empty
for step in range(max_length):
    decoder_X = embed_and_position_encode(decoder_tokens)   # whatever has been generated so far

    # Sublayer 1: masked self-attention over decoder_tokens so far
    masked_out, _ = masked_attention_from_qkv(decoder_X @ W_Q, decoder_X @ W_K, decoder_X @ W_V)
    decoder_X = layer_norm(decoder_X + masked_out)

    # Sublayer 2: cross-attention over the encoder's output
    cross_out, _ = cross_attention(decoder_X, encoder_output, W_Q_cross, W_K_cross, W_V_cross)
    decoder_X = layer_norm(decoder_X + cross_out)

    # Sublayer 3: feed-forward (identical to Topic 4)
    ff_out = feed_forward(decoder_X, W1, b1, W2, b2)
    decoder_X = layer_norm(decoder_X + ff_out)

    next_word = predict_next_token(decoder_X)   # outside this week's scope
    decoder_tokens.append(next_word)
```

This is illustrative, not something to run — `embed_and_position_encode()` and `predict_next_token()` are placeholders for machinery outside this week's scope (a vocabulary, a final output layer, training). But every *Transformer-specific* piece in this sketch — masked attention, cross-attention, feed-forward, residual, norm — is code you've already written and tested.

---

## Common Questions

**If the decoder needs three sublayers and the encoder only needs two, why does `transformer_block()` still cover "the encoder"?**
Because `transformer_block()` *is* the encoder layer, exactly as-is. The decoder layer is a variant with one extra sublayer inserted (masked self-attention, then cross-attention, then feed-forward) — not a totally different structure. If you understand `transformer_block()`, you already understand two-thirds of a decoder layer.

**Why does masking use negative infinity instead of just setting the weight to 0 directly?**
Because softmax needs to see the *raw score* to correctly redistribute probability across the remaining (unmasked) positions. Setting a weight to 0 after softmax would leave the other weights not summing to 1 anymore. Setting the *score* to `-inf` before softmax means `exp(-inf) = 0`, so that position naturally drops out while the rest of the row still sums to 1.

**Does cross-attention need its own `W_Q`, `W_K`, `W_V`, separate from self-attention's?**
Yes — in a real model, the decoder's masked self-attention and its cross-attention are separate sublayers with their own independently learned projection weights, even though they call the same underlying attention mechanism.

**Is there really nothing new to implement this week for the decoder?**
Correct, by design — Topic 3 is explicitly conceptual. If you wanted to actually build and run a decoder, you'd be reusing every function from Topics 1, 2, and 4, plus the small masking addition shown above. That's intentionally left as a natural extension beyond this week's Manual + Tutor scope, not a gap in what you've learned.

---

## Up Next

**Topic 4 — Implementing a Simplified Transformer Block** (if you haven't already worked through it) assembles the encoder side of everything mapped out above into `transformer_block()` — the exact function this guide has been referring back to throughout.

**The Module Demo — Transformer from Scratch** runs that encoder stack end-to-end on NaijaLingo's English–Yoruba example, and its README notes explicitly that a full decoder (using the masked attention and cross-attention sketched in this guide) is the natural next step beyond this week's scope.
