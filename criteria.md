# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall accuracy of at least 70%

At least **70% of held-out posts** must receive the correct label in each run.

**Why this target:**

The classifier should identify the main request in at least seven out of ten
r/MSCS posts to be useful for sorting discussions. The examples read so far
include both direct requests, such as submitting GRE scores, and mixed requests
about admission chances and whether a degree is worthwhile, so perfect accuracy
would be unrealistic for a model trained on roughly 200 posts.

---

## 2. Every label has an F1 score of at least 0.55

Each of the four labels must have a held-out **F1 score of at least 0.55** in
each run, using the notebook's per-label precision and recall calculations.

**Why this target:**

Strong results on profile evaluations should not conceal failure to recognize
admission updates or degree decisions. With 200 posts divided among four labels,
some labels may have only a handful of examples in a 15% test split, so 0.55 is
a deliberately lower floor than the overall accuracy target while still
requiring both useful precision and recall.

---

## 3. At most 25% confusion across the hardest boundary

Among test posts whose true label is `profile_calibration` or `degree_tradeoff`,
**at most 25%** may be predicted as the other label in each run. Calculate this
as the sum of the two cross-label confusion-matrix cells divided by the total
number of true test posts in those two classes. Errors into the other classes
are still covered by criteria 1 and 2.

**Why this target:**

The quant-offer post asks about both admission odds and turning down a job,
which makes this boundary especially easy to confuse. Allowing at most one in
four posts to cross this boundary tests whether the model distinguishes
“Where can I get in?” from “Is graduate school worth it?” instead of treating
every detailed applicant profile as `profile_calibration`.

---

## 4. At least 30 examples per label, with no label above 50%

Before splitting, the labelled dataset must contain **at least 200 distinct
posts**, with **at least 30 posts in each label** and **no label exceeding 50%**
of the dataset.

**Why this target:**

Profile evaluations and application-tactic questions recur in the initial
posts and could dominate collection. A minimum of 30 examples gives every
label some training coverage, while the 50% cap prevents one common request
from becoming a majority label; even 30 examples still yield only about
four or five test examples, which must be acknowledged when interpreting F1.

---

## 5. Confident predictions are at least 10 percentage points more accurate

In each run, accuracy on the **most-confident third** of test predictions must
exceed accuracy on the **least-confident third by at least 0.10**. Use the
notebook's confidence report, which ranks posts by their highest predicted
class probability and compares equally sized groups.

**Why this target:**

Some posts make a narrow request, while others combine university fit, career
plans, and financial concerns. A gap of at least ten percentage points would
show that the confidence ranking helps prioritize uncertain posts for manual
review; it does not establish that the probability values are calibrated.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
