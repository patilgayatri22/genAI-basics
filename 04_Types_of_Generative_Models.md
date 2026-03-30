# 🗂️ Types of Generative Models

> **Basics Module 04** | ⏱️ 20–25 min read | 🟢 Beginner  
> Prerequisites: [01 — What Is Generative AI](./01_What_Is_Generative_AI.md)

---

## 🎯 What You'll Learn

- The five main categories of generative models
- What each type creates, how it works intuitively, and what it's used for
- The leading models in each category (as of 2025)
- How multimodal models combine multiple types
- A decision guide: which type fits which task

---

## The Big Picture

Generative AI is not one thing. It's a family of model types, each trained to generate a different kind of output.

```
                    GENERATIVE AI
                         │
       ┌─────────────────┼──────────────────┐
       │                 │                  │
  Language            Vision             Audio
  (Text / Code)       (Image / Video)    (Speech / Music)
       │                 │                  │
    LLMs             Diffusion          Speech models
    Code models      GANs               Music models
    Summarisers      VAEs               Audio LLMs
       │
       └── Multimodal models (combine two or more)
```

---

## 1. Large Language Models (LLMs)

### What they create
Text — in any form. Paragraphs, bullet points, code, poems, SQL, JSON, dialogue, summaries.

### How they work (intuitively)
Trained on billions of pages of text, LLMs learn the patterns of human language. When you give them a prompt, they predict the most likely continuation — one token at a time — until they've completed a response.

*(Covered in depth in [Module 02](./02_How_LLMs_Work.md) and [Module 03](./03_Transformers_101.md))*

### Key use cases

| Use Case | Example |
|----------|---------|
| Question answering | "Explain inflation in simple terms" |
| Summarisation | Paste a 20-page report, get a 3-paragraph summary |
| Writing assistance | Draft emails, blog posts, reports |
| Code generation | "Write a Python function that reverses a string" |
| Translation | Translate between 100+ languages |
| Data extraction | "Extract all dates and names from this contract" |
| Reasoning | "What are the pros and cons of this decision?" |
| Chat / assistants | Customer support bots, tutors, companions |

### Leading models (2025)

| Model | Creator | Open? | Strengths |
|-------|---------|-------|-----------|
| GPT-4o | OpenAI | ❌ | Multimodal, fast, very capable |
| Claude 3.5 / 4 | Anthropic | ❌ | Long context, safety, coding |
| Gemini 1.5 Pro | Google | ❌ | 1M context, multimodal |
| Llama 3.1 405B | Meta | ✅ | Best open-weights model |
| Mistral Large | Mistral AI | ✅ | Efficient, multilingual |
| Phi-3 / Phi-4 | Microsoft | ✅ | Small, surprisingly capable |
| Qwen 2.5 | Alibaba | ✅ | Strong multilingual + code |

### A quick analogy
An LLM is like a person who has read everything ever published and can write fluently in any style, on any topic — but has no real-time knowledge and sometimes confabulates.

---

## 2. Image Generation Models

### What they create
Images — photorealistic photos, illustrations, paintings, logos, icons, concept art.

### How they work (intuitively)

There are three main architectures. You don't need to know which one powers which product — just the intuition of each.

#### Diffusion Models (current dominant approach)
Start with a completely noisy image (pure static). Gradually remove the noise, step by step, guided by a text description. After 20–50 steps, noise becomes a coherent image.

```
Text: "a red fox in a snowy forest"

Step 1:  [pure random noise 🌫️]
Step 10: [vague warm shape emerging 🌫️→🟠]
Step 30: [fox silhouette visible 🦊]
Step 50: [detailed fox in snow ✅]
```

#### GANs (Generative Adversarial Networks) — older approach
Two networks compete: a **generator** makes fake images, a **discriminator** tries to spot fakes. The competition drives quality up. Used in StyleGAN for ultra-realistic faces.

#### VAEs (Variational Autoencoders)
Compress images into a compact "meaning vector" (latent space), then reconstruct. Used as a component inside Stable Diffusion to work in compressed space.

### Key use cases

| Use Case | Example |
|----------|---------|
| Creative art | Concept art, illustrations, digital painting |
| Product design | Mockups, logo variations, packaging |
| Marketing | Ad visuals, social media images |
| Photo editing | Remove backgrounds, change styles, in-painting |
| Architecture | Visualise buildings before they're built |
| Game development | Character concepts, environment art |

### Leading models (2025)

| Model | Creator | Open? | Strengths |
|-------|---------|-------|-----------|
| DALL·E 3 | OpenAI | ❌ | Best prompt adherence |
| Midjourney v6 | Midjourney | ❌ | Stunning aesthetic quality |
| Stable Diffusion 3 | Stability AI | ✅ | Open, customisable |
| Adobe Firefly | Adobe | ❌ | Commercial-safe images |
| FLUX | Black Forest Labs | ✅ | High quality, fast |
| Imagen 3 | Google | ❌ | Photorealistic |

### A quick analogy
An image model is like a world-class illustrator who has studied every image ever created and can paint anything you describe — in any style, at any resolution.

---

## 3. Audio Models

Audio generation covers two distinct domains: **speech** (human voice) and **music/sound**.

### 3a. Speech Models

**Speech-to-Text (STT)**  
Convert spoken audio into written text.

```
Audio recording of a meeting → Transcript with speaker labels and timestamps
```

**Text-to-Speech (TTS)**  
Convert written text into spoken audio — with natural rhythm, emotion, and voice.

```
"Hello, welcome to our service." → Natural-sounding voice audio clip
```

**Voice Cloning**  
Reproduce a specific person's voice from a few seconds of audio sample.

### Leading speech models (2025)

| Model | Creator | Type | Strengths |
|-------|---------|------|-----------|
| Whisper | OpenAI | STT | 99 languages, open source |
| OpenAI TTS | OpenAI | TTS | Natural voices (Alloy, Nova, etc.) |
| ElevenLabs | ElevenLabs | TTS + cloning | Ultra-realistic voice cloning |
| Kokoro | Various | TTS | Open-source, lightweight |
| AssemblyAI | AssemblyAI | STT | Enterprise, speaker diarisation |

### 3b. Music / Sound Models

Generate original music, sound effects, or audio from text descriptions.

```
"An upbeat jazz piano piece with light brushed drums, 120 BPM"
→ A unique 30-second audio clip
```

### Leading music models (2025)

| Model | Creator | Strengths |
|-------|---------|-----------|
| Suno | Suno | Full songs with lyrics + voice |
| Udio | Udio | High-quality music generation |
| MusicGen | Meta | Open-source music generation |
| AudioCraft | Meta | Sound effects + music |

### A quick analogy
Speech models are like a universal translator + world-class voice actor. Music models are like a composer who can write any genre on demand.

---

## 4. Video Generation Models

### What they create
Video clips — from text descriptions, from images, or from existing video with modifications.

### How they work (intuitively)
Similar to image diffusion, but extended through time. The model must generate frames that are both visually coherent AND consistent across time (so objects don't flicker or teleport).

This is significantly harder than images — which is why video generation only became impressive in 2024.

```
Text: "a timelapse of a flower blooming in morning light"

→ A 5-second video clip that was never filmed
```

### Key use cases

| Use Case | Description |
|----------|-------------|
| Marketing videos | Product demos, ads from simple descriptions |
| Concept visualisation | Show how a product or building will look |
| Educational content | Animated explainers |
| Film pre-visualisation | Rough cuts before expensive production |
| Social media | Short-form video at scale |

### Leading models (2025)

| Model | Creator | Strengths |
|-------|---------|-----------|
| Sora | OpenAI | Long, coherent, cinematic |
| Runway Gen-3 | Runway | Fast, professional quality |
| Kling | Kuaishou | Realistic motion, longer clips |
| Veo 2 | Google | High resolution, good physics |
| Stable Video Diffusion | Stability AI | Open source |
| Pika | Pika Labs | Easy to use, fast |

### Current limitations
Video generation is impressive but still has notable weaknesses:
- Physics and motion can look unnatural (water, fire, hands)
- Maximum clip length is typically under 60 seconds
- Consistency across very long videos breaks down
- Faces and text can distort

### A quick analogy
Video models are like a film director who can visualise and render any scene you describe — though sometimes the physics and human anatomy get a bit surreal.

---

## 5. Code Generation Models

### What they create
Source code in any programming language — functions, classes, scripts, tests, documentation.

### How they work
Code models are LLMs fine-tuned specifically on code repositories (GitHub, Stack Overflow, documentation). They understand programming syntax, logic, and common patterns.

```
Prompt: "Write a function that checks if a string is a palindrome"

Output:
def is_palindrome(s: str) -> bool:
    """Check if string is a palindrome, ignoring case and spaces."""
    cleaned = s.lower().replace(" ", "")
    return cleaned == cleaned[::-1]
```

### Key use cases

| Use Case | Description |
|----------|-------------|
| Code completion | Auto-complete as you type |
| Code generation | Write functions from docstrings or comments |
| Code review | Spot bugs, suggest improvements |
| Refactoring | Improve existing code structure |
| Test generation | Write unit tests for your functions |
| Documentation | Generate docstrings and README files |
| Bug fixing | "Why doesn't this work? Fix it." |
| Explanation | "Explain what this code does" |

### Leading models (2025)

| Model | Creator | Open? | Strengths |
|-------|---------|-------|-----------|
| GitHub Copilot | GitHub/OpenAI | ❌ | IDE integration, context-aware |
| Claude 3.5 Sonnet | Anthropic | ❌ | Strongest for complex code |
| GPT-4o | OpenAI | ❌ | Broad language support |
| Cursor | Cursor | ❌ | Full codebase context |
| CodeLlama | Meta | ✅ | Open-weights, strong performance |
| DeepSeek Coder | DeepSeek | ✅ | Competitive open-source |
| StarCoder 2 | HuggingFace | ✅ | Open, well-documented |

---

## 6. Multimodal Models

### What they are
Models that work with **more than one type of input or output** simultaneously.

```
Multimodal combinations:
  Text + Image → Text    (visual question answering)
  Text → Image + Text    (image generation with explanation)
  Text + Audio → Text    (audio transcription + analysis)
  Image + Text → Code    (sketch-to-code)
  Video + Text → Text    (video summarisation)
```

### Why multimodal matters
The real world is multimodal. A photo has text in it. A meeting has audio AND visual slides. A document has charts AND prose. Multimodal models handle this naturally.

### Key use cases

| Use Case | Input | Output |
|----------|-------|--------|
| Visual Q&A | Image + question | Answer |
| Document analysis | PDF / screenshot | Summary or extracted data |
| Code from screenshot | UI screenshot | HTML/CSS code |
| Chart understanding | Chart image | Data explanation |
| Meeting notes | Audio + slides | Summary + action items |
| Accessibility | Image | Detailed description |

### Leading multimodal models (2025)

| Model | Creator | Modalities |
|-------|---------|-----------|
| GPT-4o | OpenAI | Text + Image + Audio |
| Claude 3.5 / 4 | Anthropic | Text + Image |
| Gemini 1.5 Pro | Google | Text + Image + Audio + Video |
| Llava 1.6 | Open source | Text + Image |
| Phi-3 Vision | Microsoft | Text + Image |

---

## 7. How to Choose: A Decision Guide

```
What do you need to generate?
│
├── Text, code, structured data
│     └── Use an LLM
│           ├── Need coding focus?      → CodeLlama, Copilot, DeepSeek Coder
│           ├── Need long context?      → Claude, Gemini 1.5 Pro
│           ├── Need open source?       → Llama 3, Mistral, Qwen
│           └── General purpose?       → GPT-4o, Claude Sonnet
│
├── Images
│     └── Use an image model
│           ├── Need commercial rights? → DALL·E 3, Adobe Firefly
│           ├── Need artistic quality?  → Midjourney
│           ├── Need full control?      → Stable Diffusion (open source)
│           └── Need speed?            → FLUX
│
├── Audio
│     ├── Speech → text?               → Whisper, AssemblyAI
│     ├── Text → speech?               → OpenAI TTS, ElevenLabs
│     └── Music?                       → Suno, Udio
│
├── Video
│     └── Use a video model
│           ├── Need quality?          → Sora, Veo 2
│           ├── Need speed?            → Pika, Runway
│           └── Need open source?      → Stable Video Diffusion
│
└── Mix of modalities (image + text input, etc.)
      └── Use a multimodal model
            ├── Analyse documents?     → Claude, GPT-4o
            ├── Analyse video?         → Gemini 1.5 Pro
            └── General purpose?      → GPT-4o, Claude
```

---

## 8. 🧪 Try It Yourself

### Exercise 1: LLM
Ask Claude or ChatGPT to:
- Summarise a Wikipedia article you paste in
- Write a short Python function
- Translate a sentence into 3 languages

### Exercise 2: Image generation
Go to [DALL·E](https://labs.openai.com) or [Stable Diffusion online](https://stablediffusionweb.com):
- Generate "a photorealistic image of an astronaut drinking coffee on Mars"
- Generate the same prompt but add "in the style of a watercolour painting"
- Notice how changing the style words changes the output

### Exercise 3: Speech transcription
Record yourself saying something on your phone. Upload it to [OpenAI Whisper demo](https://huggingface.co/openai/whisper-large-v3) or use the API. Notice the accuracy.

### Exercise 4: Multimodal
Take a screenshot of any chart or graph. Upload it to Claude or GPT-4o and ask: "What does this chart show? What are the key trends?"

---

## Key Takeaways

- ✅ Generative AI is not one model — it's a **family**: LLMs, image, audio, video, code, multimodal
- ✅ Each type is trained differently and excels at different tasks
- ✅ **Diffusion models** (not GANs) are now the dominant approach for image and video
- ✅ **Speech models** have two directions: speech-to-text (Whisper) and text-to-speech (TTS)
- ✅ **Multimodal models** are becoming the standard — the future is handling text + image + audio together
- ✅ Choose your model type based on **what you need to create**, not just brand name

---

## 📚 Go Deeper

- [Hugging Face — Explore all model types](https://huggingface.co/models) — browse thousands of open models by task
- [Papers With Code — State of the art](https://paperswithcode.com/sota) — which models lead each benchmark
- [Sora technical report — OpenAI](https://openai.com/research/video-generation-models-as-world-simulators) — video generation explained
- [Whisper paper — Robust Speech Recognition](https://arxiv.org/abs/2212.04356)

---

*← Previous: [03 — Transformers 101](./03_Transformers_101.md)*  
*Next → [05 — Training vs Inference](./05_Training_vs_Inference.md)*
