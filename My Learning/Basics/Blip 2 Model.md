# Bootstrapping Language-Image Pre-training 2 (BLIP 2) — Basics

We already have a powerful vision encoder and a powerful LLM. How do we connect them without retraining both from scratch?”
Vision Encoder represents - visual patterns, objects, spatial relationships etc.
While LLM embedding space represent - language, syntax, semantics, reasoning etc.
How we do connect them - Put a lightweight learned bridge between them.

---

```text
                 IMAGE
                   │
                   ▼
        ┌───────────────────┐
        │ Frozen Vision     │
        │ Encoder           │
        └─────────┬─────────┘
                  │
                  ▼
          Visual Features
                  │
                  ▼
        ┌───────────────────┐
        │     Q-Former      │
        │   (trainable)     │
        └─────────┬─────────┘
                  │
                  ▼
        Visual representation
                  │
                  ▼
        ┌───────────────────┐
        │ Frozen LLM        │
        └─────────┬─────────┘
                  │
                  ▼
                TEXT
```

---

Q former is Querying Transformer, it's task is extract visual info that is useful for the language model.
Instead of passing every raw vision feature directly into the LLM, BLIP-2 introduces a small Transformer containing a set of learnable query vectors.
These aren't visual patches, they're learned vectors whose job is to ask the vision encoder for useful information.

```text
             Vision Encoder
                  │
        ┌─────────┴──────────┐
        │                    │
        ▼                    ▼
     visual               visual
    features              features
        │                    │
        └─────────┬──────────┘
                  │
             ┌────▼────┐
             │ Q-Former│
             └────┬────┘
                  │
             useful visual
              information
                  │
                  ▼
                 LLM
```

---

```text
Learnable queries
Q₁ Q₂ Q₃ ... Q₃₂
       │
       │ cross-attention
       ▼
Vision features
V₁ V₂ V₃ ... Vₙ
```

---

BLIP-2 therefore keeps the large components frozen.

```text
Vision Encoder
     ❄️
   FROZEN

      ↓

Q-Former
     🔥
  TRAINABLE

      ↓

Projection
     🔥
  TRAINABLE

      ↓

LLM
     ❄️
   FROZEN
```

---

After the Q-Former extracts useful visual representations, they are projected into the LLM's expected embedding dimension.

```text
                     IMAGE
                       │
                       ▼
              ┌─────────────────┐
              │ Vision Encoder  │
              │    FROZEN       │
              └────────┬────────┘
                       │
                       ▼
                 Visual Features
                       │
                       ▼
              ┌─────────────────┐
              │    Q-Former     │
              │    TRAINABLE    │
              └────────┬────────┘
                       │
                       ▼
              Compact visual features
                       │
                       ▼
              Linear Projection
                       │
                       ▼
              LLM-compatible
               representations
                       │
                       ▼
              ┌─────────────────┐
              │      LLM        │
              │     FROZEN      │
              └────────┬────────┘
                       │
                       ▼
                  Text Answer
```

---

## Example

Suppose the image is:
🧑‍🍳 A chef holding a pizza.
And the prompt is:
"What is the person holding?"

Raw pixels: 224 × 224 × 3
Vision encoder Produces visual features: V₁, V₂, ..., Vₙ
Q-Former The learnable queries attend to these features. It extracts a compact representation containing useful visual information.
```text
V₁...Vₙ
   ↓
Q-Former
   ↓
Q₁...Q₃₂
```

Projection Those representations are transformed into the LLM's expected dimension.
Q₁...Q₃₂ -> projecter -> LLM-compatible visual embeddings

The LLM receives the visual representations together with:
"What is the person holding?"
It can then generate:
"The person is holding a pizza."

---

```text
                 IMAGE
                   │
                   ▼
        ┌─────────────────────┐
        │  Vision Encoder     │
        │     FROZEN          │
        └──────────┬──────────┘
                   │
                   ▼
            Visual Features
                   │
                   │
          ┌────────▼────────┐
          │    Q-Former     │
          │    TRAINABLE    │
          │                 │
          │ Learnable       │
          │ Queries +       │
          │ Cross-Attention │
          └────────┬────────┘
                   │
                   ▼
          Compact Visual Tokens
                   │
                   ▼
             Projection
                   │
                   ▼
        LLM-compatible embeddings
                   │
                   ▼
        ┌─────────────────────┐
        │        LLM          │
        │       FROZEN        │
        └──────────┬──────────┘
                   │
                   ▼
               RESPONSE
```
