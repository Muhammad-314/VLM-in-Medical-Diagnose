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

-ROUGE-1 could give a strong overlap score even though the model reversed the finding.
ROUGE also struggles to determine whether an omitted finding is clinically important or whether an included finding is supported by the image.

Remember: ROUGE helps evaluate content overlap and coverage, but coverage is not the same as correctness.