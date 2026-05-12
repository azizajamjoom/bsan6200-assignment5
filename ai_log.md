# AI Usage Log — BSAN 6200 Assignment 5 Option C
**Student:** Aziza Jamjoom  
**Assignment:** LM Evaluation Report — British Airways Sentiment Classification  
**AI Tool Used:** Claude (Anthropic) via claude.ai

---

## Policy Statement
Per Assignment 5 Tier 2 restrictions, the following were completed entirely
without AI assistance:
- All 30 ground truth labels in test_set.csv (read and labeled manually)
- Failure analysis pattern identification and narrative
- Best combination justification based on personal interpretation of results
- Evaluation interpretation and markdown analysis cells in the notebook

AI was used exclusively for coding assistance, debugging, and file structure
guidance as documented in detail below.

---

## Session Log — May 11, 2026

---

### 1. Dataset Understanding and Task Design
**What I asked:** Help understanding the BA_AirlineReviews dataset columns and
deciding what NLP task to use for the assignment.

**What Claude helped with:** Explained the three columns (Verified, Reviews,
Recommended), identified that the dataset had 3,427 reviews with average length
of 889 characters, and suggested sentiment classification (Positive/Negative/Mixed)
as the task since it maps naturally to the Recommended column and has clear
business relevance for British Airways.

**What I did myself:** Confirmed the task choice, wrote the business justification,
and defined the success criteria (accuracy >= 75%, F1 >= 0.65, format compliance
>= 90%).

---

### 2. Test Set Construction Code
**What I asked:** Help writing code to sample 30 reviews across three complexity
tiers matching my professor's guidance (20 easy, 5 hard, 5 really difficult).

**What Claude helped with:** Wrote the pandas sampling code that filtered reviews
by length and Recommended column to select each tier — easy (250-800 chars, clear
signal), hard (verified, 800-1600 chars, likely mixed), really difficult (under
150 chars or over 1800 chars). Saved output to test_set.csv.

**What I did myself:** Downloaded test_set.csv, opened it in Google Sheets, read
every single review personally, and typed Positive, Negative, or Mixed into the
ground_truth_label column for all 30 rows. This took approximately 25-30 minutes
and was done entirely without AI assistance.

---

### 3. Label Validation Error Fix
**What I asked:** The validation cell threw an AssertionError because all my labels
were lowercase (positive, negative, mixed) instead of capitalized.

**What Claude helped with:** Added .str.strip().str.capitalize() to fix the
capitalization in one line before the assertion ran.

**What I did myself:** Identified that the error came from my own spreadsheet
formatting in Google Sheets, re-uploaded the corrected file.

---

### 4. API Key Setup — HuggingFace
**What I asked:** How to get a HuggingFace API token and where to add it in Colab.

**What Claude helped with:** Walked me through navigating to hf.co/settings/tokens,
explained the difference between Read and Fine-grained token types, and instructed
me to add the token to Colab Secrets with notebook access toggled ON.

**What I did myself:** Created the account, generated the token, added it to Colab.

---

### 5. API Key Setup — Google Gemini
**What I asked:** How to get a Gemini API key from Google AI Studio. I shared a
screenshot showing I was on the wrong page (New Organization form).

**What Claude helped with:** Identified I was on the wrong page and directed me
to the correct "Get API key" button in the left sidebar of aistudio.google.com.

**What I did myself:** Created the key and added it to Colab Secrets.

---

### 6. Model Connection Failures — Extended Debugging Session
This was the most significant part of the session. Six separate API issues arose
in sequence requiring iterative troubleshooting over approximately 45 minutes.
At each step I ran the diagnostic code, shared the exact output back to Claude,
and Claude diagnosed the next issue based on what I showed.

**Issue 1 — Mistral-7B-Instruct-v0.3 not supported**
Output I shared: "Model mistralai/Mistral-7B-Instruct-v0.3 is not supported
for text_generation." Claude suggested switching to v0.1 which also failed,
indicating HuggingFace had deprecated both Mistral versions on the free tier.

**Issue 2 — Gemini 404 errors (model name changed)**
Output I shared: "404 POST /v1beta/models/gemini-1.5-flash:generateContent"
Claude ran genai.list_models() and I shared the full list of available models
on my key, which included gemini-2.0-flash, gemini-2.5-flash, and others.
Claude updated the model name accordingly.

**Issue 3 — Gemini 429 rate limit**
Output I shared: "429 POST /v1beta/models/gemini-2.0-flash:generateContent"
persisting even after 60 second waits. Claude diagnosed this as a daily quota
exhaustion from repeated test calls during debugging, not a per-minute limit.
Gemini was dropped and replaced with two HuggingFace models.

**Issue 4 — HuggingFace API URL 404**
Output I shared: Status 404, raw response "Cannot POST
/models/distilbert-base-uncased-finetuned-sst-2-english" as an HTML error page.
Claude identified that HuggingFace had changed their routing and the old
api-inference.huggingface.co/models/... URL format no longer worked.

**Issue 5 — HuggingFace token missing inference permission**
Output I shared: Status 400, response "Model not supported by provider
hf-inference." I also shared a screenshot of my HuggingFace Access Tokens page
showing my token had READ-only permissions. Claude identified that READ tokens
do not include Inference API access. I created a new Fine-grained token with
"Make calls to Inference Providers" checked and updated the Colab Secret.

**Issue 6 — Finding working models**
Claude wrote a diagnostic loop testing four candidate models. I ran the cell
and shared the output showing status codes and raw responses for each:
- cardiffnlp/twitter-roberta-base-sentiment-latest: 200, returned label scores
- siebert/sentiment-roberta-large-english: 200, returned label scores
- ProsusAI/finbert: 200 but neutral/positive/negative format
- nlptown/bert-base-multilingual-uncased-sentiment: 200 but star rating format

Based on the output I shared, Cardiff and Siebert were selected as the final
two models since they both returned 200 and produced usable sentiment scores.

**What I did myself:** Ran every diagnostic cell, read and shared all outputs,
created the new HuggingFace token with correct permissions, and confirmed the
final model selection.

---

### 7. Prompt Template Design
**What I asked:** Help writing the three prompt templates (zero_shot, few_shot, cot).

**What Claude helped with:** Wrote the template text and rationale explanations
for each strategy explaining what each was expected to improve and why.

**What I did myself:** Reviewed each template, confirmed the label definitions
and examples accurately represented the task, and approved the final wording.
Also noted that for encoder-based models like Cardiff and Siebert, prompting
strategy would have no effect — this became a key finding in the analysis.

---

### 8. Experiment Loop and Metrics Code
**What I asked:** Help writing the run_one() helper function, the 180-call
experiment loop, and the metrics computation cells.

**What Claude helped with:** Wrote run_one() to track model, strategy, input,
raw output, predicted label, correct/incorrect, latency, cost, format compliance,
and error status per call. Wrote the summary metrics loop using sklearn. Wrote
the difficulty tier pivot table.

**What I did myself:** Ran all 180 experiments, monitored the progress output
showing accuracy after each of the 6 runs, and confirmed 0 errors and 0 format
fails before continuing.

---

### 9. Results Analysis and Narrative Cells
**What I asked:** Help writing the best combination justification and failure
analysis after I shared my actual experiment outputs with Claude.

Outputs I shared with Claude:
- Full results matrix showing Siebert 73.3%, Cardiff 66.7% across all strategies
- Complete failure output showing 54/180 failures, confusion matrix, and all
  individual failed examples with their review text snippets

**What Claude helped with:** Provided template language and structure for both
narrative cells based on the numbers I shared.

**What I did myself:** Identified which specific example IDs were failing and
confirmed the pattern explanations were accurate by reading the actual review
text. The interpretation of why specific reviews failed (e.g., ID 21 anchoring
on early birthday language, ID 17 reacting to "poor economy product" keywords)
was based on my own reading of those reviews in the output I shared.

---

### 10. Supporting File Generation
**What I asked:** Generate the remaining required repo files after the notebook
was complete (README, memo, ai_log, requirements, prompt_templates, failure_analysis).

**What Claude helped with:** Wrote all six files based on the actual results
and findings from the completed notebook.

**What I did myself:** Reviewed all files for accuracy against my actual results
and approved for submission.

---

## Summary Table

| Task | Claude's Role | My Role |
|------|--------------|---------|
| Dataset exploration | Wrote exploration code | Confirmed task and labels |
| Test set sampling | Wrote sampling code | Labeled all 30 reviews manually |
| Label fix | One-line capitalize fix | Identified the error source |
| API setup guidance | Step-by-step instructions | Created accounts and keys |
| API debugging (6 issues) | Diagnosed each issue | Ran cells and shared all outputs |
| Model selection | Suggested based on my output | Made final decision |
| Prompt templates | Wrote templates and rationale | Reviewed and approved |
| Experiment loop | Wrote run_one() and loop | Ran and monitored all 180 calls |
| Results metrics | Wrote sklearn metrics code | Interpreted the numbers |
| Failure analysis | Provided structure | Identified patterns from my data |
| Supporting repo files | Wrote all 6 files | Reviewed for accuracy |
