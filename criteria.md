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

## 1.

Held-out accuracy is at least 0.67 on every seed (42, 7, and 2024).

**Why this target:** The test split is about 30 posts. 0.67 is 20 of those 30. A coin flip across procedure, case, and verdict is about 10 of the 30. I want the worst of the three seeds to still be a result I would use on a new post.

---

## 2.

Every label has an F1 of at least 0.55 on every seed.

**Why this target:** procedure and verdict are the pair my rule was written for, so those posts will get swapped. This results in a higher expected miss rate, so this bar is lower than my accuracy bar because of that.



---

## 3.

No label is more than 50% of labels.csv.

**Why this target:** I have not collected the 200 posts yet. The checklist already flags a label over 70%, because the model can look accurate by always guessing the big label. 50% is the share where I will stop and go find more of the other two.



---

## 4.

On every seed, the most-sure third of the test posts is right at least 0.2 more often than the least-sure third.

**Why this target:** Section 5 prints those two thirds, about 10 posts each. 0.2 is 2 extra correct posts in the sure third. One extra post is luck, I want a gap big enough that a sure prediction is actually safer.



---

## 5.

Macro F1 is at least 0.6 on every seed.

**Why this target:** Macro F1 is the average of the procedure, case, and verdict F1 scores. I set 0.6 because it is in between my accuracy bar and my per label F1. Anything lower than the per label F1 is usless because it would pass whenever #2 passes, and the 0.67 is a little high because a run of 0.55, 0.55, and 0.65 averages to 0.58, passing #2 but not this bar.



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
