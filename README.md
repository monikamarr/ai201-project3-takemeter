# TakeMeter: NBA Reddit Comment Classification

## Project Overview

This project applies the TakeMeter framework to NBA Reddit discussions. The goal is to classify comments into one of three categories:

* Analysis
* Hot Take
* Reaction

The project compares a zero-shot large language model baseline against a fine-tuned DistilBERT classifier trained on a manually annotated dataset of NBA Reddit comments.

---

# Community Choice and Reasoning

I chose NBA Reddit communities because they contain a wide variety of discussion styles. Users debate player rankings, analyze trades, discuss roster construction, react to breaking news, and share emotional responses to games.

This diversity makes NBA Reddit an ideal community for studying different forms of online discourse and building a classifier that can distinguish between analytical discussion, strong opinions, and emotional reactions.

Data was collected from NBA-related Reddit communities, including:

* r/nba
* NBA trade discussion communities
* NBA news discussion threads
* Player highlight and debate threads

---

# Label Taxonomy

## Analysis

**Definition:**
Comments that use basketball reasoning, evidence, trade logic, roster construction, strategy, statistics, or factual comparisons.

**Examples:**

1. "The Wolves wouldn't do that trade because Gobert's defense is more valuable than Beal's scoring."

2. "The Spurs aren't going to send out multiple first-round picks just to salary dump Fox."

---

## Hot Take

**Definition:**
Bold opinions, rankings, predictions, or strong claims that are not heavily supported by evidence.

**Examples:**

1. "Victor Wembanyama will retire as the greatest player of all time."

2. "The most athletic human I've ever seen and it's not close."

---

## Reaction

**Definition:**
Emotional responses, jokes, excitement, frustration, disbelief, sarcasm, or short reactions.

**Examples:**

1. "That game was insane!"

2. "Lmao get a grip."

---

# Dataset

The final dataset contains **233 manually labeled NBA Reddit comments**.

| Label    | Count |
| -------- | ----: |
| Analysis |   105 |
| Hot Take |    66 |
| Reaction |    62 |

## Data Collection Source

Comments were manually collected from public NBA Reddit discussion threads. Threads were selected from player discussions, trade discussions, news posts, and highlight posts to ensure a mix of analytical discussion, opinions, and reactions.

## Labeling Process

Each comment was manually reviewed and assigned one label according to the taxonomy above.

The labeling process focused on identifying the primary purpose of the comment:

* Analysis comments provide reasoning or evidence.
* Hot Take comments express strong opinions or predictions.
* Reaction comments express emotion or immediate response.

Ambiguous examples were reviewed and relabeled when necessary to improve consistency.

---

# Difficult-to-Label Examples

## Example 1

Comment:

> "The most athletic human I've ever seen and it's not close."

Decision:

**Hot Take**

Reason:

This is a strong ranking claim without supporting evidence.

---

## Example 2

Comment:

> "He's still so athletic it makes people forget how much less athletic he is than his prime."

Decision:

**Analysis**

Reason:

The comment contains comparative reasoning and explains an observation.

---

## Example 3

Comment:

> "Always crazy to think that his career tape includes highlights against both fathers and their sons."

Decision:

**Analysis**

Reason:

Although informal, the comment communicates a factual observation rather than an emotional reaction.

---

# Fine-Tuning Approach

## Base Model

DistilBERT (`distilbert-base-uncased`)

## Training Setup

* Model: DistilBERT
* Epochs: 3
* Learning Rate: 2e-5
* Batch Size: 16
* Weight Decay: 0.01
* Stratified train/validation/test split

## Hyperparameter Decision

I selected a learning rate of 2e-5 because it is commonly used for fine-tuning BERT-family models and tends to provide stable training performance on smaller datasets.

I used three training epochs because the dataset contains only 233 examples. Training longer could increase the risk of overfitting.

---

# Baseline Description

The baseline classifier used Groq's Llama 3.3 70B model in a zero-shot setting.

## Prompt Used

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

## Accuracy Comparison

| Model                   | Accuracy |
| ----------------------- | -------: |
| Groq Zero-Shot Baseline |    54.3% |
| Fine-Tuned DistilBERT   |    57.1% |

Fine-tuning improved performance by **2.9 percentage points** over the baseline.

### Results Summary

```text
==================================================
RESULTS COMPARISON
==================================================
Model                               Accuracy
---------------------------------------------
Zero-shot baseline (Groq)              0.543
Fine-tuned DistilBERT                  0.571
---------------------------------------------

Fine-tuning improvement: 0.029
```

---

## Fine-Tuned Model Metrics

| Label    | Precision | Recall | F1 Score |
| -------- | --------: | -----: | -------: |
| Analysis |      0.52 |   1.00 |     0.68 |
| Hot Take |      0.00 |   0.00 |     0.00 |
| Reaction |      1.00 |   0.44 |     0.62 |

Overall Accuracy: **57.1%**

---

## Baseline Metrics

| Label    | Precision | Recall | F1 Score |
| -------- | --------: | -----: | -------: |
| Analysis |      0.82 |   0.56 |     0.67 |
| Hot Take |      1.00 |   0.20 |     0.33 |
| Reaction |      0.41 |   1.00 |     0.58 |

Overall Accuracy: **54.3%**

---

## Confusion Matrix

| True Label | Predicted Analysis | Predicted Hot Take | Predicted Reaction |
| ---------- | -----------------: | -----------------: | -----------------: |
| Analysis   |                 16 |                  0 |                  0 |
| Hot Take   |                 10 |                  0 |                  0 |
| Reaction   |                  5 |                  0 |                  4 |

### Confusion Matrix Visualization

<img width="514" height="490" alt="image" src="https://github.com/user-attachments/assets/76a229a4-c06a-416a-847b-a945cb646730" />


The confusion matrix shows that the model strongly favored the Analysis category. All Hot Take examples were classified as Analysis, while some Reaction comments were correctly identified. This indicates that distinguishing Analysis from Hot Take was the primary challenge in this project.

---

# Wrong Predictions Analysis

## Wrong Prediction 1

Text:

> "The most athletic human I've ever seen and it's not close."

True Label:

**Hot Take**

Predicted Label:

**Analysis**

Reason:

The model interpreted player evaluation language as basketball analysis rather than a strong opinion.

---

## Wrong Prediction 2

Text:

> "Never seen a talent like him before or after."

True Label:

**Hot Take**

Predicted Label:

**Analysis**

Reason:

The statement resembles player evaluation and lacks explicit indicators that it is an opinion.

---

## Wrong Prediction 3

Text:

> "He is now the perfect tank commander."

True Label:

**Hot Take**

Predicted Label:

**Analysis**

Reason:

The model appears to interpret basketball terminology as analytical discussion rather than a strong opinion.

---

# Sample Classifications

| Text                                                            | Predicted Label | Confidence | Correct? |
| --------------------------------------------------------------- | --------------- | ---------: | -------- |
| We made it to the finals with him.                              | Analysis        |      0.349 | Yes      |
| Missing 10–15 games a year is pretty average.                   | Analysis        |      0.345 | Yes      |
| The documentary will be 100 hours long and still not be enough. | Reaction        |      0.339 | Yes      |
| He is now the perfect tank commander.                           | Analysis        |      0.349 | No       |
| The most athletic human I've ever seen and it's not close.      | Analysis        |      0.347 | No       |

## Correct Prediction Explanation

Text:

> "We made it to the finals with him."

Prediction:

**Analysis**

Reason:

The comment references a basketball outcome and serves as evidence supporting an argument about a player's value. This matches the Analysis category.

---

# Reflection

The model successfully learned to identify many Analysis comments but struggled to separate Analysis from Hot Take.

This suggests that NBA discussions often blend basketball reasoning with strong opinions. Many comments contain elements of both categories, making the classification task more difficult than expected.

The model frequently defaulted to predicting Analysis when uncertain, which explains the poor Hot Take recall.

Although the fine-tuned model only slightly outperformed the baseline, the results demonstrate that domain-specific annotation can improve performance even with a relatively small dataset.

---

# Spec Reflection

## How the Spec Helped

The requirement to define labels and edge cases before data collection improved annotation consistency. Having clear label definitions made it easier to make decisions when comments were ambiguous.

## How Implementation Diverged

During annotation I discovered that Analysis and Hot Take overlap much more than expected. Many comments combined basketball reasoning with strong opinions, requiring judgment calls that were not fully anticipated in the original plan.

---

# AI Usage

## Example 1

I used ChatGPT to help refine the label taxonomy by generating borderline examples between Analysis and Hot Take. I manually reviewed and revised the final definitions before applying them during annotation.

## Example 2

I used ChatGPT to analyze model errors after evaluation. The AI helped identify patterns in the confusion matrix and suggested possible reasons for the model's tendency to over-predict Analysis.

## Annotation Assistance

ChatGPT provided preliminary labeling suggestions for collected Reddit comments. All labels were manually reviewed and corrected before inclusion in the final dataset.

---

# Project Demo
https://youtu.be/t4_4OkMT53g

