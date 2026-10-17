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

## 1. Overall accuracy

Overall accuracy is at least **0.80** on the held-out test split, on all three seeds.

**Why this target:** With four labels a random guesser gets 25%, and always guessing `Reaction` (about half of my random sample of 24) would get roughly 35–40% once I top up the thin labels. 0.80 is 24 of the ~30 test posts, so at most 6 wrong. It matches my consistency target (criterion 4): I'm asking the model to agree with my labels about as often as I expect to agree with myself, which is the most my labels can support.

---

## 2. Per-label performance

On every seed, `Reaction` and `Opinion` each have an F1 of at least **0.60**, `Suggestion` at least **0.45**, and `Explanation` at least **0.40**.

**Why this target:** `Reaction` and `Opinion` are my biggest labels, so they should do about as well as the model does overall. `Suggestion` gets a looser floor because it's thin (0 of 24 in my random sample, so it had to be sampled on purpose) and will only have about 5 test posts, where one miss moves F1 a lot. It still has strong surface cues like "you should" and "try", though. `Explanation` gets the loosest floor because it's thin *and* sits on my hardest boundary, where a post full of facts can still be an opinion.

---

## 3. Balance

No label is more than **40%** of `labels.csv`, and no label is less than **15%** (at least 30 of 200 posts).

**Why this target:** In my random sample `Reaction` came out at about 50% and `Suggestion` at 0%. Left alone, the model could learn to guess `Reaction` and still pass a weak accuracy target. The brief's 70% cap is too loose for four labels. 15% is my relaxed floor for `Suggestion` and `Explanation` if they turn out hard to find. A perfectly balanced split would be 25% each, and 15% still puts about 4–5 of each in the test split.

---

## 4. Consistency

When I re-label a random 30 of my own posts at least three days later, without looking at the original labels, I match my first label on at least **80%** of them (24 of 30).

**Why this target:** My decision checklist is ordered and is meant to give one answer per post, so most labels should come out the same the second time. But 5 of my first 24 sampled comments went into my Hard Cases as difficult two-label (about 20%), so I expect some of those to flip. If I'm under 80%, the problem is my rules, not the model.

---

## 5. Confidence

On every seed, accuracy on the model's most-confident third of test posts is at least **0.20** higher than on its least-confident third.

**Why this target:** With ~30 test posts each third is about 10 posts, so 0.20 means at least 2 more right out of 10. I expect short, clear posts like "Nice art" to be both confident and correct, and the opinion/explanation mixes to be the unsure ones. If the gap is smaller than this, the confidence score can't tell me which predictions to double-check.

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
