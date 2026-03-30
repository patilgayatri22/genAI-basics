# ⚙️ Transformers 101

> **Basics Module 03** | ⏱️ 25–30 min read | 🟢 Beginner  
> Prerequisites: [02 — How LLMs Work](./02_How_LLMs_Work.md)

---

## 🎯 What You'll Learn

- Why the Transformer was such a breakthrough (and what it replaced)
- The self-attention mechanism — explained through analogy, not equations
- What "multi-head attention" means and why it matters
- How the layers stack up to form a complete model
- What positional encoding is and why it's needed
- How to think about what each layer is "doing"

> **Promise:** No calculus. No matrix multiplication. Just intuition.

---

## 1. What Problem Did Transformers Solve?

Before 2017, language models used **recurrent neural networks (RNNs)**. They processed text like reading a sentence left to right, one word at a time, carrying a "memory" forward.

```
RNN approach (word by word):
"The cat sat on the mat" →
  Read "The" → update memory
  Read "cat" → update memory (but "The" is fading)
  Read "sat" → update memory (but "The cat" is fading)
  ...
  Read "mat" → generate response (but "The" is barely remembered)
```

**Problems:**
- Long-range dependencies got lost — the model forgot early words
- Processing was sequential — couldn't be parallelised, so training was slow
- The further apart two words were, the harder it was to connect them

**The Transformer's solution:** throw out sequential processing entirely. Look at **all words simultaneously**, and learn which ones to pay attention to.

---

## 2. The Core Idea: Attention

Attention is the heart of the Transformer. The intuition:

> For each word in a sentence, decide which other words are most relevant to understanding it — then use those words to build a richer understanding.

### A concrete example

Consider: *"The animal didn't cross the street because **it** was too tired."*

What does "it" refer to? The animal — not the street. Humans figure this out by connecting "it" to "animal" based on context. Attention learns to do the same.

```
Word: "it"

Attention weights (how much to focus on each other word):
  "The"     → 0.02  (barely relevant)
  "animal"  → 0.71  ← strong connection
  "didn't"  → 0.01
  "cross"   → 0.03
  "the"     → 0.01
  "street"  → 0.08  (slightly relevant — it's the other candidate)
  "because" → 0.05
  "it"      → 0.04  (itself)
  "was"     → 0.02
  "too"     → 0.02
  "tired"   → 0.01
```

The model learns to assign high weight to "animal" when processing "it", so the final representation of "it" borrows heavily from "animal".

This is **self-attention**: every word attends to every other word to build richer meaning.

---

## 3. Query, Key, Value — The Attention Mechanism

You'll hear these three terms constantly. Here's an analogy to make them click.

### The Library Analogy

Imagine you're looking for books in a library:

- **Your query** = what you're looking for ("books about machine learning")
- **The keys** = the labels/titles on each book's spine
- **The values** = the actual content inside each book

You compare your query against all the keys to decide which books are most relevant. Then you read (attend to) those books' values — weighted by how relevant each was.

```
In attention:
  Query  = "What am I looking for right now?" (derived from the current word)
  Keys   = "What does each word offer?" (derived from every word in the sequence)
  Values = "What information does each word contain?" (also derived from each word)

Process:
  1. Compare this word's Query against every word's Key
  2. More similar = higher attention score
  3. Softmax → convert scores to probabilities (sum to 1)
  4. Weighted sum of Values → new, richer representation of this word
```

The weights are not manually set — they're **learned during training**. The model discovers which query-key relationships matter for the task.

---

## 4. Multi-Head Attention: Many Perspectives at Once

A single attention operation captures one type of relationship. But language has many kinds of relationships simultaneously:

- Grammatical: *subject → verb agreement*
- Semantic: *"bank" → "river" or "bank" → "money"*
- Coreference: *"it" → "animal"*
- Positional: *"not" affects the word right after it*

**Multi-head attention** runs several attention operations in parallel — each "head" can learn to specialise in a different type of relationship.

```
Input sentence
     ↓
  ┌─────────────────────────────────────────────┐
  │  Head 1: focuses on grammatical structure   │
  │  Head 2: focuses on semantic meaning        │
  │  Head 3: focuses on coreference (who=who)   │
  │  Head 4: focuses on negation/modification   │
  │  Head 5: focuses on positional proximity    │
  │  ...                                        │
  │  Head 12: learns something unexpected       │
  └─────────────────────────────────────────────┘
     ↓
  Combine all heads → rich, multi-perspective representation
```

GPT-2 (small) has 12 attention heads. GPT-3 has 96. Each head sees the full sentence but learns to notice different patterns.

---

## 5. The Feed-Forward Layer: Processing What Attention Found

After attention, each word has a new, context-enriched representation. This passes through a **feed-forward network (FFN)** — a simple two-layer neural network applied independently to each word position.

If attention is about **which words to look at**, the FFN is about **what to do with that information** — transforming it, compressing it, and preparing it for the next layer.

Think of it as:
- Attention = gathering context ("what else is relevant here?")
- FFN = processing that context ("now what does this mean?")

---

## 6. Layer Norms and Residual Connections

Two more pieces you'll encounter:

### Residual connections (skip connections)
After each attention or FFN operation, the original input is **added back** to the output:

```
output = LayerNorm(input + attention(input))
```

Why? It helps information flow through deep networks. Instead of transforming the input completely at each step, each layer only needs to learn **the change** — much easier to train.

### Layer Normalisation
Keeps the numbers inside the model from growing too large or shrinking too small during training. It stabilises training and isn't something you need to worry about conceptually.

---

## 7. Positional Encoding: Where Is Each Word?

Self-attention is brilliant at finding relationships between words — but it has one blind spot. Because it processes all words simultaneously, it has **no built-in sense of order**.

```
Without positional encoding, these look identical to attention:
"The dog bit the man."
"The man bit the dog."
Both contain the same tokens — just in different positions.
```

**Positional encoding** adds a signal to each token's embedding that encodes its position in the sequence.

```
Token at position 0: embedding + position_signal(0)
Token at position 1: embedding + position_signal(1)
Token at position 2: embedding + position_signal(2)
...
```

Now the model can distinguish "The dog bit the man" from "The man bit the dog" — the same words in different positions have different combined representations.

Modern models use **Rotary Position Embeddings (RoPE)**, which handle very long sequences more gracefully — one reason why 200K-token context windows are now possible.

---

## 8. One Full Transformer Block

Putting it together, one transformer block looks like this:

```
Input tokens (as embeddings)
         │
         ▼
┌─────────────────────────────┐
│     Layer Normalisation      │
└─────────────────────────────┘
         │
         ▼
┌─────────────────────────────┐
│    Multi-Head Self-Attention │  ← "What else is relevant here?"
│    (Q, K, V matrices)        │
└─────────────────────────────┘
         │
         ▼
┌─────────────────────────────┐
│    + Residual connection     │  ← add original input back
└─────────────────────────────┘
         │
         ▼
┌─────────────────────────────┐
│     Layer Normalisation      │
└─────────────────────────────┘
         │
         ▼
┌─────────────────────────────┐
│    Feed-Forward Network      │  ← "Now process that information"
│    (Linear → GELU → Linear)  │
└─────────────────────────────┘
         │
         ▼
┌─────────────────────────────┐
│    + Residual connection     │  ← add input back again
└─────────────────────────────┘
         │
         ▼
Richer token representations
```

---

## 9. Stacking Layers: From Words to Meaning

A real LLM stacks many of these blocks on top of each other. Each layer refines the representations from the previous one.

```
Layer 1:  Learns basic syntax — "this is a verb, that is a noun"
Layer 2:  Learns word relationships — "this noun is the subject of that verb"
Layer 3:  Starts capturing semantics — "this sentence is about frustration"
...
Layer 12: Has a rich, contextualised understanding of each token's role
Layer 24: Integrates high-level meaning, tone, and intent
...
Layer 96: Ready to predict the next token
```

This is why deeper models are more capable — more layers = more rounds of refinement = richer understanding.

### How deep are real models?

| Model | Layers | Attention heads |
|-------|--------|----------------|
| GPT-2 Small | 12 | 12 |
| GPT-3 | 96 | 96 |
| Llama 3 8B | 32 | 32 |
| Llama 3 70B | 80 | 64 |
| GPT-4 (estimated) | ~120 | ~96 |

---

## 10. The Output: From Numbers Back to Text

After all the layers, the final representation of the last token is passed through a **linear projection** + **softmax** to produce a probability distribution over the entire vocabulary.

```
Final layer output (for last token position)
        │
        ▼
[Linear layer]  →  50,000-dimensional vector (one score per token)
        │
        ▼
[Softmax]  →  50,000-dimensional probability distribution
        │
        ▼
Sample a token  →  "Paris" (with 68% probability)
```

This is the output: one token. Then it's appended to the input and the whole process runs again to generate the next token.

---

## 11. Visualising Attention (What Researchers Actually See)

When researchers study trained transformers, they can visualise which tokens attend to which. Some patterns that emerge:

```
"The animal didn't cross the street because it was too tired"
                                              │
                              "it" → strongly attends to → "animal"


"The bank by the river was overgrown with reeds"
        │
      "bank" → strongly attends to → "river" (river-bank sense)

"The bank froze my account after suspicious activity"
        │
      "bank" → strongly attends to → "account" (financial sense)
```

The model learns to **use context to disambiguate meaning** — exactly what attention was designed for.

---

## 12. The Transformer Family Tree

The original 2017 Transformer had two parts: an **encoder** (reads input) and a **decoder** (generates output), used for translation.

Modern language models have evolved from this:

```
Original Transformer (2017)
  ├── Encoder-only models
  │     └── BERT, RoBERTa — good at understanding, not generating
  │         Used for: classification, search, embeddings
  │
  ├── Decoder-only models
  │     └── GPT family, Llama, Claude, Mistral — generative models
  │         Used for: text generation, chat, code, reasoning
  │
  └── Encoder-Decoder models
        └── T5, BART, mT5 — read then generate
            Used for: translation, summarisation
```

When people say "LLM" today, they almost always mean a **decoder-only transformer**.

---

## 13. 🧪 Try It Yourself

### Exercise 1: Feel the attention
Go to [BertViz](https://github.com/jessevig/bertviz) (or search "transformer attention visualiser") and paste in a sentence. Watch which tokens attend to which. Look for:
- How pronouns attend to their antecedents
- How adjectives attend to the nouns they modify
- How verbs attend to their subjects

### Exercise 2: Probe different heads
If using BertViz, switch between attention heads. Notice each head specialises in something different — some track syntax, some track semantics, some seem mysterious.

### Exercise 3: Layer intuition
Ask an LLM:
- "What is the grammatical structure of this sentence: 'The quick brown fox jumps.'"  → early-layer knowledge
- "What is the emotional tone of this paragraph?" → later-layer knowledge
- "What is the implied meaning of this idiom?" → deepest-layer knowledge

### Exercise 4: The context window matters
Copy a 5,000-word article into a chat with an LLM. Ask a question whose answer is in the first paragraph. Now ask one whose answer is in the middle. Many models perform worse on "middle" retrieval — the "lost in the middle" phenomenon caused by how attention distributes over long sequences.

---

## Quick Reference Glossary

| Term | What it means |
|------|--------------|
| **Self-attention** | Each token in a sequence attends to all other tokens |
| **Query (Q)** | "What am I looking for?" — derived from the current token |
| **Key (K)** | "What do I offer?" — derived from each token in the sequence |
| **Value (V)** | "What information do I contain?" — the actual content |
| **Attention weights** | Softmax scores: how much to attend to each token |
| **Multi-head attention** | Multiple attention operations in parallel, each specialising |
| **Feed-forward network** | Per-token processing layer after attention |
| **Residual connection** | Add original input back to the output of each sub-layer |
| **Layer norm** | Stabilises internal values during training |
| **Positional encoding** | Injects token position information into embeddings |
| **Decoder-only** | The architecture used by GPT, Claude, Llama, etc. |

---

## Key Takeaways

- ✅ Transformers replaced sequential RNNs with **parallel, attention-based** processing
- ✅ **Self-attention** lets every token look at every other token to find what's relevant
- ✅ **Query, Key, Value** is the mechanism — like searching a library with relevance scoring
- ✅ **Multi-head attention** runs many attention operations in parallel, each learning different patterns
- ✅ **Stacked layers** progressively refine representations — from syntax to semantics to intent
- ✅ **Positional encoding** solves the ordering problem — attention alone has no sense of position

---

## 📚 Go Deeper

- [The Illustrated Transformer — Jay Alammar](https://jalammar.github.io/illustrated-transformer/) — the definitive visual guide
- [Attention Is All You Need — original paper](https://arxiv.org/abs/1706.03762) — worth reading the abstract + figures
- [BertViz — interactive attention visualiser](https://github.com/jessevig/bertviz)
- [3Blue1Brown — But what is a GPT? (YouTube)](https://www.youtube.com/watch?v=wjZofJX0v4M) — beautiful animation of transformers

---

*← Previous: [02 — How LLMs Work](./02_How_LLMs_Work.md)*  
*Next → [04 — Types of Generative Models](./04_Types_of_Generative_Models.md)*
