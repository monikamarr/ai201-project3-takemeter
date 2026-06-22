# TakeMeter: NBA Reddit Comment Classification

## Project Overview

This project builds a classifier for NBA Reddit comments using the TakeMeter framework. The goal is to categorize comments into one of three classes:

* Analysis
* Hot Take
* Reaction

The project compares a zero-shot large language model baseline against a fine-tuned DistilBERT classifier trained on a manually annotated dataset.

## Community

The dataset was collected from NBA-related Reddit communities, including discussion threads, trade discussions, player debates, and reaction posts.

## Labels

### Analysis

Comments that use basketball reasoning, evidence, roster logic, trade logic, statistics, or strategic discussion.

Example:

> "The Wolves wouldn't make that trade because Gobert's defense is more valuable than Beal's scoring."

### Hot Take

Bold opinions, rankings, predictions, or strong claims with limited supporting evidence.

Example:

> "Victor Wembanyama will retire as the greatest player of all time."

### Reaction

Emotional responses, jokes, excitement, frustration, disbelief, or short reactions.

Example:

> "That game was insane!"

## Dataset

The final dataset contains 233 manually labeled NBA Reddit comments.

| Label    | Count |
| -------- | ----: |
| Analysis |   105 |
| Hot Take |    66 |
| Reaction |    62 |

The dataset was split into training, validation, and test sets using stratified sampling.

## Models

### Baseline

A zero-shot classifier using Groq's Llama 3.3 70B model.

### Fine-Tuned Model

DistilBERT (`distilbert-base-uncased`) fine-tuned on the annotated NBA Reddit dataset.

Training configuration:

* 3 epochs
* Learning rate: 2e-5
* Batch size: 16
* Weight decay: 0.01

## Results

| Model                   | Accuracy |
| ----------------------- | -------: |
| Groq Zero-Shot Baseline |    57.1% |
| Fine-Tuned DistilBERT   |    57.1% |

The fine-tuned model matched the baseline but did not outperform it.

## Error Analysis

The largest source of error was distinguishing Hot Take comments from Analysis comments.

Examples:

True Label: Hot Take

> "The most athletic human I've ever seen and it's not close."

Predicted Label: Analysis

Many Hot Take comments contained basketball-specific language that resembled analytical discussion. As a result, the model frequently defaulted to the Analysis category when uncertain.

The confusion matrix also showed that Analysis was the dominant predicted class, suggesting that the model struggled to learn a clear boundary between Analysis and Hot Take.

## AI Usage

ChatGPT was used during project development to:

* Refine label definitions
* Generate edge-case examples
* Review annotation consistency
* Analyze model errors
* Assist with project documentation

All final labels and project decisions were manually reviewed before inclusion in the dataset.

## Future Work

Potential improvements include:

* Collecting a larger dataset
* Increasing the number of Hot Take examples
* Refining label definitions
* Experimenting with alternative transformer models and hyperparameters
