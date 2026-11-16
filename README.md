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
https://www.reddit.com/r/Theatre/
https://www.reddit.com/r/Theatre/
https://www.reddit.com/r/Theatre/

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



---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `label_one`

**Definition:**

**Example 1:**
>

**Example 2:**
>

### `label_two`

**Definition:**

**Example 1:**
>

**Example 2:**
>

### The hardest boundary

**Which two labels:**

**The decision rule I used every time:**
<!-- e.g. "If the post names a specific checkable fact, it's `analysis`, even
     if the tone is heated." -->



---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:**

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->

**Counts per label:**

| Label | Count | Share |
|---|---|---|
|  |  |  |
|  |  |  |
|  |  |  |
| **Total** |  | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

**2.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

**3.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:**

**Settings:** <!-- epochs, learning rate, batch size, seed -->

**Anything I changed from the defaults, and why:**

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->



---

## How I Used AI

 Only 5 of my 23 posts had labels, and those used an older taxonomy (detailed/vague). I had Claude label all 23 against the definitions in label_definitions.txt and fix the CSV quoting. I checked the result loaded correctly. Claude also helped draft this write-up from my results.

**Moment 1**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Moment 2**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Pre-labelling disclosure:**

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.500 | 0.500 | +0.000 |
| Macro F1 | 0.389 | 0.222 | -0.167 |
| F1 — `analysis` | 0.667 | 0.000 | -0.667 |
| F1 — `hot_take` | 0.000 | 0.000 | +0.000 |
| F1 — `reaction` | 0.500 | 0.667 | +0.167 |

Both scored on the same 4 held-out posts from test_split.csv. Baseline is facebook/bart-large-mnli reading label_definitions.txt. Trained is distilbert-base-uncased, seed 42, on mps (Apple Silicon GPU).

**What I predicted before I looked:** see the committed note in notes.md.

**What the gap actually means:** Fine-tuning added nothing. With 23 posts the trained model learned to answer `reaction` every time, so it tied the baseline on accuracy and lost on macro F1. The baseline at least got the one `analysis` post right by reading my definitions.

---

## Run Log — Before

Device: mps (Apple Silicon GPU), torch 2.15.0. Test split is 4 posts per seed (1 analysis, 1 hot_take, 2 reaction).

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. References the source 5/5 times | 5/5 | not measurable | not measurable | not measurable | NOT TESTABLE |
| 2. No more than 60% of predictions go to one label | ≤ 60% | 100% reaction | 100% reaction | 100% reaction | MISSED |
| 3. Agreement with staff labels | ≥ 75% | not run | not run | not run | NOT TESTED |
| 4. Confidence above 65% on 4 of 5 predictions | 4/5 | not checked | not checked | not checked | NOT TESTED |
| 5. Every label F1 | ≥ 0.69 | 0.00 / 0.00 / 0.67 | 0.00 / 0.00 / 0.67 | 0.00 / 0.00 / 0.67 | MISSED |

F1 order is analysis / hot_take / reaction. Accuracy was 0.500 and macro F1 0.222 on all three seeds. The spread is zero, but that is not stability. The model predicted `reaction` for every post on every seed.

### Confusion matrix (seed 42)

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 0 | 0 | 1 |
| **hot_take** | 0 | 0 | 1 |
| **reaction** | 0 | 0 | 2 |

**My biggest off-diagonal number, and what it means:** Every non-reaction post went to `reaction` and nothing went the other way. The model never learned a boundary at all. It collapsed onto my majority label.

---

## Verdicts and Diagnoses

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | References the source | 5/5 | NOT TESTABLE | A classifier outputs a label, not a source, so there is nothing to check. |
| 2 | Share of predictions to one label | ≤ 60% | MISSED | 100% of predictions were `reaction` on all three seeds. |
| 3 | Agreement with staff | ≥ 75% | NOT TESTED | I did not finish Milestone 3. |
| 4 | Confident predictions | 4 of 5 above 0.65 | NOT TESTED | I did not pull per-prediction confidence before stopping. |
| 5 | Every label F1 | ≥ 0.69 | MISSED | analysis and hot_take were 0.00 on every seed. |

**Diagnoses**

- **Criteria 2 and 5: too few examples.** Training had 3 analysis and 2 hot_take posts against 10 reaction. Validation loss barely moved (1.084 to 1.068 on seed 42) and validation accuracy stayed at 0.50 every epoch, so the model never separated the labels and just guessed the majority. I can't rule out a labeling problem because I didn't finish the staff agreement check, which is the only tool that would separate the two. With this little data, though, the model never had enough to show a labeling problem anyway.
- **Criteria 1 and 4: the criteria were written badly.** Criterion 1 asks for something a classifier can't do. Criterion 4's wording doesn't say what's being counted. I've added revisions under each in criteria.md.

---

## What's Still Broken

- **Criteria 2 and 5 (missed):** I would label more posts for `analysis` and `hot_take`, at least 20 each, and rerun the three seeds. I stopped at 23 posts on purpose to see what a classifier does with almost no data. The answer is that it guesses the majority label every time.
- **Criterion 3 (not tested):** I would label the 30 staff posts under the staff taxonomy and run agreement.py. I stopped because I ran out of time.
- **Criterion 4 (not tested):** I would pull the confidence numbers from results_three_seeds_before.json and check them against the revised criterion. I ran out of time.
- **Milestone 5:** I did not make an improvement. The change my diagnosis points to is adding examples to the thin labels.

**The gap between what I meant my labels to capture and what the model learned:** I meant the labels to separate people asking how to run a show, newcomers asking how to start, and people sharing opinions. The confusion matrix shows the model learned none of that and only learned that most posts are `reaction`.