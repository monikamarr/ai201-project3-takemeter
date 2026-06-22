# TakeMeter: NBA Reddit Comment Classification

## Project Overview

This project applies the TakeMeter framework to NBA Reddit discussions. The goal is to classify comments into one of three categories:

* Analysis
* Hot Take
* Reaction

The project compares a zero-shot large language model baseline against a fine-tuned DistilBERT classifier trained on a manually annotated dataset.

---

# Community Choice and Reasoning

I chose NBA Reddit communities because they contain a wide variety of basketball discussion styles. Some users provide detailed reasoning about roster construction, trades, coaching, and player performance, while others post bold opinions or emotional reactions.

This diversity makes NBA Reddit a strong environment for studying different types of online discourse.

Data was collected from:

* r/nba
* NBA trade discussion communities
* NBA news and discussion threads
* Player discussion and highlight threads

---

# Label Taxonomy

## Analysis

Definition:

Comments that use basketball reasoning, evidence, trade logic, roster construction, strategy, or factual comparisons.

Examples:

1. "The Wolves wouldn't do that trade because Gobert's defense is more valuable than Beal's scoring."

2. "The Spurs aren't going to send out multiple first-round picks just to salary dump Fox."

---

## Hot Take

Definition:

Bold opinions, rankings, predictions, or strong claims that are not heavily supported by evidence.

Examples:

1. "Victor Wembanyama will retire as the greatest player of all time."

2. "The most athletic human I've ever seen and it's not close."

---

## Reaction

Definition:

Emotional responses, jokes, excitement, frustration, disbelief, or short reactions to events.

Examples:

1. "That game was insane!"

2. "Lmao get a grip."

---

# Dataset

The final dataset contains 233 manually labeled NBA Reddit comments.

| Label    | Count |
| -------- | ----: |
| Analysis |   105 |
| Hot Take |    66 |
| Reaction |    62 |

## Labeling Process

Comments were manually collected from NBA Reddit discussion threads and labeled according to the taxonomy defined in the planning phase.

Each comment was reviewed individually and assigned one label. Ambiguous examples were discussed and relabeled when necessary to maintain consistency.

## Difficult-to-Label Examples

### Example 1

Comment:

> "The most athletic human I've ever seen and it's not close."

Decision:

Hot Take

Reason:

This is a strong ranking claim without supporting evidence.

---

### Example 2

Comment:

> "He's still so athletic it makes people forget how much less athletic he is than his prime."

Decision:

Analysis

Reason:

The comment contains comparative reasoning and explains a basketball observation.

---

### Example 3

Comment:

> "Always crazy to think that his career tape includes highlights against both fathers and their sons."

Decision:

Analysis

Reason:

Although informal, it communicates a factual observation rather than an emotional reaction.

---

# Fine-Tuning Approach

## Base Model

DistilBERT (`distilbert-base-uncased`)

## Training Setup

* 3 training epochs
* Learning rate: 2e-5
* Batch size: 16
* Weight decay: 0.01
* Stratified train/validation/test split

## Hyperparameter Decision

I kept the default learning rate of 2e-5 because it is commonly used for BERT-family fine-tuning and provides stable training on small datasets.

I also used three epochs because the dataset contains only 233 examples and additional epochs could increase the risk of overfitting.

---

# Baseline Description

The baseline classifier used Groq's Llama 3.3 70B model in a zero-shot setting.

Prompt:

```text
You are classifying comments from NBA Reddit communities.

Choose exactly one label.

Analysis: basketball reasoning, evidence, statistics, trade logic, roster construction, coaching analysis, or strategic explanation.

Hot Take: bold opinion, ranking, prediction, insult, or strong claim with little support.

Reaction: emotional response, joke, disbelief, excitement, frustration, or short reaction.

Return exactly one of these labels and nothing else:
Analysis
Hot Take
Reaction
```

Each test example was sent to the model individually and the returned label was recorded.

---

# Evaluation Report

## Accuracy

| Model                   | Accuracy |
| ----------------------- | -------: |
| Groq Zero-Shot Baseline |    57.1% |
| Fine-Tuned DistilBERT   |    57.1% |

The fine-tuned model matched the baseline accuracy.

---

## Per-Class Metrics (Fine-Tuned Model)

| Label    | Precision | Recall |   F1 |
| -------- | --------: | -----: | ---: |
| Analysis |      0.52 |   1.00 | 0.68 |
| Hot Take |      0.00 |   0.00 | 0.00 |
| Reaction |      1.00 |   0.44 | 0.62 |

---

## Per-Class Metrics (Baseline)

| Label    | Precision | Recall |   F1 |
| -------- | --------: | -----: | ---: |
| Analysis |      0.82 |   0.56 | 0.67 |
| Hot Take |      1.00 |   0.20 | 0.33 |
| Reaction |      0.41 |   1.00 | 0.58 |

---

## Confusion Matrix (Fine-Tuned Model)

| True Label | Predicted Analysis | Predicted Hot Take | Predicted Reaction |
| ---------- | -----------------: | -----------------: | -----------------: |
| Analysis   |                 16 |                  0 |                  0 |
| Hot Take   |                 10 |                  0 |                  0 |
| Reaction   |                  9 |                  0 |                  0 |

The fine-tuned model heavily favored the Analysis category and failed to learn a strong distinction between Analysis and Hot Take.

---

# Wrong Predictions Analysis

## Error 1

Text:

> "The most athletic human I've ever seen and it's not close."

True Label:

Hot Take

Predicted Label:

Analysis

Reason:

The model interpreted player evaluation language as basketball analysis rather than a bold opinion.

---

## Error 2

Text:

> "Never seen a talent like him before or after."

True Label:

Hot Take

Predicted Label:

Analysis

Reason:

The statement is an opinion but resembles analytical player evaluation.

---

## Error 3

Text:

> "The documentary will be 100 hours long and still not be enough."

True Label:

Reaction

Predicted Label:

Analysis

Reason:

The model struggled to recognize sarcasm and emotional language.

---

# Sample Classifications

| Text                                                            | Predicted Label | Confidence | Correct? |
| --------------------------------------------------------------- | --------------- | ---------: | -------- |
| We made it to the finals with him.                              | Analysis        |      0.372 | Yes      |
| Missing 10–15 games a year is pretty average.                   | Analysis        |      0.375 | Yes      |
| The documentary will be 100 hours long and still not be enough. | Analysis        |      0.372 | No       |
| He is now the perfect tank commander.                           | Analysis        |      0.364 | No       |
| The most athletic human I've ever seen and it's not close.      | Analysis        |      0.373 | No       |

### Correct Prediction Explanation

"We made it to the finals with him."

This was correctly classified as Analysis because it references a basketball outcome and serves as evidence supporting an argument about a player's value.

---

# Reflection

The model successfully learned to identify many Analysis comments but struggled to separate Analysis from Hot Take.

This suggests that NBA discussions often mix opinions with basketball reasoning, making the categories less distinct than originally expected.

The model appeared to learn that Analysis was the safest prediction when uncertain, which caused it to over-predict that category.

---

# Spec Reflection

## How the Spec Helped

The requirement to define labels and edge cases before collecting data improved consistency during annotation and forced me to think carefully about category boundaries.

## How Implementation Diverged

During annotation I discovered that Analysis and Hot Take overlap much more than expected. Many comments contained both basketball reasoning and strong opinions, making the original label boundaries difficult to enforce consistently.

---

# AI Usage

## Example 1

I used ChatGPT to help refine the label taxonomy by generating borderline examples between Analysis and Hot Take. I manually reviewed and revised the final definitions before using them for annotation.

## Example 2

I used ChatGPT to analyze model errors after evaluation. The AI suggested that overlap between Analysis and Hot Take was likely causing confusion. I reviewed the misclassified examples manually before incorporating those conclusions into this report.

## Annotation Assistance

ChatGPT assisted with preliminary labeling suggestions for collected Reddit comments. All labels were manually reviewed and corrected before inclusion in the final dataset.
