# BSAN 6200 — Assignment 5, Option C: LM Evaluation Report

**Project:** British Airways Review Sentiment Classification  
**Author:** Aziza Jamjoom  
**Course:** BSAN 6200 — Applied Information Systems and Business Analytics

---

## 1. Project Description

This project systematically evaluates two language models on a business-relevant
NLP task: classifying British Airways passenger reviews into one of three sentiment
labels — **Positive**, **Negative**, or **Mixed**.

**Input:** Free-text passenger review (one sentence to several paragraphs)  
**Output:** Exactly one label — Positive, Negative, or Mixed

**Business relevance:** Airlines receive thousands of online reviews monthly.
An automated sentiment classifier enables British Airways to instantly flag
negative reviews for urgent customer recovery, track sentiment trends across
routes and cabin classes, and reduce manual analyst triage time significantly.

**Evaluation approach:** 30 manually labeled reviews were tested across 2 models
and 3 prompting strategies (zero_shot, few_shot, chain-of-thought) for a total
of 180 API calls. Results were measured by accuracy, per-class F1, format
compliance, latency, and cost.

---

## 2. Models and Tools Used

| Model Key | Full Model Name | Type | Provider |
|-----------|----------------|------|----------|
| cardiff | cardiffnlp/twitter-roberta-base-sentiment-latest | Encoder classifier | HuggingFace |
| siebert | siebert/sentiment-roberta-large-english | Encoder classifier | HuggingFace |

**Libraries:**
- `requests` — HuggingFace Inference API calls
- `pandas` — data handling and results storage
- `scikit-learn` — accuracy, F1, classification report
- `google-generativeai` — Gemini SDK (used for model discovery)
- `huggingface_hub` — HuggingFace client
- `time` — latency tracking per call

---

## 3. Paid vs. Free Path

This project used the **fully free path**. No paid APIs were used.

| Model | Access Method | Cost per Call | Total Cost (180 calls) |
|-------|--------------|---------------|------------------------|
| Cardiff | HuggingFace Inference API (free tier) | $0.00 | $0.00 |
| Siebert | HuggingFace Inference API (free tier) | $0.00 | $0.00 |

**Hypothetical paid cost at scale (Gemini flash pricing at $0.075/1M tokens):**
- 1,000 reviews/month → ~$0.023
- 10,000 reviews/month → ~$0.225
- 100,000 reviews/month → ~$2.25

Both free-tier models are production-viable at current volumes given $0 cost
and sub-second latency on most calls.

---

## 4. Setup Instructions

1. Clone this repository
2. Open `notebooks/lm_evaluation.ipynb` in Google Colab
3. Add API keys to Colab Secrets (left sidebar → key icon):
   - `HF_TOKEN` — get free at hf.co/settings/tokens (requires Fine-grained token with "Make calls to Inference Providers" permission enabled)
   - `GEMINI_KEY` — get free at aistudio.google.com (used only for model discovery, not experiments)
4. Upload `data/test_set.csv` when prompted in Step 4
5. Run all cells top to bottom
6. All results save automatically to `results/results_matrix.csv`

**Note:** The raw source dataset (reviews_data1.csv, 3,427 BA reviews) is not
included in this repo due to size. The 30 sampled and labeled examples used for
evaluation are in `data/test_set.csv`.

---

## 5. Key Findings

- **Best model:** Siebert (siebert/sentiment-roberta-large-english) — 73.3% accuracy, Weighted F1: 0.673
- **Runner-up:** Cardiff (cardiffnlp/twitter-roberta-base-sentiment-latest) — 66.7% accuracy, Weighted F1: 0.683
- **Prompting strategy had zero effect** on accuracy for both models. Cardiff always scored 66.7% and Siebert always scored 73.3% regardless of whether zero_shot, few_shot, or CoT was used. This is because both models are encoder-based classifiers that ignore prompt framing and classify based solely on input text semantics — a fundamental architectural difference from generative models like GPT or Gemini.
- **Mixed was the hardest label** — misclassified 21 times out of 24 total Mixed examples across all runs, most often collapsed into Negative.
- **Easy tier had the highest failure rate** (37.5%) despite being labeled easy, because many positive reviews contained negative-sounding phrases that triggered misclassification.

---

## 6. File Descriptions

| File | Description |
|------|-------------|
| `README.md` | This file — project overview, setup, findings |
| `memo.md` | Executive memo summarizing results and business recommendation |
| `ai_log.md` | Detailed log of all AI tool usage per Tier 2 policy |
| `requirements.txt` | All Python libraries required to run the notebook |
| `.gitignore` | Excludes .env and other sensitive/unnecessary files from Git |
| `data/test_set.csv` | 30 manually labeled BA reviews across 3 difficulty tiers (easy, hard, really_difficult) with ground truth labels assigned by Aziza Jamjoom |
| `notebooks/lm_evaluation.ipynb` | Main Jupyter notebook — full experiment pipeline from data loading through metrics and analysis |
| `prompts/prompt_templates.md` | All 3 prompt strategy templates (zero_shot, few_shot, cot) with documented rationale for each |
| `results/results_matrix.csv` | Raw results from all 180 API calls — model, strategy, input, predicted label, ground truth, correct/incorrect, latency, cost |
| `evaluation/failure_analysis.md` | Grouped failure analysis describing what went wrong, on which examples, and why |
