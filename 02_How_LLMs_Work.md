# 🧠 How LLMs Work

> **Basics Module 02** | ⏱️ 20–25 min read | 🟢 Beginner  
> Prerequisites: [01 — What Is Generative AI](./01_What_Is_Generative_AI.md)

---

## 🎯 What You'll Learn

- What tokens are and why they matter
- How text becomes numbers (and why that's necessary)
- What "next-token prediction" means and why it's so powerful
- What a context window is and why it's a constraint
- How the model "remembers" a conversation
- Why bigger isn't always better

---

## 1. The Core Loop in One Sentence

> An LLM reads your input one token at a time, and for each step it asks: *"Given everything I've seen so far, what token should come next?"*

That's it. Everything — essays, code, jokes, therapy, legal summaries — comes from repeating that loop.

---

## 2. What Is a Token?

Before an LLM can process text, it needs to convert it into numbers. The bridge between human text and machine numbers is the **token**.

A token is a chunk of text — roughly a word, but not always. The model uses a **tokenizer** to split text into tokens before processing.

### Examples

```
"Hello, world!"
→ ["Hello", ",", " world", "!"]   = 4 tokens

"Generative AI is transforming the world."
→ ["Gener", "ative", " AI", " is", " transform", "ing", " the", " world", "."]   = 9 tokens

"cat"      → 1 token
"cats"     → 1 token
"caterpillar" → 3 tokens  ["cat", "erp", "illar"]

"print('hello')"
→ ["print", "('", "hello", "')"]   = 4 tokens
```

Notice:
- Common words are usually one token
- Rare or long words get split into smaller pieces
- Punctuation, spaces, and capitalisation all affect tokenisation
- Code, numbers, and non-English text often use more tokens per word

### Why does this matter for you?

Because **APIs charge per token**, and **context windows are measured in tokens**. Knowing roughly how many tokens your text uses helps you:
- Estimate costs
- Know when you're approaching limits
- Write more efficient prompts

**Rule of thumb:** 1 token ≈ 0.75 English words, or about 4 characters.

| Text | Approximate tokens |
|------|--------------------|
| "Hello" | 1 |
| A typical tweet | 20–40 |
| A full email | 100–300 |
| A short article (800 words) | ~1,000 |
| A novel chapter | ~3,000–5,000 |
| The entire Harry Potter series | ~1.5 million |

---

## 3. How Text Becomes Numbers: Embeddings

Tokens are IDs (numbers like 15496, 8348, 612). But token IDs alone don't capture meaning. The model converts each token ID into a **embedding** — a list of hundreds or thousands of floating-point numbers called a **vector**.

```
Token: "king"
Embedding: [0.24, -0.87, 0.11, 0.63, -0.42, ... ]  ← 768 or more numbers
```

The magic: **similar meanings produce similar vectors**.

```
"king"   → [0.24, -0.87, 0.11, ...]
"queen"  → [0.21, -0.82, 0.14, ...]   ← very close!
"banana" → [-0.91, 0.34, -0.77, ...]  ← far away
```

This is how the model knows that "king" and "queen" are related, and that both are far from "banana", even though those are all just strings of letters to a computer.

A famous example:
```
embedding("king") - embedding("man") + embedding("woman") ≈ embedding("queen")
```

The embedding space encodes relationships. This is the foundation of how the model "understands" language.

---

## 4. Next-Token Prediction

Here's the core mechanism. Given a sequence of tokens, the model outputs a **probability distribution** over the entire vocabulary — every possible next token gets a probability score.

```
Input:   "The capital of France is"

Model outputs probabilities for every token in vocabulary:
  "Paris"    → 68.2%   ← highest
  "Lyon"     → 4.1%
  "located"  → 3.8%
  "a"        → 2.9%
  "one"      → 1.4%
  ... (50,000+ more tokens, all with tiny probabilities)
```

The model picks a token (usually sampling from the top probabilities), appends it to the sequence, and repeats.

```
Step 1:  "The capital of France is" → picks "Paris"
Step 2:  "The capital of France is Paris" → picks "."
Step 3:  "The capital of France is Paris." → picks end-of-sequence token → done
```

This is called **autoregressive generation** — the output of each step feeds into the next.

### What controls which token is picked?

**Temperature** is the main dial:

```
Temperature = 0.0  →  Always pick the highest-probability token
                       Deterministic, repetitive, safe

Temperature = 0.7  →  Usually pick high-probability tokens, occasionally surprise
                       Good for most tasks

Temperature = 1.5  →  Sample more freely, including unlikely tokens
                       More creative, more unpredictable
```

```python
# You've probably seen this in API calls:
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Tell me a joke"}],
    temperature=0.9,   # ← this is what temperature does
    max_tokens=200
)
```

---

## 5. The Context Window

The **context window** is the maximum number of tokens the model can process at once — everything it can "see" when generating the next token.

Think of it like short-term memory. The model can only see what's inside the window. Anything outside it is invisible.

```
Context window = 8,000 tokens
                 ┌────────────────────────────────────────┐
Your chat so far:│ Message 1 | Message 2 | ... | Message N│← fits inside
                 └────────────────────────────────────────┘

Add more messages:
                 ┌────────────────────────────────────────┐
                 │ (older messages fall off the left) ... │
                 └────────────────────────────────────────┘
```

### Context window sizes across models

| Model | Context window | Roughly equivalent to |
|-------|---------------|----------------------|
| GPT-3 (original) | 4,096 tokens | ~3,000 words (a long essay) |
| GPT-4 Turbo | 128,000 tokens | ~96,000 words (a short novel) |
| Claude 3.5 | 200,000 tokens | ~150,000 words (a long novel) |
| Gemini 1.5 Pro | 1,000,000 tokens | ~750,000 words (several novels) |
| Llama 3.1 405B | 128,000 tokens | ~96,000 words |

### Why context windows matter

**What fits in context = what the model can reason about.** If you're asking it to summarise a 200-page document, you need a large enough context window to fit the whole document.

**Longer context ≠ better reasoning everywhere.** Models can "lose" information in very long contexts — they're better at using information near the beginning and end ("lost in the middle" problem).

**Cost scales with context length.** More tokens in = higher API cost.

---

## 6. How the Model "Remembers" a Conversation

Here's a common misconception: the model doesn't actually remember anything between messages.

Each time you send a message, the application sends the **entire conversation history** as a single input.

```
What you think is happening:
  Turn 1: "My name is Alice" → model remembers "Alice"
  Turn 2: "What's my name?"  → model looks up memory → "Alice"

What's actually happening:
  Turn 2 input:
  ┌─────────────────────────────────────────────────────────┐
  │ User: "My name is Alice"                                │
  │ Assistant: "Nice to meet you, Alice!"                   │
  │ User: "What's my name?"    ← the actual current input  │
  └─────────────────────────────────────────────────────────┘
  The model sees all of this at once and "finds" Alice in it.
```

This is why:
- Long conversations use more tokens and cost more
- If a conversation exceeds the context window, early messages get dropped and the model "forgets"
- The model has no memory between separate conversations unless you explicitly provide history

---

## 7. Training: Where the Knowledge Comes From

Before inference (generating text for you), the model goes through **training** — a process of reading enormous amounts of text and slowly adjusting its internal parameters.

```
Training data examples:
  "The mitochondria is the powerhouse of the cell."
  "def fibonacci(n): return n if n <= 1 else fibonacci(n-1) + fibonacci(n-2)"
  "Romeo and Juliet is a tragedy written by William Shakespeare."
  ... × billions
```

For each example, the model:
1. Tries to predict the next token
2. Checks if it was right
3. Adjusts its parameters slightly to do better next time

After training on enough text, the model has essentially **compressed the patterns of human language** into its parameters. It doesn't store the text — it stores the patterns.

### The scale of training data

| Model | Approximate training tokens |
|-------|----------------------------|
| GPT-2 | 40 billion |
| GPT-3 | 300 billion |
| Llama 3 | 15 trillion |
| GPT-4 | Undisclosed (estimated trillions) |

For context: the entire English Wikipedia is about 4 billion tokens — a rounding error in modern LLM training sets.

---

## 8. What the Model Actually Knows

This is one of the most important mental models to have:

**The model doesn't know facts. It knows patterns about how facts are expressed.**

```
You ask: "What is the boiling point of water?"

The model doesn't look this up. It has seen millions of sentences like:
"Water boils at 100°C at sea level."
"The boiling point of water is 212°F."
"H₂O has a boiling point of..."

It generates a response that matches those patterns → correct answer.

You ask: "What happened in the news today?"

The model has seen no sentence patterns about today (it was trained months ago).
It has to either say it doesn't know, or... make something up that sounds plausible.
→ This is hallucination.
```

The model's knowledge is:
- **Frozen at the training cutoff date** — it doesn't know about recent events
- **Shaped by what was in the training data** — if something was rarely written about, it knows less about it
- **Probabilistic, not factual** — it generates what sounds right, not what is right

---

## 9. Why Bigger Models Are Better (Usually)

More parameters = the model can store more complex patterns. There are consistent findings:

| Capability | Emerges at roughly... |
|------------|----------------------|
| Basic language | ~100M parameters |
| Following simple instructions | ~1B parameters |
| Multi-step reasoning | ~10B parameters |
| Complex coding, nuanced writing | ~70B+ parameters |
| Near-human performance on exams | ~100B+ parameters |

But bigger also means:
- More expensive to run
- Slower responses
- Higher memory requirements
- More energy consumption

The trend in 2024–2025 is **small models getting better** — Phi-3 (3.8B) and Gemma 2 (9B) achieve results that used to require 70B+ models.

---

## 10. 🧪 Try It Yourself

### Exercise 1: Count tokens
Go to [platform.openai.com/tokenizer](https://platform.openai.com/tokenizer) and paste in different kinds of text. Notice:
- How does English compare to Japanese or Arabic?
- How does code tokenise?
- Does capitalisation change the token count?

### Exercise 2: See temperature in action
Using any chat interface, ask for "a one-sentence description of the ocean" 5 times in a row. Notice the variation — that's temperature at work.

### Exercise 3: Hit the context limit
Start a conversation with an LLM. Paste in a long document, then ask questions about the beginning of it. In most chat interfaces this works fine. Now paste an extremely long document and ask about something near the start — if it answers incorrectly, the early content may have "scrolled off" the context window.

### Exercise 4: Probe the knowledge cutoff
Ask an LLM what happened in the news "this week." It should either say it doesn't know, or hallucinate. Then ask about a historical event from 10 years ago — it should answer confidently.

---

## Key Takeaways

- ✅ LLMs work by **predicting the next token**, one at a time, based on everything before it
- ✅ **Tokens** are chunks of text (~0.75 words each) — the basic unit of processing and cost
- ✅ **Embeddings** convert tokens into vectors that capture meaning and relationships
- ✅ The **context window** is the model's short-term memory — it can only see what's inside it
- ✅ The model has **no persistent memory** — conversation history is re-sent every turn
- ✅ Knowledge is **frozen at the training cutoff** — the model generates patterns, not facts

---

## 📚 Go Deeper

- [Let's Build GPT from Scratch — Andrej Karpathy (YouTube)](https://www.youtube.com/watch?v=kCc8FmEb1nY) — builds an LLM from scratch in Python, surprisingly accessible
- [Tiktokenizer — visualise tokenisation](https://tiktokenizer.vercel.app/) — interactive tokeniser
- [The Illustrated GPT-2 — Jay Alammar](https://jalammar.github.io/illustrated-gpt2/) — visual walkthrough of GPT internals

---

*← Previous: [01 — What Is Generative AI](./01_What_Is_Generative_AI.md)*  
*Next → [03 — Transformers 101](./03_Transformers_101.md)*
