# Failure Analysis — British Airways Sentiment Classification

## Overview
54 out of 180 total predictions were incorrect (30.0% failure rate).
Cardiff failed on 10 examples per strategy, Siebert on 8 per strategy.
Failures were identical across all three strategies per model, confirming that
prompt format had zero effect on classification — these encoder-based models
classify on text semantics alone, not prompt structure.

---

## Pattern 1: Mixed Label Consistently Misclassified

Mixed was the hardest class for both models — misclassified 12 times as Negative
and 9 times as Positive across all runs.

Example ID 1 (Gatwick to Venice) was consistently called Negative despite being
Mixed. The review praised crew and check-in but criticized BA's cabin bag policy.
The negative complaint carried stronger semantic weight than the positive aspects,
pulling both models toward Negative.

Example ID 9 (Heathrow to Cape Town) was consistently called Positive despite
being Mixed. The glowing praise for cabin crew dominated over the criticism of
the aging aircraft. Both models weight emotionally vivid language over balanced
overall assessment, making Mixed the most difficult label to predict correctly.

---

## Pattern 2: Positive Reviews with Embedded Complaints Misclassified

15 Positive reviews were predicted as Negative across all runs.

Example ID 17 (flight to Pisa) was labeled Positive by the human annotator
because the passenger flew without major incident and the tone was matter-of-fact.
However both models returned Negative due to phrases like "poor economy product",
"seats were really tight", and "had to pay for food." This shows both models
react to the presence of negative keywords regardless of the reviewer's overall
conclusion, a known limitation of sentiment models trained on social media data
where negative phrases almost always indicate negative sentiment.

---

## Pattern 3: Easy Tier Had the Highest Failure Rate

Counterintuitively, the easy tier had a higher failure rate (45/120 = 37.5%)
than the really_difficult tier (6/30 = 20.0%) and hard tier (3/30 = 10.0%).

Many easy-tier reviews that were labeled Positive contained minor complaints
phrased in negative language, which triggered misclassification. Longer hard
and really_difficult reviews gave models more semantic context to work with,
resulting in better overall performance on those tiers.

---

## Pattern 4: Example ID 21 Wrong Across All 6 Runs

Review 21 described a birthday business class trip labeled Negative. Both models
called it Positive across every strategy (3 strategies × 2 models = 6 runs).
The review opened with enthusiastic language about the occasion and the upgrade,
and both models anchored on that early positive framing rather than the critical
conclusion. This is a known limitation of models that do not perform positional
weighting — early positive sentiment in a long review can override a negative ending.

---

## Pattern 5: Prompting Strategy Had Zero Effect on Output

Accuracy was identical across zero_shot, few_shot, and cot for both models
(Cardiff always 66.7%, Siebert always 73.3%). This confirms that Cardiff and
Siebert are encoder-based classification models, not generative models. They
ignore prompt framing entirely and classify based solely on input text semantics.
This is a fundamental architectural difference from GPT or Gemini-style models
where prompting strategy significantly impacts output quality and format.

---

## Summary Table

| Model | Strategy | Failures | Accuracy |
|-------|----------|----------|----------|
| cardiff | zero_shot | 10/30 | 66.7% |
| cardiff | few_shot | 10/30 | 66.7% |
| cardiff | cot | 10/30 | 66.7% |
| siebert | zero_shot | 8/30 | 73.3% |
| siebert | few_shot | 8/30 | 73.3% |
| siebert | cot | 8/30 | 73.3% |

| Difficulty Tier | Failures | Total | Failure Rate |
|-----------------|----------|-------|--------------|
| easy | 45 | 120 | 37.5% |
| hard | 3 | 30 | 10.0% |
| really_difficult | 6 | 30 | 20.0% |
