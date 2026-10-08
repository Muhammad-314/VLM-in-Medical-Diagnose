# Large Language and Vision Assistant (LLaVA) — Basics

LLaVA is similar to BLIP 2, except little simpler.

## Mental Model

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
                 Projector
                      │
                      ▼
           LLM-compatible embeddings
                      │
                      ▼
                    LLM
                      │
                      ▼
                 Text Output
```

Orignal LLaVA used CLIP vision encoder.
But Vision Encoder produces vision features [v₁, v₂, v₃, ..., vₙ], but the LLM expects representation in its own embedding space.
So they features are gone through Projector.

---

## Projector

```text
Visual Feature
      ↓
Linear Projection
      ↓
LLM Embedding Space
```

The image is represented as a sequence of continuous embeddings that can participate in the LLM's processing alongside text tokens.

---

## Multimodal instruction tuning

The model learns: Given this visual representation + this instruction, generate an appropriate answer.

```text
Image
 +
Instruction
 ↓
Desired response
```
