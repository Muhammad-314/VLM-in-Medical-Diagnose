# Bootstrapping Language-Image Pre-training (BLIP) — Basics

CLIP is very good at saying whether an image and text belong together. But real vision-language tasks require richer interaction between vision and language. That's the job of BLIP.

---

## ITC — Image-Text Contrastive Learning

Same idea as CLIP.

## ITM — Image-Text Matching

Model tries to figure out does image and text actually match.

```text
        IMAGE
          │
          ▼
    Vision Encoder
          │
          │
          ├──────────────┐
          │              │
          ▼              ▼
       Visual         Text
      Features       Tokens
          │              │
          └──────┬───────┘
                 ▼
          Multimodal
          interaction
                 │
                 ▼
            MATCH / NO MATCH
```

## ITG — Image-grounded Text Generation

Suppose we give image of 👨 🚲 with prompt "Describe the image."
So the model needs a language-generation component.

```text
                 IMAGE
                   │
                   ▼
             Vision Encoder
                   │
                   ▼
            Visual Features
                   │
                   ▼
        Vision-Language Model
                   │
                   ▼
          Autoregressive Decoder
                   │
                   ▼
     "A man is riding a bicycle."
```

---

So BLIP has 3 parts in it, Are these related (ITC), Do they specifically match? (ITM), & Given this image, generate language. (ITG)

### Mental Model

```text
                 IMAGE
                   │
                   ▼
            Vision Encoder
                   │
                   ▼
            Visual Features
                   │
                   ▼
        ┌─────────────────────┐
        │ Vision-Language     │
        │     Transformer     │
        └─────────┬───────────┘
                  │
                  ▼
            Text Decoder
                  │
                  ▼
              Language
```

```text
IMAGE
  │
  ▼
Vision Encoder
  │
  ▼
Visual representation
  │
  ├───────────────┐
  │               │
  ▼               ▼
Contrastive    Multimodal
alignment      interaction
  │               │
  │               ▼
  │          Text generation
  │               │
  └───────────────┴──→ Language
```
