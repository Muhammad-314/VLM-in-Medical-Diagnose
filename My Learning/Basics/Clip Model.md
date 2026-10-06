# Contrastive Language Image Pre-Training (CLIP) — Basics

Teach an image encoder and a text encoder to produce representations where matching images and texts are close together, and non-matching ones are far apart.

---

## Mental Model

```text
                 ┌─────────────────┐
                 │     IMAGE       │
                 └────────┬────────┘
                          ↓
                   Image Encoder
                          ↓
                     Image Vector
                          │
                          │
                          │ similarity
                          │
                          ↓
                     Text Vector
                          ↑
                    Text Encoder
                          ↑
                 ┌────────┴────────┐
                 │ "A dog"        │
                 └────────────────┘

```

Text Encoder is a transformer, while for image encoder, it could be ViT or CNN.

---
