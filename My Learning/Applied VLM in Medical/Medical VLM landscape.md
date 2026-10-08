# Medical Image Classification

Problem: Given a medical image: Predict a label.

```text
Chest X-ray
     ↓
Model
     ↓
┌─────────────────┐
│ Pneumonia: YES  │
└─────────────────┘
```

---

# Medical VQA

VQA = Visual Question Answering.
The model receives:

```text
IMAGE
+
QUESTION

Produces Answer
```

```text
Chest X-ray
      +
"What abnormality is visible?"
      ↓
"Right lower-lobe opacity."
```

---

# Medical Report Generation

Ask the model to produce an entire report.

```text
Chest X-ray
     ↓
Medical VLM
     ↓
Radiology report

FINDINGS:
There is a focal opacity in the right lower
lung field...

IMPRESSION:
Findings are suggestive of...
```

It also raises much more serious issues around:
- hallucination
- omission
- factual consistency
- clinical terminology
- uncertainty

---

# Image-Text Retrieval

The goal is to find the most relevant image for a piece of text, or vice versa.

This can be extremely useful for:
- medical image search
- dataset construction
- clinical education
- case retrieval
- research

```text
Medical Image Encoder
        ↕
Shared Embedding Space
        ↕
Medical Text Encoder
```

---

# Diagnosis

Given the patient's information and the medical image, what is the likely diagnosis ?

```text
Patient history
       +
Symptoms
       +
Lab results
       +
Medical image
       ↓
Medical VLM
       ↓
Possible diagnosis
```

---

# Prognosis

We're asking about future outcomes, rather than the current diagnosis.

Examples:
- likelihood of disease progression
- treatment response
- survival outcome
- recurrence risk
- future complications

---

# Multimodal Reasoning

```text
Patient:
65-year-old male

History:
Smoking, chronic cough

Lab:
Elevated inflammatory markers

Image:
Chest X-ray

Question:
"What finding is most concerning,
and how does it relate to the clinical history?"
```

```text
                ┌───────────────┐
                │ Medical Image │
                └───────┬───────┘
                        │
                        ▼
                Visual information

Clinical history ───────┐
Symptoms ───────────────┤
Lab results ────────────┤
Medications ────────────┤
Prior reports ──────────┤
                        ▼
                Multimodal Model
                        │
                        ▼
                    Reasoning
                        │
                        ▼
                    Response
```

---

Medical VLM research therefore cares heavily about:
Hallucination
The model generates something that isn't actually supported by the image.

Grounding
Can we identify where in the image the evidence came from?

Factuality
Does the generated report accurately reflect the image?

Clinical validity
Does the output make medical sense?

Calibration
Does the model know when it is uncertain?

Generalization
Does it work across:
- hospitals
- scanners
- demographics
- modalities
- diseases
- populations?