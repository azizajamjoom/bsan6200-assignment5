# Evaluation Memo: LM Selection for British Airways Sentiment Classification

**To:** British Airways Customer Intelligence Team  
**From:** Aziza Jamjoom  
**Re:** Recommended model for automated passenger review sentiment classification  

---

## Background

This memo summarizes the results of a systematic evaluation of two language models
across three prompting strategies on 30 manually labeled British Airways passenger
reviews. The goal was to identify the best model and strategy combination for
automating sentiment classification at scale.

---

## Task

Classify British Airways passenger reviews into one of three sentiment labels:
Positive, Negative, or Mixed. Reviews were sourced from a public dataset of
3,427 verified and unverified BA reviews and sampled across three complexity
tiers: 20 easy, 5 hard, and 5 really difficult examples.

---

## Models Evaluated

| Model | Full Name | Access | Cost/call |
|-------|-----------|--------|-----------|
| Cardiff | cardiffnlp/twitter-roberta-base-sentiment-latest | HuggingFace Inference API | $0.00 |
| Siebert | siebert/sentiment-roberta-large-english | HuggingFace Inference API | $0.00 |

---

## Key Results

| Model | Strategy | Accuracy | Weighted F1 | Avg Latency | Cost |
|-------|----------|----------|-------------|-------------|------|
| Siebert | zero_shot | 73.3% | 0.673 | 2.598s | $0.00 |
| Siebert | few_shot | 73.3% | 0.673 | 0.310s | $0.00 |
| Siebert | cot | 73.3% | 0.673 | 0.275s | $0.00 |
| Cardiff | zero_shot | 66.7% | 0.683 | 0.384s | $0.00 |
| Cardiff | few_shot | 66.7% | 0.683 | 0.199s | $0.00 |
| Cardiff | cot | 66.7% | 0.683 | 1.726s | $0.00 |

---

## Recommendation

**Siebert (few_shot)** is recommended for production deployment.

Siebert achieved the highest accuracy at 73.3% and weighted F1 of 0.673.
The few_shot strategy is preferred over zero_shot due to its faster average
latency (0.310s vs 2.598s) while producing identical accuracy. CoT had no
effect on either model since both are encoder-based classifiers that ignore
prompt framing — an important architectural finding documented in the failure
analysis.

Notably, prompting strategy had zero measurable impact on accuracy for both
models. This is because Cardiff and Siebert are encoder-based classification
models, not generative models. They classify based purely on text semantics,
making them faster and cheaper to run but less flexible than GPT-style models.

---

## Cost Projection

Both models are available for free on the HuggingFace Inference API.
At Siebert's average latency of 0.310s per call, 100,000 reviews per month
could be processed in approximately 8.6 hours of compute time at $0.00 cost.
If a paid API were required at scale, Gemini flash pricing of $0.075 per 1M
tokens would cost approximately $2.25 per 100,000 reviews — still highly
cost-effective compared to manual analyst triage.

---

## Limitations

The test set of 30 examples is small. The Mixed class was underrepresented
(4 examples) which may have inflated apparent accuracy. Results should be
validated on a larger balanced sample of at least 300 examples before
production deployment. Both models also showed difficulty with reviews that
contain positive language but negative overall conclusions, suggesting a
fine-tuned aviation-specific model could improve accuracy further.
