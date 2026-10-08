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
In CLIP Learning, correct text-image pair similarity score should be high, while their pair with different and incorrect pair should have less similarity. So CLIP learns cross-modal semantic alignment.

We have a batch of four image-text pairs:
```text
I₁ = dog image
I₂ = car image
I₃ = pizza image
I₄ = airplane image

T₁ = "a dog"
T₂ = "a car"
T₃ = "a pizza"
T₄ = "an airplane"
```

After encoding:
```text
Images:

I₁ → v₁
I₂ → v₂
I₃ → v₃
I₄ → v₄

Texts:

T₁ → t₁
T₂ → t₂
T₃ → t₃
T₄ → t₄
```

Now we can construct a matrix to calculate similarities:
```text
              Text
          T₁    T₂    T₃    T₄
       ┌────────────────────────
I₁     │ 0.95  0.12  0.08  0.17
I₂     │ 0.10  0.93  0.14  0.11
I₃     │ 0.07  0.16  0.96  0.09
I₄     │ 0.13  0.09  0.11  0.94
```

Diagonol values is what we want to increase (These are positive pairs, and everything else is a negative pair)

We calculate similarities, using cosine similarity.
CLIP uses a temperature-scaled contrastive loss, typically implemented as cross-entropy over the similarity matrix. So CLIP effectively performs image→text classification and text→image classification within each batch.

```text
                    CLIP
             ┌───────────────┐
Image ──────►│ Image Encoder │
             └───────┬───────┘
                     │
                     ▼
                 embedding
                     ↕
                 similarity
                     ↕
                 embedding
                     ▲
             ┌───────┴───────┐
Text ───────►│ Text Encoder  │
             └───────────────┘
```
