# 💰 Training vs Inference

> **Basics Module 05** | ⏱️ 20–25 min read | 🟢 Beginner  
> Prerequisites: [02 — How LLMs Work](./02_How_LLMs_Work.md)

---

## 🎯 What You'll Learn

- The difference between training and inference — and why it matters
- The three phases of building an LLM: pretraining, fine-tuning, alignment
- What each phase costs in time, data, and money
- Why running a model (inference) is different from building one (training)
- How to estimate the cost of using GenAI in your own projects
- The landscape of who pays for what

---

## 1. The Core Distinction

These two words get confused constantly. Here's the clean split:

| | Training | Inference |
|-|----------|-----------|
| **What it is** | Teaching the model by adjusting its weights | Using the trained model to generate outputs |
| **Who does it** | AI labs (OpenAI, Anthropic, Meta, Google) | Everyone — you, apps, businesses |
| **When it happens** | Once (or periodically) | Every time someone sends a prompt |
| **Cost** | Millions of dollars | Fractions of a cent per query |
| **Hardware** | Thousands of GPUs for weeks | Fewer GPUs, but constantly busy |
| **Output** | A trained model file (billions of weights) | Text, images, code, audio |

**Analogy:**  
Training is like writing and printing a textbook. Inference is like a student reading that textbook to answer a question. Writing the book is hard and expensive. Reading it is cheap and fast — but the book doesn't change.

---

## 2. Phase 1: Pretraining

This is how a model gets its foundational knowledge. It's the most expensive phase by far.

### What happens

The model is shown an enormous amount of text — billions of documents from the internet, books, code, Wikipedia, scientific papers — and trained to predict the next token in each sequence.

```
Training example:
  Input:  "The mitochondria is the powerhouse of"
  Target: "the"   ← predict this
  
  Input:  "The mitochondria is the powerhouse of the"
  Target: "cell"  ← predict this
  
  ...repeated billions of times across trillions of tokens
```

Every wrong prediction slightly adjusts the model's billions of parameters. After enough iterations, those parameters encode the patterns of language — grammar, facts, reasoning styles, code syntax, and much more.

### The data

| Source | Typical share |
|--------|--------------|
| Web crawl (Common Crawl) | 40–60% |
| Books | 10–20% |
| Wikipedia | 3–5% |
| Code (GitHub, etc.) | 5–10% |
| Scientific papers (arXiv, etc.) | 2–5% |
| Other curated sources | Remainder |

The total? **Trillions of tokens.** For context, the entire English Wikipedia is ~4 billion tokens — a rounding error.

### The cost

| Model | Estimated training cost | Training tokens | Parameters |
|-------|------------------------|-----------------|------------|
| GPT-3 | ~$4–12 million | 300 billion | 175 billion |
| Llama 3 8B | ~$1–2 million | 15 trillion | 8 billion |
| Llama 3 405B | ~$30–50 million | 15 trillion | 405 billion |
| GPT-4 | ~$100–300 million* | Undisclosed | ~1.8 trillion* |

*Estimated; not confirmed by OpenAI

### The hardware

Pretraining requires thousands of specialised GPU/TPU chips running continuously for weeks or months.

```
Training Llama 3 405B:
  Hardware: ~16,000 NVIDIA H100 GPUs
  Duration: Weeks of continuous training
  Electricity: Megawatts of power consumption
  Storage: Petabytes of training data
```

**Very few organisations in the world can afford to pretrain frontier models.**  
That's why there are only a handful: OpenAI, Anthropic, Google, Meta, Mistral, xAI, Cohere.

---

## 3. Phase 2: Fine-Tuning

A pretrained model is like a student who has read everything ever written — but has never been told what format to respond in, how to be helpful, or how to follow instructions. Fine-tuning fixes this.

### What happens

The base pretrained model is further trained on a smaller, curated dataset of high-quality examples. This shapes its behaviour without forgetting its general knowledge.

```
Base model output (no fine-tuning):
  Prompt: "What is the capital of France?"
  Output: "capital of France Paris France capital city Europe Western..."
          ← just continues predicting tokens, not helpful

Fine-tuned model output:
  Prompt: "What is the capital of France?"
  Output: "The capital of France is Paris."
          ← follows instruction format, gives a clean answer
```

### Types of fine-tuning

**Supervised Fine-Tuning (SFT)**  
Train on thousands of (instruction, ideal response) pairs written by humans.

```
Example training pairs:
  Instruction: "Summarise this article in 3 bullet points."
  Response: "• Key point 1\n• Key point 2\n• Key point 3"
  
  Instruction: "Write a professional email declining this meeting."
  Response: "Dear [Name], Thank you for the invitation..."
```

**RLHF (Reinforcement Learning from Human Feedback)**  
Human raters compare pairs of model outputs and say which is better. A "reward model" is trained on these preferences and used to further tune the LLM to produce preferred outputs.

```
Same prompt, two outputs:
  Output A: "I can't help with that."  ← rated lower
  Output B: "Here are some ways to approach this problem..." ← rated higher

→ Model learns to produce more outputs like B
```

This is how ChatGPT, Claude, and most modern assistants are aligned to be helpful, honest, and follow instructions well.

### What fine-tuning costs

| Fine-tuning type | Rough cost | Data needed |
|-----------------|-----------|-------------|
| Full fine-tune (7B model) | $500–$2,000 | 10K–100K examples |
| LoRA fine-tune (7B model) | $50–$200 | 1K–10K examples |
| Full fine-tune (70B model) | $5,000–$20,000 | 50K+ examples |
| RLHF (any large model) | $10,000–$1M+ | Large human annotation budget |

Fine-tuning is **accessible** to most developers — especially with parameter-efficient methods like LoRA (Low-Rank Adaptation) that only train a small fraction of the model's weights.

---

## 4. Phase 3: Alignment & Safety Training

Beyond capability, modern models go through additional training to make them safe, honest, and in line with human values.

### Techniques used

**Constitutional AI (Anthropic):** The model is given a set of principles and trained to critique and revise its own outputs against them, without requiring constant human labelling.

**RLHF (OpenAI, Anthropic, others):** Human raters choose between model outputs; these preferences train a reward model that guides further tuning.

**DPO (Direct Preference Optimisation):** A simpler, more stable alternative to RLHF that directly optimises on preference pairs without needing a separate reward model.

These phases are what turn a raw, autocomplete-style base model into a helpful, honest, and (mostly) harmless assistant.

---

## 5. Inference: Using the Trained Model

Once training is done, the model weights are fixed. Inference is the process of running those fixed weights on new inputs.

```
Training:  adjust weights → adjust weights → adjust weights → ... (billions of times)
                                  ↓
Inference: fixed weights + your prompt → output
           fixed weights + next prompt → output
           fixed weights + next prompt → output
           (weights never change during inference)
```

### What inference looks like computationally

For each token generated:
1. Load the prompt tokens + previous output tokens into memory
2. Run them through all transformer layers (forward pass only)
3. Get probabilities for the next token
4. Sample one token
5. Repeat

There's no learning, no weight updates — just a fast matrix multiplication cascade through billions of numbers.

### Inference hardware

| Use case | Hardware |
|----------|----------|
| Small model (7B), personal use | 1× consumer GPU (RTX 4090, 8GB VRAM minimum) |
| Medium model (13B–30B) | 1–2× professional GPUs (A100 40GB) |
| Large model (70B) | 2–4× A100 80GB GPUs |
| Frontier model (GPT-4 scale) | 8–32× H100 GPUs per inference server |
| API calls to OpenAI/Anthropic | No hardware on your end — they handle it |

---

## 6. What Actually Costs Money: A Practical Guide

If you're building with GenAI, here's where money gets spent:

### For developers using APIs (most common case)

You pay **per token** — both the tokens you send (input) and the tokens the model generates (output).

```
Example pricing (approximate, as of 2025):

Model              Input per 1M tokens    Output per 1M tokens
─────────────────  ─────────────────────  ──────────────────────
GPT-4o             $2.50                  $10.00
GPT-4o-mini        $0.15                  $0.60
Claude 3.5 Sonnet  $3.00                  $15.00
Claude Haiku 3.5   $0.80                  $4.00
Gemini 1.5 Flash   $0.075                 $0.30
Llama 3 (via API)  $0.20                  $0.20  (varies by provider)
```

### Real-world cost examples

```
A simple chatbot answer (~500 input tokens, ~200 output tokens):
  GPT-4o:      (500/1M × $2.50) + (200/1M × $10.00) = $0.0013
  GPT-4o-mini: (500/1M × $0.15) + (200/1M × $0.60)  = $0.000195
  Haiku 3.5:   (500/1M × $0.80) + (200/1M × $4.00)  = $0.0012

Summarise a 10-page document (~5,000 input tokens, ~300 output tokens):
  GPT-4o:      $0.0155
  GPT-4o-mini: $0.00093

Process 1 million customer emails (avg 300 tokens each, 100 token response):
  GPT-4o:      ~$4,000
  GPT-4o-mini: ~$105  ← 38× cheaper for similar quality on simple tasks
```

### Cost optimisation strategies

| Strategy | Savings |
|----------|---------|
| Use a smaller model for simple tasks | 10–100× cheaper |
| Cache repeated queries | Eliminate cost for identical prompts |
| Reduce system prompt length | Counts as input tokens on every call |
| Summarise chat history instead of sending it all | Reduces context length |
| Batch requests | Often gets volume discounts |
| Use open-source models locally | Near-zero per-query cost after hardware |

---

## 7. Running Models Locally vs API

### API (cloud)

```
Pros:
  ✅ No hardware required
  ✅ Always the latest model
  ✅ Scales instantly to millions of users
  ✅ No maintenance

Cons:
  ❌ Per-token cost adds up at scale
  ❌ Data leaves your infrastructure
  ❌ Depends on internet + provider uptime
  ❌ Rate limits
```

### Local (on your own hardware)

```
Pros:
  ✅ No per-query cost after hardware
  ✅ Data stays on your machine (privacy)
  ✅ Works offline
  ✅ No rate limits

Cons:
  ❌ Upfront hardware cost ($1,000–$10,000+)
  ❌ Smaller/weaker models (can't run GPT-4 locally)
  ❌ Maintenance, updates
  ❌ Electricity costs
```

### Tools for running locally

| Tool | Best for |
|------|---------|
| [Ollama](https://ollama.com) | Easiest local LLM setup (Mac/Linux/Windows) |
| [LM Studio](https://lmstudio.ai) | User-friendly GUI, model downloads |
| [llama.cpp](https://github.com/ggerganov/llama.cpp) | Maximum performance, CPU-friendly |
| [Jan](https://jan.ai) | Open-source ChatGPT alternative, local |

```bash
# Running Llama 3.2 locally with Ollama (after installing Ollama):
ollama run llama3.2

# That's it. Free, private, no API key needed.
```

---

## 8. The Economics at Scale

To understand why this industry is structured the way it is:

```
Training frontier models:
  Cost: $50M–$500M per run
  Who can do this: OpenAI, Google, Anthropic, Meta, xAI
  Revenue model: API subscriptions, enterprise contracts

Inference at scale (e.g. ChatGPT):
  Cost: $0.001–$0.01 per conversation
  At 100M daily active users: $100,000–$1,000,000 per day in GPU costs
  Revenue needed: subscriptions ($20/mo) + API revenue

Building on top (your apps):
  Cost: API fees, which get passed to users or absorbed as cost
  Opportunity: the whole application layer is wide open
```

The trillion-dollar question everyone is betting on: **can inference costs drop fast enough** (through hardware improvements, model efficiency, and competition) to make GenAI profitable at consumer scale?

---

## 9. 🧪 Try It Yourself

### Exercise 1: Calculate your costs
Go to the [OpenAI pricing page](https://openai.com/pricing) and the [Anthropic pricing page](https://www.anthropic.com/pricing). Choose a use case you care about (e.g. summarising 100 documents per day). Calculate:
- How much would GPT-4o cost per month?
- How much would GPT-4o-mini cost?
- At what volume does it make sense to consider running a local model?

### Exercise 2: Token counting
Install `tiktoken`:
```bash
pip install tiktoken
```
Then count tokens in your own text:
```python
import tiktoken
enc = tiktoken.encoding_for_model("gpt-4o")
text = "Paste your text here..."
tokens = enc.encode(text)
print(f"Token count: {len(tokens)}")
print(f"Estimated GPT-4o cost: ${len(tokens)/1_000_000 * 2.50:.6f}")
```

### Exercise 3: Run a model locally
If you have a Mac or Linux machine with 8GB+ RAM:
1. Install [Ollama](https://ollama.com)
2. Run `ollama run llama3.2` in your terminal
3. Chat with it — completely free, completely local

### Exercise 4: Compare outputs
Ask the same question to:
- GPT-4o
- GPT-4o-mini
- Claude Haiku (via Claude.ai)
- Local Llama 3.2 (if you have it running)

Notice quality differences vs cost differences.

---

## Summary Table: The Three Phases

| Phase | What happens | Who does it | Cost | Output |
|-------|-------------|-------------|------|--------|
| **Pretraining** | Learn language from massive datasets | AI labs only | $1M–$500M | Base model weights |
| **Fine-tuning** | Shape behaviour with curated examples | AI labs + developers | $100–$100K | Instruction-following model |
| **Alignment** | RLHF, constitutional AI, DPO | AI labs | $10K–$1M+ | Safe, helpful assistant |
| **Inference** | Generate outputs for users | Everyone | $0.0001–$0.01 per call | Text, images, code, etc. |

---

## Key Takeaways

- ✅ **Training** creates the model; **inference** uses it — they are completely different operations
- ✅ **Pretraining** requires billions in compute — only a handful of labs do it
- ✅ **Fine-tuning** is accessible — you can do it for hundreds of dollars
- ✅ **You pay per token** when using APIs — input + output both count
- ✅ **Smaller models** are dramatically cheaper and often good enough for simple tasks
- ✅ **Local models** (Llama, Mistral, etc.) eliminate per-query cost but require upfront hardware

---

## 📚 Go Deeper

- [Epoch AI — AI training compute trends](https://epochai.org/research/trends-in-training-dataset-sizes) — data on how training scale has grown
- [Andrej Karpathy — "The State of GPT" (YouTube)](https://www.youtube.com/watch?v=bZQun8Y4L2A) — training pipeline explained clearly
- [LoRA paper — Low-Rank Adaptation](https://arxiv.org/abs/2106.09685) — how efficient fine-tuning works
- [Hugging Face pricing calculator](https://huggingface.co/pricing) — estimate costs for open models

---

*← Previous: [04 — Types of Generative Models](./04_Types_of_Generative_Models.md)*  
*Next → [06 — APIs and SDKs](./06_APIs_and_SDKs.md)*
