# Prompt Templates — British Airways Sentiment Classification

## Overview
Three prompting strategies were tested on each of the two models.
All templates classify a review into exactly one of: `Positive`, `Negative`, `Mixed`.

---

## Strategy 1: Zero-Shot

```
You are a sentiment classifier for British Airways passenger reviews.
Classify the review into exactly ONE label:
  Positive - overall satisfied, even with minor complaints
  Negative - overall dissatisfied or critical
  Mixed    - clearly praises some aspects AND criticizes others

Respond with ONLY the label word. No explanation.

Review: {review_text}
Label:
```

**Rationale:** Establishes a performance baseline using only the model's pretrained
knowledge. No examples are provided so the model must rely entirely on its understanding
of sentiment. Expected to handle clear positive and negative reviews well but struggle
with Mixed cases where the boundary is subtle. Format compliance may also be lower
since no example anchors the expected single-word output format.

---

## Strategy 2: Few-Shot (3 examples)

```
You are a sentiment classifier for British Airways passenger reviews.
Classify reviews as: Positive, Negative, or Mixed.
Respond with ONLY the label word.

Examples:
Review: The cabin crew were outstanding throughout the entire flight.
Food was delicious, seats comfortable, and we landed 20 minutes early.
Will definitely fly BA again.
Label: Positive

Review: Our flight was delayed 4 hours with zero communication from staff.
When we finally boarded the crew were dismissive and unhelpful.
My luggage arrived damaged. Absolutely unacceptable.
Label: Negative

Review: Check-in and the Heathrow lounge were genuinely excellent.
However once onboard the food in economy was inedible and the
entertainment system was broken for half the flight.
Label: Mixed

Now classify:
Review: {review_text}
Label:
```

**Rationale:** Concrete examples anchor the model to the exact single-word output
format and explicitly demonstrate the Mixed category boundary — the hardest class
to distinguish from Positive or Negative. Expected to outperform zero-shot on
ambiguous reviews and improve format compliance. The three examples cover one
instance of each label to avoid biasing the model toward any single class.

---

## Strategy 3: Chain-of-Thought (CoT)

```
You are a sentiment classifier for British Airways passenger reviews.
Classify the review as: Positive, Negative, or Mixed.

Think step by step before answering:
1. List the POSITIVE aspects mentioned in the review.
2. List the NEGATIVE aspects mentioned in the review.
3. Decide which side dominates, or if they are roughly equal.
4. State your final answer as exactly: Label: [Positive/Negative/Mixed]

Review: {review_text}
```

**Rationale:** Forces the model to explicitly identify positive and negative signals
before committing to a label. Expected to improve accuracy on long, multi-issue
reviews in the really difficult tier where multiple topics are discussed across
a full journey. Known tradeoff: verbose output may reduce format compliance for
generative models. For encoder-based models like Cardiff and Siebert, CoT has
no effect on output since these models classify on text semantics directly —
this architectural difference is documented in the failure analysis.
