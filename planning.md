# TakeMeter Planning Document

## Community

I chose the NBA Reddit community (primarily r/nba and team-specific NBA subreddits). This community is a good fit because discussions range from detailed basketball analysis to emotional game reactions and bold predictions. The variety of discourse makes it well suited for a text classification task.

## Labels

### Analysis

Definition: A post that presents basketball reasoning, evidence, statistics, tactical observations, or historical comparisons to support a claim.

Examples:

* "The Celtics generate efficient offense because their spacing forces defenses into difficult rotations."
* "Jokic's assist numbers remain elite because Denver's offense is built around his decision making."

Potentially ambiguous example:

* "LeBron is overrated because his playoff record against top seeds is below .500."

### Hot Take

Definition: A bold opinion or prediction stated confidently without substantial supporting evidence.

Examples:

* "Victor Wembanyama will be the greatest player ever."
* "The Thunder are guaranteed to win the next three championships."

Potentially ambiguous example:

* "LeBron is overrated because his playoff record against top seeds is below .500."

### Reaction

Definition: An emotional response to a game, player performance, trade, injury, or news event.

Examples:

* "That game was insane!"
* "I can't believe he hit that shot."

Potentially ambiguous example:

* "This team is embarrassing and will never win anything."

## Hard Edge Cases

The hardest distinction is between Analysis and Hot Take.

Example:

"LeBron is overrated because his playoff record against top-seeded opponents is below .500."

Decision Rule:

If a post contains specific evidence that meaningfully supports the argument, label it Analysis.

If the evidence is weak, cherry-picked, or mainly used to justify a strong opinion, label it Hot Take.

## Data Collection Plan

Source:

* r/nba
* Team-specific NBA subreddits
* NBA discussion threads

Dataset Goal:

* Analysis: 70 examples
* Hot Take: 70 examples
* Reaction: 70 examples

Total: 210 examples

If one class is underrepresented, I will intentionally collect additional examples from discussion threads that commonly contain that label.

## Evaluation Metrics

I will use:

* Accuracy
* Precision
* Recall
* F1 Score
* Confusion Matrix

Accuracy measures overall performance, while precision, recall, and F1 provide insight into how well the model distinguishes between specific labels.

## Definition of Success

The classifier will be considered successful if:

* Overall accuracy exceeds 75%
* Each class achieves an F1 score above 0.70
* The fine-tuned model performs noticeably better than the zero-shot baseline

## AI Tool Plan

### Label Stress Testing

I will use ChatGPT to generate borderline NBA posts that fall between Analysis and Hot Take and refine label definitions if needed.

### Annotation Assistance

I may use ChatGPT to pre-label examples, but every label will be manually reviewed and corrected before inclusion in the dataset.

### Failure Analysis

After evaluation, I will provide misclassified examples to ChatGPT and ask it to identify common error patterns. Any patterns suggested by AI will be verified manually before inclusion in the final report.

