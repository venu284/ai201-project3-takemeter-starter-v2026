# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->
TakeMeter will classify individual public comments from r/formula1. The four
labels distinguish supported interpretations, unsupported takes, answerable
questions, and reactions or simple updates. I chose a fourth label for
questions as the optional stretch feature before collecting or labeling the
200 examples; the reading showed that a genuine request for an answer is a
different kind of comment from a cheer or joke.

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`

**Definition:** An F1 interpretation or prediction that connects its conclusion to a specific, checkable event, quote, comparison, or number offered as support.

**Example 1:** [Firefox72 in the daily discussion](https://www.reddit.com/r/formula1/comments/1x3u2qv/ask_rformula1_anything_daily_discussion_thread/):
> Nowhere is a bit harsh on Ferrari in 2009
> They secured a win and scored a bunch of podiums after the upgrades. Somewhere there or there about with Mclaren.
> The difference was Ferrari only had 1 driver for almost half a season post Massa's accident and yet still only finished just 1 point behind Mclaren.

**Example 2:** [droppokeguy in the pre-race discussion](https://www.reddit.com/r/formula1/comments/1x33eva/2026_singapore_grand_prix_prerace_discussion/):
> The humidity makes it impossible to dry in more than 2~H + no sun
> 2022 it took half the race before it was dry enough for slicks
> So if it rains like an hour or so before it would be gambling

### `hot_take`

**Definition:** An F1 judgment about performance or strategy, prediction, or proposed strategic action that does not connect its conclusion to a specific, checkable supporting detail, even if the claim may be true.

**Example 1:** [T4Gx in the post-race discussion](https://www.reddit.com/r/formula1/comments/1x38muu/2026_singapore_grand_prix_postrace_discussion/):
> Liam Lawson has upped his game. Deserves to be in F1 for a long time.

**Example 2:** [wfelt871 in the post-race discussion](https://www.reddit.com/r/formula1/comments/1x38muu/2026_singapore_grand_prix_postrace_discussion/):
> Another Ferrari strategic masterclass

### `question`

**Definition:** A comment primarily asking for an answerable F1 fact, explanation, or source while withholding its own conclusion.

**Example 1:** [Whatisreality228 in the daily discussion](https://www.reddit.com/r/formula1/comments/1x3u2qv/ask_rformula1_anything_daily_discussion_thread/):
> Verstappen is on form to win the title next year, yes or no? Why?

**Example 2:** [Trixie960 in the daily discussion](https://www.reddit.com/r/formula1/comments/1x3u2qv/ask_rformula1_anything_daily_discussion_thread/):
> Is there anywhere to continue watching the coverage after the race? Kayo cut off when they went to show the interviews with ferrari

### `reaction`

**Definition:** A comment primarily reporting or responding to one F1 moment with a fact, feeling, joke, or cheer, without advancing a broader judgment or seeking an answer.

**Example 1:** [Rude_Review4303 in the pre-race discussion](https://www.reddit.com/r/formula1/comments/1x33eva/2026_singapore_grand_prix_prerace_discussion/):
> Lock the fuck in Oscar..

**Example 2:** [Cumzooka in the post-race discussion](https://www.reddit.com/r/formula1/comments/1x38muu/2026_singapore_grand_prix_postrace_discussion/):
> Lmaooo all the swearing

### The hardest boundary

**Which two labels:** `analysis` and `hot_take`.

**Borderline real post:** [Huge-Revolution2416 in the pre-race discussion](https://www.reddit.com/r/formula1/comments/1x33eva/2026_singapore_grand_prix_prerace_discussion/):
> Singapore always looks like a one-stop on paper until a Safety Car flips the whole race upside down.

**The decision rule I will use every time:** A named mechanism alone is not supporting evidence. Choose `analysis` only when the comment identifies a specific checkable event, quote, comparison, or number and connects that detail to its conclusion; otherwise choose `hot_take` for a judgment or prediction. This post predicts a strategy change but cites no particular race or result, so it is `hot_take`.

For mixed forms, classify the main point: a sincere question stays `question` even if it gives context; a rhetorical question that asserts a stance follows `analysis` or `hot_take`; a bare event report stays `reaction` until it draws a broader conclusion. Tone, length, votes, and whether the F1 claim is actually correct do not determine the label.
---

## The Dataset

**Where the posts came from:**

I collected 200 complete individual comments from three public r/formula1 threads. Each CSV record contains one whole comment, including its paragraph breaks. All examples remain in one unsplit [labels.csv](labels.csv) with `text,label,note`; the notebook will make the 70/15/15 split.

| Source thread | Comments |
|---|---:|
| [Daily discussion](https://www.reddit.com/r/formula1/comments/1x3u2qv/ask_rformula1_anything_daily_discussion_thread/) | 31 |
| [Singapore pre-race discussion](https://www.reddit.com/r/formula1/comments/1x33eva/2026_singapore_grand_prix_prerace_discussion/) | 37 |
| [Singapore post-race discussion](https://www.reddit.com/r/formula1/comments/1x38muu/2026_singapore_grand_prix_postrace_discussion/) | 132 |
| **Total** | **200** |

Codex helped collect the comments from cached public browser renderings after direct Reddit JSON access returned HTTP 403. Collection took 12 minutes 30 seconds. Selection used source order before any labels were assigned: the retained daily and pre-race comments, then 132 comments spaced through the visible post-race comments. An extraction error omitted one valid daily comment together with an adjacent deleted-comment notice, so this is not complete coverage of all visible comments. There was no supplemental collection aimed at particular labels.

[data/label_sources.csv](data/label_sources.csv) records a text hash, comment ID, permalink, and thread URL for every example. All 200 bodies matched the saved thread captures; browser spotchecks also matched the longest comment from each thread. These checks used cached pages. Only 27 individual permalink headers were readable, and 11 had separately saved body text; the other 173 permalinks returned cache misses. I cannot claim independent live verification of every comment.

**How I labelled them:**

I chose 20 labels myself before AI pre-labeling, using the four definitions above. I then asked Codex to pre-label all 180 remaining comments at once, with reasons and possible alternative labels. I reviewed every comment and initially accepted all suggestions. During the hard-case review, I changed record 144 from `analysis` to `reaction` and records 165 and 168 from `hot_take` to `reaction`. All 180 pre-labeled rows are marked `pre-labelled, checked`.

After a separate AI audit, I verified and accepted three corrections to my initial cold labels:

| CSV record | Original cold label | Final label | Reason |
|---|---|---|---|
| 8 | `analysis` | `hot_take` | The prediction gives no specific supporting detail, consistent with the evidence threshold used for record 9. |
| 35 | `reaction` | `hot_take` | It proposes a strategy and predicts the race outcome; humor does not change that main point. |
| 72 | `analysis` | `reaction` | It reports Hamilton's decision without drawing a further conclusion. |

The 17 unchanged cold rows retain `cold`. The three corrected rows have notes beginning `cold; corrected after AI review` and include `original_label=...`. Their current labels are the corrected training labels, not the original unaided answers. The original 20-row cold snapshot is unchanged in the local review records, and commit `43b318d` also preserves those initial answers. Any analysis of my unaided labeling must use those original answers. The original AI suggestions for the other 180 comments remain in the local review records.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `analysis` | 17 | 8.5% |
| `hot_take` | 34 | 17.0% |
| `question` | 17 | 8.5% |
| `reaction` | 132 | 66.0% |
| **Total** | **200** | **100%** |

The 200-example goal is met, so no stop-rule exception applies. No label exceeds the milestone's 70% cap. My stricter [acceptance criterion 3](criteria.md#3-each-label-represents-15-to-40-of-the-dataset) is a **miss**: `analysis` and `question` are below 15%, and `reaction` exceeds 40%. I kept the original target and did not change labels to force balance.

These counts describe this collected file, not the community's natural distribution. The post-race thread supplies 66% of the examples, and several distinct comments react to the same cooldown-room muting. There are no exact duplicates or duplicates after case and whitespace normalization, but near copies such as records 88, 112, and 123 remain because they have different wording and source IDs. This event concentration may make a random split less representative of new races. Always predicting `reaction` would match 66% of this full file, so overall accuracy will need to be read alongside per-label F1. Milestone 5 generated the model and confidence results; their interpretation is deferred to the next unit.

**Three hard cases**

Record numbers refer to CSV records, excluding the header.

**1. CSV record 23**

> Hamilton was more than 60 points clear of Verstappen few months ago. Now he is only 8 points ahead.This is the guy who wanted Ferrari to make him the number 1 driver btw.

**Could have been:** `hot_take` or `analysis`.

**I chose, because:** I chose `hot_take` because it includes points totals, but its main point is a dismissive judgment without explaining why those numbers justify the team-priority claim.

[Original comment](https://www.reddit.com/r/formula1/comments/1x3u2qv/comment/pfeujbn/)

**2. CSV record 144**

> Perfect sweep by Verstappen
>
> Pole in Sprint Q
>
> P1 in Sprint
>
> Pole in Q
>
> P1 in Race

**Could have been:** `analysis` or `reaction`.

**I chose, because:** I chose `reaction` because I read it as a celebration and list of results rather than an interpretation or prediction.

[Original comment](https://www.reddit.com/r/formula1/comments/1x38muu/comment/pf8tii3/)

**3. CSV record 165**

> Ferrari truly snatching defeat from the jaws of victory today.

**Could have been:** `hot_take` or `reaction`.

**I chose, because:** I chose `reaction` because I read it as an immediate response in the race context. Without that context, I would read it as `hot_take`.

[Original comment](https://www.reddit.com/r/formula1/comments/1x38muu/comment/pf8prs3/)

**Related correction, CSV record 168:**

> If it wasn't for the Ferrari clowning, this race would have been a complete snoozefest.

I also considered `hot_take` and chose `reaction` for the same context-dependent reason as record 165.

[Original comment](https://www.reddit.com/r/formula1/comments/1x38muu/comment/pf8q2q2/)

**Consistency limitation:** The decisions for 165 and 168 depend on race context outside the model's comment text. They also overlap with the existing `hot_take` example, "Another Ferrari strategic masterclass." I have recorded my choices, but this boundary remains a consistency risk. The model may not be able to recover a distinction that requires context it does not receive.

---

## The Training Run

**Base model:** `distilbert-base-uncased`, fine-tuned on the four F1 labels.

**Settings:** 3 epochs, learning rate `2e-5`, batch size 16, maximum length 128 tokens, seed 42. The CSV retains whole comments; the tokenizer truncates model inputs to the configured token limit.

**Device:** `mps`, recorded as `Apple Silicon GPU (MPS)` in [results.json](results.json), using PyTorch `2.14.1`. I ran the notebook from the repository with its `.venv` kernel. Hardware can affect the arithmetic and resulting scores even with the same data and seed, so this device is part of the run record.

**Anything I changed from the defaults, and why:** No model, hyperparameter, or seed changes. I entered my name and email, then ran sections 1–5 in order. The dataset was committed in `6ad6f40` before training; neither the dataset nor the acceptance criteria changed during the run.

**Split sizes:** The notebook produced 139 training, 31 validation, and 30 test comments. Its floating-point validation-size calculation rounds up to 31; I kept that output rather than making a separate split.

| Label | Train | Validation | Test |
|---|---:|---:|---:|
| `analysis` | 11 | 3 | 3 |
| `hot_take` | 24 | 5 | 5 |
| `question` | 12 | 3 | 2 |
| `reaction` | 92 | 20 | 20 |
| **Total** | **139** | **31** | **30** |

The test set has fewer than eight examples for `analysis` (3), `hot_take` (5), and `question` (2). Their metrics will be sensitive to individual examples and split changes. Section 2 also warned that `reaction` is 66% of the full dataset and that `analysis` and `question` have only 17 examples each. There were no `[FIX ME]` errors. The personal 15% to 40% balance target remains a miss.

**Run evidence:** Training completed all 3 epochs and 27 steps. Section 5 wrote [results.json](results.json) and the 30 held-out comments in [test_split.csv](test_split.csv). The executed notebook preserves the outputs. Warnings about the missing progress widget, newly initialized classification weights, and MPS pinned memory did not prevent completion. Sections 6–7 remain unexecuted; score interpretation and the three-seed comparison belong to the next unit.

---

## How I Used AI

**Moment 1: Testing label boundaries**

- *What I asked for:* Eight synthetic F1 comments on the boundaries between my four labels, including fact-plus-claim and sincere-versus-rhetorical question cases.
- *What came back:* Probes such as "Verstappen is eight points behind Hamilton, so Ferrari's development has stalled." The number is checkable, but it does not establish the claimed cause.
- *What I changed:* I tightened `analysis` to require a specific detail connected to the conclusion, and wrote the sincere-versus-rhetorical question rule. [MILESTONE_2.md](MILESTONE_2.md) preserves all eight probes and their decisions. None is in the training CSV.

**Moment 2: Reviewing AI pre-labels**

- *What I asked for:* After my 20 cold labels, I asked Codex to pre-label all 180 remaining comments so I could review them together, instead of using the planned batches of 25.
- *What came back:* A label and reason for every comment, with alternative labels for 61. The initial suggestions included `analysis` for record 144's list of Verstappen's session results, and `hot_take` for records 165 and 168.
- *What I changed:* I initially accepted all 180 after review, then corrected those three to `reaction` during the hard-case discussion. I confirmed the reasons for records 23 and 144, supplied record 165 as my third hard case, and identified 168 as a related correction. A later independent AI audit identified inconsistent cold labels in records 8, 35, and 72; I verified and accepted the suggested corrections recorded in the dataset section. The context limitation for 165 and 168 remains unresolved.

**Pre-labelling disclosure:**

Codex initially pre-labeled 180 of 200 real comments. I reviewed every one; they are marked `pre-labelled, checked`. I chose the other 20 before that assistance and confirmed that those choices were mine. I later accepted AI-reviewed corrections to three of those 20, so the final training labels contain 183 comments whose labels received AI input and 17 unchanged cold choices. Original cold labels remain preserved separately and in the corrected rows' notes. The CSV contains no synthetic examples. Codex also assisted collection, acceptance-criteria drafting and review, validation, and this write-up. I ran the Milestone 5 notebook; Codex checked the saved outputs and artifacts and helped document the run. Performance interpretation remains for the next unit.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
