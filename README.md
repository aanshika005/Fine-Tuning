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
TakeMeter classifies posts from **r/MSCS**, a community discussing computer science master’s admissions and career decisions, by the kind of help their authors seek. It sorts posts into four categories: `profile_calibration` for evaluating admission competitiveness, `application_levers` for choosing application tactics, `degree_tradeoff` for weighing graduate study against career and financial alternatives, and `cycle_pulse` for checking admission updates and other applicants’ plans. The goal is to distinguish the central purpose of each post, even when it covers several topics

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `profile_calibration`

**Definition:** Posts asking how an applicant’s qualifications match specific programs or which universities would make a realistic shortlist.

**Example 1:**
> “How competitive is my profile for Cambridge, Oxford, Imperial, ETH, EPFL, Toronto, Waterloo, UBC, Edinburgh, and UCL”

**Example 2:**
> “What are my chances for the following programs? Am I aiming too high? Should I have more safe Universities in my list?”

### `application_levers`

**Definition:** Posts asking which specific application action to take, such as submitting test scores, selecting recommenders, or contacting professors.

**Example 1:**
> “I got a 170 on quant but 148 on verbal. Should i submit my scores or not ?”

**Example 2:**
> “Will it deter the strength of my application if all three lors come from industry, if so by how much?”

### `degree_tradeoff`

**Definition:** Posts weighing whether pursuing a master’s degree is worthwhile compared with employment or other career paths, considering costs, risks, and long-term goals.

**Example 1:**
> “For perf or research engineering at AI labs, is an MS worth turning down the quant offer, or is a PhD the only grad path that matters?”

**Example 2:**
> “I just wanna know if even considering higher education is worth it considering my profile.”

### `cycle_pulse`

**Definition:** Posts asking about admission-cycle developments or other applicants’ experiences and plans, rather than requesting an individual profile evaluation or application strategy.

**Example 1:**
> “Has anyone received Spring 2027 admission decisions for UIUC MCS, TAMU MCS, or Columbia MSCS?”

**Example 2:**
> “Just curious if people are still going ahead with their plans or reconsidering the US altogether.”

### The hardest boundary

**Which two labels:** `profile_calibration` and `degree_tradeoff`.

**The decision rule I used every time:**

If the central question is **whether graduate school is worth pursuing compared with employment or another path**, use `degree_tradeoff`, even when the post includes a detailed applicant profile. If the central question is **which programs match the applicant’s qualifications or how competitive their application is**, use `profile_calibration`.

For posts asking both, use the main decision the author frames the post around. For example, the quant-offer post is `degree_tradeoff` because it weighs graduate study against an existing job offer.

---

## The Dataset

**Where the posts came from:**

I collected 200 public posts from r/MSCS, a Reddit community discussing computer science master's admissions, university choices, and career opportunities. Each example includes the post's title and body. The dataset includes profile evaluations, application questions, degree decisions, and admission-cycle discussions.

**How I labelled them:**

I decided the four-label taxonomy from the initial posts and labelled them. AI then reviewed the 20 existing labels and pre-labelled the remaining 180 posts according to each author's main request. I then reviewed all those 180 labels and made some corrections.

**Counts per label:**

| Label | Count | Share |
|---|---:|---:|
| `profile_calibration` | 78 | 39.0% |
| `application_levers` | 54 | 27.0% |
| `degree_tradeoff` | 51 | 25.5% |
| `cycle_pulse` | 17 | 8.5% |
| **Total** | **200** | **100%** |

No label exceeds 50%, but `cycle_pulse` falls below my target of 30 examples. 

**Three hard cases**

The following posts were sitting on the decision boundary of two labels:

**1.**
> *The post:* “Quant offer (5$00k+) or MS? T20 CompE, ~3.76 GPA. Odds for Stanford / Berkeley / UIUC?”
>
> *Could have been:* `profile_calibration` or `degree_tradeoff`.
>
> *I chose, because:* The current label is `degree_tradeoff`. Although the author asks about admission odds and provides a detailed profile, the main decision is whether graduate study justifies turning down an existing quant offer. The profile supports that decision rather than being the sole purpose of the post.

**2.**
> *The post:* “Need Help with final uni hotlist and application timelines.”
>
> *Could have been:* `profile_calibration` or `application_levers`.
>
> *I chose, because:* The current label is `application_levers`. The author provides a profile and university list, but the detailed questions focus on scheduling tests, choosing recommenders, explaining GPA in the SOP, and obtaining a credential evaluation. These are concrete application actions rather than primarily requests for admission predictions.

**3.**
> *The post:* “Have all universities paused CPT for MSCS?”
>
> *Could have been:* `cycle_pulse` or `degree_tradeoff`.
>
> *I chose, because:* The current label is `degree_tradeoff`. The title asks for a policy update, which suggests `cycle_pulse`, but the body explicitly asks whether the author should still apply given concerns about internship access. That makes the main decision whether pursuing the degree remains worthwhile. This is a borderline case to check during human review.

---

## The Training Run

**Base model:**

`distilbert-base-uncased`, fine-tuned to classify r/MSCS posts into four categories.

**Settings:**

The run used the notebook's default training settings:

- Epochs: 3
- Learning rate: 0.00002 (`2e-5`)
- Batch size: 16
- Seed: 42
- Maximum input length: 128 tokens

**Anything I changed from the defaults, and why:**

I replaced the example labels with `profile_calibration`, `application_levers`, `degree_tradeoff`, and `cycle_pulse` to match my r/MSCS taxonomy. The model and training hyperparameters remained at their defaults.

After three epochs, held-out test accuracy was **40.0%** and macro F1 was **0.143**. The model achieved an F1 of **0.571** for `profile_calibration`, but **0.000** for the other three labels. The overall accuracy therefore does not demonstrate useful performance across all four categories.

The most-confident third of test predictions had **90% accuracy**, compared with **10%** for the least-confident third. This meets my confidence-gap target for this seed, but it still needs to hold across the other two seeds.

**Split sizes:**

The notebook produced a stratified split of **139 training**, **31 validation**, and **30 test** posts.

| Label | Train | Validation | Test |
|---|---:|---:|---:|
| `profile_calibration` | 54 | 12 | 12 |
| `application_levers` | 38 | 8 | 8 |
| `degree_tradeoff` | 35 | 8 | 8 |
| `cycle_pulse` | 12 | 3 | 2 |
| **Total** | **139** | **31** | **30** |

`cycle_pulse` has only **17 examples overall** and **2 in the test set**, well below the suggested eight test examples. Its F1 is therefore especially sensitive to individual mistakes and may change substantially between seeds. The dataset also misses my collection target of at least 30 examples per label.

---

## How I Used AI

**Moment 1**

- *What I asked for:* I provided twenty labelled r/MSCS posts and asked AI to label other 180 posts based on the kinds of help their authors wanted.
- *What came back:* AI labelled some posts as *need to review*.
- *What I changed:* I reviewed all the 180 posts and made changes to the labels of some posts.

**Moment 2**

- *What I asked for:* I provided my draft of five measurable acceptance criteria for my four-label r/MSCS classifier to AI before training and asked to check them.
- *What came back:* AI proposed targets for overall accuracy, per-label F1, confusion between the hardest label pair, dataset balance, and the relationship between confidence and accuracy.
- *What I changed:* I changed my criteria 3 and criteria 5 to more defensible and achieveable numbers after AI pointed out the problems my criteria might stuck on.

**Pre-labelling disclosure:**

AI assisted with labelling of 180 posts. All 200 labels in the training file were reviewed and assigned by me after AI labelling. I claim that every AI-assisted label has been manually checked and corrected.

---

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
