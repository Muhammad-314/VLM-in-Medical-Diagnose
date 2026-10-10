# Evaluation Methods

The key idea is this: imagine a model receives a chest X-ray and generates the following report:
“There is a right-sided pleural effusion and cardiomegaly. No pneumothorax is present.”

How do we determine whether this report is good?
There are several different questions we could ask:
- Does the generated report use wording similar to a reference report?
- Does it describe similar medical concepts?
- Does it correctly identify the clinical findings?
- Are its statements actually supported by the X-ray?
- Does it avoid inventing diseases or findings?

---

## Bilingual Evaluation Understudy (BLEU) — Does the wording match?

Imagine a image is the input to the model, and we compare its generated report with a human-written reference report.
Central idea is to measure how many word sequences in the generated text also appear in a reference text.

Suppose the reference report says:
“Small right pleural effusion is present.”

The model generates:
“There is a small effusion on the right side.”

Both statements may describe the same finding, but their wording differs.

BLEU checks overlapping sequences of words, called n-grams.
- Unigram: one word, such as effusion
- Bigram: two consecutive words, such as pleural effusion
- Trigram: three consecutive words, such as small pleural effusion

Why this will fail for my research project:
- The sentences below shared almost all their words. The important difference is the word “No”.
- BLEU can penalize this difference, but it does not understand the clinical importance of negation. More generally, word overlap alone does not tell us whether a finding is true.

Output A — Clinically correct
“No pneumothorax is present.”
Correct finding

Output B — Clinically incorrect
“Pneumothorax is present.”
Incorrect finding

Remember: BLEU measures lexical overlap, not medical truth.

---

## Recall-Oriented Understudy for Gisting Evaluation (ROGUE) - Does the generated report cover the same important words?

It was developed largely for evaluating text summarization.
ROUGE variants emphasize how much of the reference content is covered by the generated text.

Reference report:
“Mild cardiomegaly and a small left pleural effusion are present.”

Generated report:
“There is mild cardiomegaly.”

The generated report correctly mentions cardiomegaly but omits the pleural effusion.
ROUGE can detect that some of the reference's important words or sequences are missing.

ROUGE-1: Overlap of individual words.

ROUGE-2: Overlap of consecutive two-word sequences.

ROUGE-L: Overlap based on the longest common subsequence of words, which can capture similarities even when words are not all consecutive.

Why it can fail:
Reference: “No pleural effusion.”
Generated: “Pleural effusion.”

- ROUGE-1 could give a strong overlap score even though the model reversed the finding.
- ROUGE also struggles to determine whether an omitted finding is clinically important or whether an included finding is supported by the image.

Remember: ROUGE helps evaluate content overlap and coverage, but coverage is not the same as correctness.

---

## Bidirectional Encoder Representations from Transformers Score (BERTScore) — Does the text have similar meaning?

It uses contextual embeddings from a pretrained language model to compare words based on their representations in context.
The intuition is that two words or phrases can have similar meanings despite being written differently.
BERTScore is better positioned to recognize semantic similarity.

BERTScore typically computes precision, recall, and an F1 score from token-level embedding similarities.
- Precision: How well the generated tokens match reference tokens.
- Recall: How well the reference tokens are covered by generated tokens.
- F1: A balance between precision and recall.

The Problem?
Compare these statements:
1. “Pleural effusion is present.”
2. “No pleural effusion is present.”

- A semantic similarity metric may regard them as highly similar because most of the words and their contextual representations overlap.
- Yet their clinical meanings differ in a crucial way.
- Similarly, a model could generate a medically plausible diagnosis that is not visible in the image. BERTScore cannot reliably establish that the image supports that diagnosis.

Remember: BERTScore measures semantic similarity between texts, not whether the text is true or visually grounded.

---

## Chest eXpert (CheXpert) Labeler — Which clinical findings are mentioned?

The CheXpert Labeler is an NLP-based system designed to extract labels for specific chest X-ray findings from radiology reports.
Instead of comparing every word, it focuses on a predefined set of clinical observations.

Examples include:
- Cardiomegaly
- Pleural effusion
- Pneumothorax
- Edema
- Consolidation

A worked example
Reference report:
“No pleural effusion. Mild cardiomegaly is present.”

Generated report:
“Pleural effusion is present. Mild cardiomegaly is present.”

The CheXpert Labeler instead tries to extract their clinical labels:

| Finding | Reference | Generated |
| :--- | :--- | :--- |
| Pleural effusion | Absent | Present |
| Cardiomegaly | Present | Present |

- Then calculate label-level measures such as precision, recall, and F1, provided you define how uncertain and unmentioned labels are handled.

This is useful for hallucination research.
- We can use extracted labels to measure whether a model produces additional positive findings or reverses negative findings.
- But the CheXpert Labeler evaluates findings extracted from text. It does not independently inspect the X-ray to verify those findings.
- A reference report is useful, but it is not infallible: it may omit findings or disagree with the actual image.

---

## RadGraph — Are the medical entities and their relationships correct?

It represents information in radiology reports as entities and relationships.
An entity is a medical concept mentioned in a report. A relationship connects concepts to explain how they relate to one another.

For example:
“Small left pleural effusion.”

A simplified representation might contain:
- Entity: Pleural effusion
- Attribute: Left-sided
- Attribute: Small
- Relationship: The attributes describe the pleural effusion

Consider a more complicated sentence:
“No focal consolidation, but a small right pleural effusion is present.”

A structured representation should distinguish the absent consolidation from the present effusion and preserve the relationship between the effusion and its side and size.

How can we evaluate reports with RadGraph?
- Compare the entities and relationships extracted from the generated report with those from a reference report.
- So, A structure-aware comparison can reveal the disagreement more clearly than simply counting matching words.

RadGraph-based evaluation is particularly useful for examining:
- Incorrect or missing clinical findings.
- Incorrect relationships between findings and attributes.
- Differences in the structured medical information contained in reports.

The limitation
- RadGraph extracts and evaluates information from text. It does not, by itself, verify whether a pleural effusion actually appears in the image.
- A generated report can have perfectly structured entities and relationships while describing findings that are not present in the X-ray.

Remember: RadGraph helps assess the structure of clinical statements, not their visual truth.

---

## Clinical Correctness Score (CCS) — Is the report medically correct?

A Clinical Correctness Score may be defined by a research team, with clinicians assessing the medical accuracy and appropriateness of generated reports.

For example, a radiologist might examine an X-ray and a generated report and ask:
- Are the reported findings actually present?
- Are any important findings missing?
- Are the location, severity and other attributes correct?
- Does the report make an unsupported diagnostic claim?
- Is the interpretation clinically appropriate?

Suppose a model generates:
“Large right pleural effusion with associated pneumothorax.”

The radiologist examines the image and finds:
- A small right pleural effusion is present.
- No pneumothorax is visible.


The report has at least two major errors:
1. It exaggerates the severity of the effusion.
2. It introduces an unsupported pneumothorax finding.

A clinician can recognize both the visual disagreement and its clinical importance. This is a major advantage over BLEU, ROUGE and BERTScore.

What are the limitations? - Human evaluation has real costs:
- Qualified clinicians are difficult to recruit and may be expensive.
- Reviewers may disagree.
- Scoring can be subjective without a clear rubric.
- Evaluating large datasets takes time.

Remember: Clinical review can provide stronger evidence of medical correctness, but its reliability depends on the protocol and the quality of the reviewers.
