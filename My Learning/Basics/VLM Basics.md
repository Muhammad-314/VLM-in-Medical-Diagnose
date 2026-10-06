# Vision-Language Models (VLMs) — Basics

So Vision Language Models, have, text and images both as the data for training, & testing.
Text prompt as usual becomes text tokens, and ingested into VLM, while image input goes into vision encoder, extract patterns and edges and textures and spatial relationships, converting them into feature vectors.
Now, it is fed to projecter, which gives token based format embeddings. So text tokens & image tokens, together go into VLM.

---

## Mental Model

```text
                    IMAGE
                      │
                      ▼
              ┌──────────────┐
              │Vision Encoder│
              └──────┬───────┘
                     │
                     ▼
            Visual Features/Tokens
                     │
                     ▼
             Projector / Adapter
                     │
                     ▼
          LLM-compatible representations
                     │
                     ▼
TEXT ───────────────► LLM ◄──────────────┐
                     │                   │
                     │   Attention       │
                     │◄──────────────────┘
                     ▼
                  Answer

```

---

## How does an image become something an LLM can understand?

LLMs aren't inherently able to understand texts, at lowest level, LLM receive vectors only, so our task is how do we transform visual info into representations that can interact meaningfully with the LLM's learned language representations ?

Image pixels are given to vision encoder then to projecter or adapter then to llm, so the image is converted into learned numerical representations that the LLM can use through its attention mechanisms.

---

## Why can't you simply feed pixels into a text LLM?

LLM was trained on different input - tokens
The model's input space is therefore built around language tokens.

In two images, a same cat can appear shifted slightly or rotated or in different bg, or something else. So their raw pixels would be different, but semantically, they are the same cat.
So pixels are not good semantic representations.

---

## What is a vision encoder?

A vision encoder is a neural network that takes an image and converts it into a learned representation containing useful visual information. Vision encoder can be a CNN or a transformer (ViT)

---

### ViT

Suppose we have image of 224 x 224, then ViT divide into patches, say 16 x 16, so (224*224)/(16*16) = 196 patches
So each patches is converted to a vector. Say each vector is vi, so we get this sequence - [v₁, v₂, v₃, ..., v₁₉₆]
Transformers already know how to process sequences. So ViT applies Transformer-style self-attention to visual patches.

---

## What are visual tokens/features?

Visual Features is a learned numerical representation extracted from an image.
Visual Tokens is when, these visual representations are arranged as a sequence and fed to Transformer like system.

---

## What does alignment mean?

Suppose we want a text and it's image - related as in their representations.
Alignment training tried to make semantically corresponding things close.

```text
        IMAGE
          ↓
    Image Encoder
          ↓
     [image vector]
          ↘
           similarity ↑
          ↗
      [text vector]
          ↑
     Text Encoder
          ↑
        "cat"

So image of cat and word "cat", should be highly similar.

```

---

## Example

📷 Image of a dog catching a frisbee with text question - "What is the dog doing?"

```text
Image
 ↓
pixel values
```

Raw numerical information

```text
pixels
 ↓
ViT / CNN
 ↓
visual representations
```

The network extracts meaningful visual information.

[v₁, v₂, v₃, ..., vₙ]
These encode things about different visual regions and their context.

```text
visual features
      ↓
projector / adapter
      ↓
LLM-compatible representations
```

Depending on the architecture, those representations are transformed so that they can interact appropriately with language representations.

```text
[VIS₁] [VIS₂] ... [VISₙ]
[What] [is] [the] [dog] [doing] [?]
```

The Transformer can use attention to relate the question to the visual information.

```text
"What is the dog doing?"

             ↓

         "catching
          a frisbee"

```
