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

| Measure | Baseline | Trained | Difference |
|---|---:|---:|---:|
| Overall accuracy | 0.433 | 0.400 | -0.033 |
| Macro F1 | 0.406 | 0.143 | -0.263 |
| F1 — `application_levers` | 0.222 | 0.000 | -0.222 |
| F1 — `cycle_pulse` | 0.500 | 0.000 | -0.500 |
| F1 — `degree_tradeoff` | 0.500 | 0.000 | -0.500 |
| F1 — `profile_calibration` | 0.400 | 0.571 | +0.171 |

**What I predicted before I looked:**

I predicted that the trained model would outperform the baseline on
`profile_calibration` and `degree_tradeoff`, because fine-tuning on r/MSCS
posts could help it learn the distinction between admission competitiveness
and whether pursuing a degree is worthwhile.

**What the gap actually means:**

On the same 30 held-out posts, the baseline correctly classified 13 posts
and the trained model correctly classified 12. The accuracy difference is
therefore only one post, but the macro F1 difference is much larger:
0.406 versus 0.143.

The trained model predicted every test post as `profile_calibration`.
Although this produced a higher F1 for that label, it failed to recognize
the other three categories. The baseline achieved nonzero F1 for all four
labels, showing broader classification ability. This run provides no
evidence that fine-tuning improved overall performance.

`cycle_pulse` has only two test examples, so its baseline F1 of 0.500
should be interpreted cautiously.

### Effect of label definitions

I also ran the baseline without `label_definitions.txt`, using only the bare
label names. Both runs used the same 30 held-out posts.

| Measure | Baseline with definitions | Baseline without definitions | Trained model |
|---|---:|---:|---:|
| Overall accuracy | 0.433 | 0.233 | 0.400 |
| Macro F1 | 0.406 | 0.257 | 0.143 |
| F1 — `application_levers` | 0.222 | 0.222 | 0.000 |
| F1 — `cycle_pulse` | 0.500 | 0.154 | 0.000 |
| F1 — `degree_tradeoff` | 0.500 | 0.500 | 0.000 |
| F1 — `profile_calibration` | 0.400 | 0.154 | 0.571 |

Providing definitions improved baseline accuracy by **20 percentage points**,
from 23.3% to 43.3%, corresponding to six additional correct predictions out
of 30 posts. Macro F1 increased from **0.257 to 0.406**. The improvements
appeared in `cycle_pulse` and `profile_calibration`; the other two labels
had unchanged F1 scores.

This suggests that the custom label names alone did not communicate their
meaning sufficiently to the baseline. I use the **baseline with definitions**
as my main comparison because it received the intended meaning of each label. The trained model beat the bare-name baseline on accuracy, but still had
lower macro F1. It did not outperform the baseline with definitions on
either overall measure.

---

## Run Log — Before

**Device:** Apple Silicon GPU (`mps`), PyTorch 2.14.1.
Each seed was evaluated on 30 held-out posts.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥70% in every seed | 40.0% | 40.0% | 43.3% | MISSED |
| 2. Every label performs acceptably | Every label's F1 ≥0.55 in every seed | Minimum F1: 0.000 | Minimum F1: 0.000 | Minimum F1: 0.000 | MISSED |
| 3. Hardest-boundary confusion | ≤25% between `profile_calibration` and `degree_tradeoff` in every seed | 40.0% | 40.0% | 35.0% | MISSED |
| 4. Dataset size and balance | ≥200 distinct posts; ≥30 per label; no label above 50% | 200 rows; minimum count: 17; largest share: 39% | Same dataset | Same dataset | MISSED |
| 5. Confidence helps identify reliable predictions | Accuracy gap ≥10 percentage points in every seed | 90% − 10% = 80 points | 70% − 20% = 50 points | 70% − 20% = 50 points | MET |

**How the measurements were calculated:**

- **Criterion 2:** The minimum per-label F1 is zero in every seed.
  `application_levers` and `cycle_pulse` never achieved nonzero F1.
- **Criterion 3:** Divide the two cross-label confusion counts by the total
  true test posts in those two classes. Seeds 42 and 7 give
  `(0 + 8) / (12 + 8) = 40%`; seed 2024 gives
  `(0 + 7) / (12 + 8) = 35%`.
- **Criterion 4:** Check the complete dataset once, before splitting.
  `cycle_pulse` has only 17 examples, below the required 30.
  The 200 rows still need a duplicate check to establish distinctness.
- **Criterion 5:** Subtract the least-confident third's accuracy from the
  most-confident third's accuracy. Each group contains 10 test posts.

Accuracy varied by only 3.3 percentage points across seeds, but performance
was consistently weak. Meeting the confidence criterion means confidence
helped rank predictions by reliability; it does not mean the classifier
successfully distinguished all four labels.

### Confusion matrix

The following matrix is for **seed 42**. Rows are true labels and columns
are predicted labels.

| true \ predicted | profile_calibration | application_levers | degree_tradeoff | cycle_pulse |
|---|---:|---:|---:|---:|
| **profile_calibration** | 12 | 0 | 0 | 0 |
| **application_levers** | 8 | 0 | 0 | 0 |
| **degree_tradeoff** | 8 | 0 | 0 | 0 |
| **cycle_pulse** | 2 | 0 | 0 | 0 |

**My biggest off-diagonal number, and what it means:**

The largest off-diagonal count is tied: eight `application_levers` posts
and eight `degree_tradeoff` posts were predicted as `profile_calibration`,
with zero errors in the reverse directions. The model predicted all
30 posts as `profile_calibration`, so its 40% accuracy reflects the
largest class's share of this test set rather than successful separation
of the four categories.

---

## Verdicts and Diagnoses

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy | At least 70% in every seed | MISSED | Accuracy was 40.0%, 40.0%, and 43.3%; all three runs fell below 70%. |
| 2 | Per-label performance | Every label's F1 at least 0.55 in every seed | MISSED | `application_levers` and `cycle_pulse` had zero F1 across all seeds. `degree_tradeoff` scored 0.000, 0.000, and 0.167. |
| 3 | Hardest-boundary confusion | At most 25% confusion between `profile_calibration` and `degree_tradeoff` in every seed | MISSED | Cross-label confusion was 40%, 40%, and 35%, calculated from the two cross-label cells divided by the total true posts in these classes. |
| 4 | Dataset size and balance | At least 200 distinct posts, at least 30 per label, and no label above 50% | MISSED | The dataset contains 200 rows and the largest label represents 39%, but `cycle_pulse` has only 17 examples. Distinctness still requires checking possible reposts. |
| 5 | Confidence ranking | Most-confident third at least 10 percentage points more accurate than least-confident third in every seed | MET | The accuracy gaps were 80, 50, and 50 percentage points, exceeding the target in every run. |

**Diagnoses**

### 1. Overall accuracy: the model relies heavily on the largest class

For seeds 42 and 7, every test post was predicted as
`profile_calibration`. For seed 2024, 26 of 30 predictions were still
assigned that label. The low accuracy spread therefore reflects consistently
weak classification rather than reliable performance across categories.

The largest class accounts for 39% of the full dataset and 40% of each test
split, explaining why predicting it exclusively produces 40% accuracy.
This pattern suggests that the current training run did not learn enough
to separate the categories. Class imbalance may contribute, but these
results alone do not establish the underlying cause.

### 2. Per-label performance: insufficient minority coverage and broader learning failure

`cycle_pulse` has only 17 examples overall, including 12 training posts and
two test posts per seed. This provides limited training coverage and makes
its test F1 especially sensitive to individual mistakes.

However, scarcity alone cannot explain all failures. `application_levers`
has 54 examples and still receives zero F1 in every run. The model's
heavy reliance on `profile_calibration` suggests a broader learning problem
that needs investigation alongside label quality and class balance.

### 3. Hardest boundary: degree decisions are repeatedly treated as profile evaluations

Eight of eight true `degree_tradeoff` posts were predicted as
`profile_calibration` for seeds 42 and 7, and seven of eight were assigned
that label for seed 2024. No true `profile_calibration` posts were predicted
as `degree_tradeoff` in any run.

These categories can share detailed applicant profiles, even though their
main questions differ: “Where can I get in?” versus “Is graduate school
worthwhile?” The overlap makes this a plausible difficult boundary.
However, because the model also misclassifies other categories into
`profile_calibration`, the matrix does not prove that this boundary alone
caused the failure.

### 4. Dataset balance: too few examples of cycle updates

`cycle_pulse` contains 17 posts, which is 13 below my minimum target.
The other labels contain 78, 54, and 51 posts. Collection therefore produced
much less coverage of admission updates and community plans than of other
requests.

I should collect additional distinct `cycle_pulse` posts and inspect possible
reposts before splitting again. Changing existing labels merely to balance
the counts would not address the coverage problem.

### What the agreement report contributes

I agreed with staff on 29 of 30 posts (96.7%). The one disagreement was
`analysis` versus `hot_take`: I overlooked that a specific comparison
supported the argument because I found the evidence unconvincing.
My adjudication placed this in pile (a), where the written rule covered
the case but I applied it loosely.

This provides one concrete example of inconsistent rule application and
supports reviewing my r/MSCS labels against their written decision rules.
It does not establish widespread labelling inconsistency: the staff task
uses different labels, and agreement was high on that set.

Taken together, the evidence points to limited `cycle_pulse` coverage and
a model that mostly predicts the largest class. Inconsistent labels and
overlapping categories remain possible contributors, but they need a
targeted review before I can claim either is the main cause.

---

## Agreement Report

**Agreement rate:** 29 / 30 = **96.7%** (rounded to 97% by the script).

### Disagreements

**1.**
> *The post:* “Three of us ran the same session on the same day, different shoes, within 10 seconds of each other over 5 miles. I'm not saying shoes do nothing, but whatever they do was smaller than the noise between three people.”
>
> *Staff said / I said:* `analysis` / `hot_take`.
>
> *My call, and why:* I agree with `analysis`. Rule 1 asks whether a specific, checkable fact carries the argument. The comparison—three runners wearing different shoes finishing within 10 seconds over five miles—is the evidence supporting the conclusion about shoe effects. Removing those details removes the argument's support. Different runners introduce other variables, making the evidence limited, but weak evidence is different from offering no evidence.
>
> *Which pile:* **(a) The rule covered it, and I applied it loosely.** My original label treated a debatable conclusion as an unsupported claim, overlooking the specific comparison supporting it.

**What the pattern in my disagreements tells me:**

My only disagreement concerned the boundary between an evidence-backed argument and an unsupported claim. It suggests that I should distinguish whether evidence supports the reasoning from whether that evidence is convincing.

The 29 agreements show that I mostly applied the staff taxonomy consistently on these posts. They do not prove that my own 200 r/MSCS labels are consistent, because those use a different taxonomy. For my dataset, I should review mixed posts against the written boundary rules rather than relying on an overall impression.

---

## The Improvement

**What I changed:**

I increased training from three epochs to six, keeping the dataset,
taxonomy, learning rate, batch size, and three seeds unchanged.

**Which diagnosis pointed at it:**

The model predicted only `profile_calibration` for seeds 42 and 7,
and mostly that label for seed 2024. Training loss was still decreasing
during the original run, so I tested whether additional training would
help the model distinguish the other categories.

This is a test of insufficient training as a possible cause, not a
confirmed diagnosis. The shortage of `cycle_pulse` examples remains
unresolved.

**Why I chose this improvement:**

My first plan was to add more `cycle_pulse` posts because it had only
17 examples and zero F1 across all three seeds. However, I could not find
additional suitable posts in r/MSCS during collection. Rather than assign
unrelated posts to that label, I chose to test one training-setting change:
increasing the number of epochs from three to six.

All other settings and the dataset remained unchanged. The shortage of
`cycle_pulse` examples remains an unresolved limitation.

### Run Log — After

**Change:** Increased training from three to six epochs. The dataset,
model, learning rate, batch size, and seeds remained unchanged.

**Device:** Apple Silicon GPU (`mps`), PyTorch 2.14.1.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥70% in every seed | 53.3% | 46.7% | 46.7% | MISSED |
| 2. Every label performs acceptably | Every label's F1 ≥0.55 in every seed | Minimum F1: 0.000 | Minimum F1: 0.000 | Minimum F1: 0.000 | MISSED |
| 3. Hardest-boundary confusion | ≤25% between `profile_calibration` and `degree_tradeoff` in every seed | 35.0% | 20.0% | 20.0% | MISSED: seed 42 exceeds target |
| 4. Dataset size and balance | ≥200 distinct posts; ≥30 per label; no label above 50% | 200 rows; minimum count: 17; largest share: 39% | Same dataset | Same dataset | MISSED: `cycle_pulse` has only 17 examples |
| 5. Confidence ranking | Accuracy gap ≥10 percentage points in every seed | 90% − 40% = 50 points | 70% − 20% = 50 points | 70% − 30% = 40 points | MET |

Criterion 3 uses the two cross-label confusion counts divided by the
20 true test posts in those classes: 7/20, 4/20, and 4/20.
Errors into other labels are excluded from this particular measurement.

### Confusion matrix — after improvement

The following matrix shows seed 42 after increasing training from three
to six epochs.

| true \ predicted | profile_calibration | application_levers | degree_tradeoff | cycle_pulse |
|---|---:|---:|---:|---:|
| **profile_calibration** | 11 | 1 | 0 | 0 |
| **application_levers** | 4 | 4 | 0 | 0 |
| **degree_tradeoff** | 7 | 0 | 1 | 0 |
| **cycle_pulse** | 0 | 2 | 0 | 0 |

For seed 42, accuracy increased from 40.0% to 53.3%, corresponding to four
additional correct predictions. Unlike the original run, the model now
correctly identifies some `application_levers` and `degree_tradeoff` posts.
However, it still fails to identify any `cycle_pulse` posts. Results from
the other two seeds are needed before concluding that the improvement
helped consistently.

### Did it help, and how do I know?

Increasing training to six epochs improved accuracy and macro F1
for every seed.

| Measure | Seed 42: Before → After | Seed 7: Before → After | Seed 2024: Before → After |
|---|---|---|---|
| Accuracy | 40.0% → 53.3% | 40.0% → 46.7% | 43.3% → 46.7% |
| Macro F1 | 0.143 → 0.351 | 0.143 → 0.274 | 0.200 → 0.301 |

`application_levers` improved from zero F1 in every seed to 0.533,
0.455, and 0.333. However, `cycle_pulse` still had zero F1 across
all seeds, and `degree_tradeoff` remained weak.

The change helped, The model now correctly identifies some `application_levers` posts in every seed, whereas its F1 for that label was previously zero throughout.
However, `degree_tradeoff` remains weak and `cycle_pulse` still has zero
F1 in all three runs. The improvement helped consistently across these
seeds, but criteria 1–4 remain MISSED and criterion 5 remains MET but it did not change the overall criterion verdicts, more training partially addressed the classification failure without resolving it.

---

## What's Still Broken

### 1. Overall accuracy — MISSED

Accuracy improved to 53.3%, 46.7%, and 46.7%, but remained below the
70% target in every seed. I would next inspect misclassified posts
alongside the training and validation curves to investigate whether
input truncation, overlapping categories, or insufficient learning
contributed to the errors.

I stopped after the planned three-to-six-epoch experiment so I could
report the effect of one change without mixing in additional changes.

### 2. Per-label performance — MISSED

Every label needed an F1 of at least 0.55 in every seed.
`application_levers` improved but remained below that threshold;
`degree_tradeoff` remained weak, and `cycle_pulse` had zero F1 throughout.

I would collect more suitable `cycle_pulse` examples and review errors
in the other categories against the written taxonomy. I could not find
additional suitable `cycle_pulse` posts during collection, so that
coverage problem remains unresolved.

### 3. Hardest-boundary confusion — MISSED

Confusion between `profile_calibration` and `degree_tradeoff` fell to
35%, 20%, and 20%. Although two seeds met the 25% limit, seed 42 did not,
so the criterion remains missed.

I would review posts combining admission odds with degree-value questions
and check whether their labels consistently follow the main-request rule.
My staff agreement check found one case where I applied a written boundary
loosely, making this review worthwhile, but it does not prove that the
r/MSCS boundary was labelled inconsistently. I stopped before making
another intervention because this experiment changed only the epoch count.

### 4. Dataset size and balance — MISSED

The dataset still has only 17 `cycle_pulse` examples, below the minimum
of 30. No label exceeds 50%, but possible reposts also need checking
before claiming that all 200 rows are distinct posts.

I would inspect possible duplicates and extend collection over a longer
period to find additional admission-cycle posts. I stopped collection
when I could not find more suitable examples, rather than force unrelated
posts into `cycle_pulse`.

### 5. Confidence ranking — MET, with limitations

The most-confident third was 50, 50, and 40 percentage points more accurate
than the least-confident third. This met the target in every seed, but
useful confidence ranking does not compensate for poor recognition of
individual categories.

**The gap between what I meant my labels to capture and what the model learned:**

My labels were intended to distinguish admission competitiveness,
application actions, degree-value decisions, and admission-cycle updates,
but the model still relies heavily on `profile_calibration`.
In the seed-42 after-run matrix, seven of eight `degree_tradeoff` posts
were assigned that label and both `cycle_pulse` posts were called
`application_levers`, showing that these distinctions remain poorly learned.

---
### How I used AI

Firstly, I used AI to help interpret the baseline comparison, and calculate criterion measurements from the three-seed results. Secondly, AI suggested increasing training from three to six epochs after I could not find more suitable `cycle_pulse` posts. It also helped check whether the changed setting was used when an outdated displayed table initially appeared unchanged.


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
