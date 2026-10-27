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
The posts come from a BMW car forum called nam3forum.com, specifically the E46 (2001–2006) board. The classifier sorts each post into procedure, case, or verdict. Procedure names a part, a place on the car, a setting, or a spec the reader is supposed to use. Case describes what the car is doing, what code it threw, or what the owner already did, but the post does not tell the reader the next step. Verdict states a conclusion, or a shop or product preference, and does not give a part location, a spec, or this car's unresolved issue.


---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `procedure`

**Definition:** Names a part, a place on the car, a setting, or a spec, and that detail is what the reader is supposed to use.

**Example 1:**
> There are 3 TPS in the E46 M3 - one by the oil filter housing, one under the intake manifold and one in the gas pedal. I had this issue and started with the TPS by the oil filter housing, then swapped the gas pedal, and finally had to remove the intake manifold. It ended up being the TPS under the intake manifold for me. Careful not to strip the soft screws holding the TPS on.

**Example 2:**
> The allen screws are for caster adjustment. Those are set during an alignment and shouldn't need to be adjusted after. Camber adjustment is easy via the three lock nuts. When camber is adjusted, caster will not be affected much, but toe will change quite a bit.

### `case`

**Definition:** Describes what this car is doing, what code it threw, or what the owner already did, and does not tell the reader the next step.

**Example 1:**
> I believe code P1637 is the throttle position sensor, is there procedure to find the right starting point for the sensor? Thanks, Chris

**Example 2:**
> Did you ever find the sweet spot for the TPS by the oil filter? I have replaced both tps sensors, the pedal and the actuator and still have the eml light issue. Any help would be very appreciated

### `verdict`

**Definition:** States a conclusion, a shop, or a product preference, and does not give a part location, a spec, or this car's unresolved fault.

**Example 1:**
> No, this has to be re-done. Poor surface prep is clearly evident here. If left like this, it only going to get worse.

**Example 2:**
> ARS customs recently did my car and they did a good job with my car.

### The hardest boundary

**Which two labels:** `procedure` and `verdict`

**The decision rule I used every time:** I used `procedure` only when the post named a part, a place on the car, a setting, or a spec the reader could check or do. Naming the cause in ordinary words stayed `verdict`. A post about the writer's own car was `case` when it gave the symptom, the code, the mileage, or the work already done, and it did not tell the reader what to do. A reply that told a personal story and also said where to look or what to use was `procedure`.



---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** Public posts on the E46 (2001–2006) board at NA M3 Forums, https://nam3forum.com/forums/forum/main-forum/e46-2001-2006. I collected whole posts from the first page of repair and parts threads.

**How I labelled them:** I labeled `cold` the first 20 posts in the csv, then had my AI tool assign the label `pre-labeled` to the following 180 posts using the definitions and the decision rule in Label Taxonomy.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| procedure | 68 | 34% |
| case | 71 | 35.5% |
| verdict | 61 | 30.5% |
| **Total** | 200 | 100% |

**Three hard cases**

**1.**
> *The post:* "So don't use race pads on streets. And if anyone has advice on how to get rid of that coating, let me know."
>
> *Could have been:* case or verdict
>
> *I chose, because:* I used case. The rotors are grooved and the coating is still on the wheels, and he asks how to get it off. A verdict, under my definition, does not include this car's unresolved issue.

**2.**
> *The post:* "So yeah, don't run race brakes on a not race car. I also couldn't get the dust off the 163m wheels and ended up having them refinished."
>
> *Could have been:* case or verdict
>
> *I chose, because:* I used verdict. He already had the wheels refinished, and that conclusion is the point of the post.

**3.**
> *The post:* "The issue you saw is called servo motor hunting. Error = position command - position feedback. [...] In close-loop control, a loose feedback sensor or plays/backlash on the linkage from feedback device to the actuator is not allowed."
>
> *Could have been:* procedure or verdict
>
> *I chose, because:* Procedure. The reply describes a squence: servo hunting, error equation, and a loose feedback sensor is not allowed. That is a check the reader can do.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** distilbert-base-uncased

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, 128 tokens per post, seed 42.

**Anything I changed from the defaults, and why:** Nothing. The run used the notebook defaults.

**Device:** mps, Apple Silicon GPU (MPS), torch 2.14.1.

**Split sizes:** train 139, val 31, test 30.

| Label | Train | Val | Test |
|---|---|---|---|
| procedure | 47 | 11 | 10 |
| case | 49 | 11 | 11 |
| verdict | 43 | 9 | 9 |

The smallest test count is verdict, 9.



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* I had my AI tool label 180 posts after 20 that I labeled cold, using the definitions and the decision rule in Label Taxonomy.
- *What came back:* Each post was labeled procedure, case, or verdict, and the note was pre-labeled. The counts were 70 procedure, 70 case, and 60 verdict.
- *What I changed:* I changed 4 of the conclusions that came back: 2 procedures to Verdicts, one verdict to procedure, one procedure to case.

**Moment 2**

- *What I asked for:* I asked whether any labels were not definitive.
- *What came back:* It found 8 questions were not difinitively in any category, these were labeled case. Two more posts were found that could go either way.
- *What I changed:* I did not change the labels because there was no observable correct conclusion

**Pre-labelling disclosure:** An AI tool assigned 180 labels from the definitions and the decision rule in Label Taxonomy. I corrected 4 labels after reviewing the generated labels.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.333 | 0.500 | +0.167 |
| Macro F1 | 0.167 | 0.392 | +0.225 |
| F1 — `case` | 0.000 | 0.588 | +0.588 |
| F1 — `procedure` | 0.500 | 0.588 | +0.088 |
| F1 — `verdict` | 0.000 | 0.000 | +0.000 |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

The baseline should mix up procedure and verdict more than case, because posts on that boundary can match both definitions at once and the baseline never sees the pattern I used to differenciate. I expect the trained model's largest gain over the baseline to be on that boundary, if the labeled examples were enough to teach it.

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.500 | 0.400 | 0.400 | 0.100 |
| Macro F1 | 0.392 | 0.296 | 0.278 | 0.114 |
| F1 — `procedure` | 0.588 | 0.375 | 0.308 | 0.281 |
| F1 — `case` | 0.588 | 0.514 | 0.526 | 0.074 |
| F1 — `verdict` | 0.000 | 0.000 | 0.000 | 0.000 |

Accuracy moved 0.100 across seeds. Enough that a criterion landing within that band can't really be called either way.

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted | procedure | case | verdict |
|---|---|---|---|
| **procedure** | 5 | 5 | 0 |
| **case** | 1 | 10 | 0 |
| **verdict** | 1 | 8 | 0 |

Biggest confusion: 8 real `verdict` posts called `case`.
The other direction: 0 real `case` posts called `verdict`.

That's lopsided — the model leans toward `case` on this pair.
A direction like that usually points at your labelling rather than the
model. Your agreement report is the thing that can tell you which.

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

8 real verdict posts were called case, and 0 real case posts were called verdict.


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
