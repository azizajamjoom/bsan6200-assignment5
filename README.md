# BSAN 6200 — Assignment 5 Option C: LM Evaluation Report
**British Airways Review Sentiment Classification**  
**Author:** Aziza Jamjoom

---

## Task
Classify British Airways passenger reviews into one of three sentiment labels:
**Positive**, **Negative**, or **Mixed**.

Input: Free-text passenger review  
Output: One sentiment label  
Business value: Automates review triage, flags negative reviews for urgent response,
and tracks sentiment trends across routes and cabin classes.

---

## Models Evaluated
| Model | Full Name | Access | Cost |
|-------|-----------|--------|------|
| Cardiff | cardiffnlp/twitter-roberta-base-sentiment-latest | HuggingFace Inference API | Free |
| Siebert | siebert/sentiment-roberta-large-english | HuggingFace Inference API | Free |

---

## Results Summary
| Model | Strategy | Accuracy | Weighted F1 |
|-------|----------|----------|-------------|
| Siebert | all strategies | 73.3% | 0.673 |
| Cardiff | all strategies | 66.7% | 0.683 |

**Best combination:** Siebert + few_shot (73.3% accuracy, 0.310s avg latency, $0.00 cost)

---

## Repo Structure
```
bsan6200-assignment5/
├── README.md
├── memo.md
├── requirements.txt
├── ai_log.md
├── .gitignore
├── data/
│   └── test_set.csv
├── notebooks/
│   └── lm_evaluation.ipynb
├── prompts/
│   └── prompt_templates.md
├── results/
│   └── results_matrix.csv
└── evaluation/
    └── failure_analysis.md
```

---

## How to Run
1. Clone the repo
2. Open `notebooks/lm_evaluation.ipynb` in Google Colab
3. Add API keys to Colab Secrets: `HF_TOKEN` and `GEMINI_KEY`
4. Run all cells top to bottom
5. Upload `data/test_set.csv` when prompted

---

## Key Finding
Prompting strategy (zero_shot, few_shot, CoT) had zero effect on accuracy for both
models. Cardiff and Siebert are encoder-based classifiers that ignore prompt framing
and classify based solely on text semantics — a fundamental difference from
generative models like GPT or Gemini where prompt engineering significantly
impacts output quality.
