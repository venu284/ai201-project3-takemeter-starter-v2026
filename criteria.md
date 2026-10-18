# Acceptance criteria for TakeMeter

These targets are set in Unit 5 before training. TakeMeter classifies public
r/formula1 comments as `analysis`, `hot_take`, `question`, or `reaction`.
I want to use model confidence to prioritize comments for manual review.

## How to check the criteria

Use the notebook's stratified 70% training, 15% validation, and 15% held-out
test splits. Check criteria 1, 2, 4, and 5 separately for seeds 42, 7, and
2024 using `results_three_seeds_before.json` in Unit 6. A criterion is met
only if it holds for all three seeds, using the unrounded scores rather
than their average. Check criterion 3 once on the complete `labels.csv`.

For criteria 4 and 5, use the notebook's confidence report. Confidence is
the highest predicted class probability. The report ranks the test comments
by confidence and takes the highest and lowest `floor(n_test / 3)` comments,
with at least one comment per group. At the planned 200-example dataset size,
each test split has about 30 comments and each confidence group has about 10.
These are relative confidence groups, not a claim that a probability of 0.80
is calibrated to 80% accuracy.

## 1. Overall held-out accuracy is at least 0.65

The `accuracy` score must be at least **0.65** in each seed's held-out test
split.

**Why this target:** I would call sorting useful if roughly two out of three
F1 comments receive the right label before review. With four labels, uniform
random guessing averages 25%, so 65% requires a substantial improvement while
allowing mistakes on sarcastic takes and comments that mix questions with claims.

## 2. Every label has a held-out F1 of at least 0.50

Each of `analysis`, `hot_take`, `question`, and `reaction` must have a
`per_label` F1 score of at least **0.50** in each seed's held-out test split.

**Why this target:** A useful review workflow needs to recognize genuine
questions and supported analysis as well as reactions and unsupported takes.
I chose a 0.50 floor because 200 examples divided among four labels leave small
test counts, and the `analysis` versus `hot_take` boundary requires detecting
whether a specific detail actually supports the conclusion.

## 3. Each label represents 15% to 40% of the dataset

In the complete `labels.csv`, each of the four labels must account for at
least **15%** and at most **40%** of all examples, including both endpoints.
For exactly 200 examples, that means 30 to 80 examples per label.

**Why this target:** Race threads contain many short cheers and strategy
verdicts, which could crowd out supported analysis and answerable questions.
The 15% floor gives each label at least 30 examples at the planned size,
while the 40% ceiling allows unequal counts without one category dominating.

## 4. Accuracy on the most confident third is at least 0.80

The confidence report's `accuracy_most_confident_third` must be at least
**0.80** in each seed's held-out test split.

**Why this target:** I want to give confident predictions lower review
priority only if at least four out of five are correct. In a group of about
10 test comments, this allows about two mistakes, so even confident predictions
still need occasional checking, especially around the evidence boundary.

## 5. Confidence separates more reliable predictions by at least 0.20

The confidence report's `accuracy_most_confident_third` minus
`accuracy_least_confident_third` must be at least **0.20**, or 20 percentage
points, in each seed's held-out test split.

**Why this target:** Confidence helps me prioritize manual review only if the
least confident comments contain more errors than the most confident ones.
With about 10 comments in each group, a 0.20 gap means about two additional
correct predictions in the most confident group, a useful difference when
deciding where to spend review time.

Keep these original targets when reporting Unit 6 results. A missed target
stays a miss. If a criterion cannot be measured as written, append a revision
and its reason below the original rather than replacing or lowering the target.

## Measurement clarification for criterion 5

The 0.20 target is unchanged. Evaluate its exact boundary using the number
of correct predictions in each confidence group. Direct floating-point
subtraction can represent `1.0 - 0.8` as `0.19999999999999996`, even though
10 correct predictions versus 8 out of 10 is exactly a 20-percentage-point gap.

The notebook saves each group's accuracy and their shared `n_per_third`.
Recover the whole-number counts from those saved values and compare them:

```python
n = confidence["n_per_third"]
top = round(confidence["accuracy_most_confident_third"] * n)
bottom = round(confidence["accuracy_least_confident_third"] * n)
met = 5 * (top - bottom) >= n
```

Here, `confidence` is one seed's saved confidence report. Rounding recovers
the integer number of correct predictions; it does not round an accuracy
or lower the acceptance threshold. Apply this check separately to each seed.

## Interpreting confidence results

With about 10 comments per confidence group, one prediction changes a group's
accuracy by about 10 percentage points. Results across the three seeds help
show this variation, but the splits reuse the same dataset and are not
independent samples of the community.

Criteria 4 and 5 test the most confident third's reliability and its
advantage over the least confident third. They do not measure the middle
third, so passing them does not establish a useful review ordering across
all comments.

A highly accurate classifier can miss criterion 5 because there are too few
errors to produce a 20-percentage-point gap. A perfect classifier has a gap
of zero and still misses that target. Preserve that verdict and explain the
observed accuracy and error counts before diagnosing the miss as a confidence
or classification problem.
