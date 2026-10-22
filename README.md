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

TakeMeter sorts top-level comments from r/pokemon, a general Pokémon subreddit where people share art, crafts, memes, news and discussion threads. Each comment gets one of four labels: `suggestion` (advice the reader can act on), `explanation` (a fact or how something works), `opinion` (a position on Pokémon in general), or `reaction` (a response to the post itself). The point of the project is to see whether a small fine-tuned model can learn a taxonomy that took two rounds of rule-tightening for a human to apply consistently.

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `suggestion`

**Definition:** Sincerely proposes something concrete the reader could do, whether phrased as an instruction ("you should…"), as the writer's own experience ("I've found that hosting helps…"), or as a wish for something specific the OP could make ("I want to see a Scyther scythe").

**Example 1:**
>You should play platinum and either heart gold or soul silver

**Example 2:**
>Sounds like you need to start playing some rom hacks. They are usually based off Gen III games so you’ll feel right at home while getting to experience some new stories / Pokémon

### `explanation`

**Definition:** States a fact or explains how or why something works (game mechanics, lore, data, how something was made), where the point is what's true rather than what the writer prefers.

**Example 1:**
>It should be noted that there is no Switch game that Spinda can come from and go into Home. Spinda is in BDSP, but because of a coding error, it can not go to Home. You can't even bring one from Pokemon Go because those are set patterns and as such, can not be transferred to Home.

**Example 2:**
>Officially, none of them get plural forms

### `opinion`

**Definition:** Takes a position on Pokémon in general (species, types, games, designs, official content, franchise decisions), including a favourite, a prediction, or a self-labelled headcanon, even when it's phrased as a joke.

**Example 1:**
>My favourite has always been and will always be Jynx. Totally dig Smoochum too. I know they have fans but … most people hate them and it’s unfortunate.

**Example 2:**
>Poison all the way, baby. I've never had as much fun playing competitive as I have with my monotype poison team. And there are just so many cool mons. Second is definitely ground.

### `reaction`

**Definition:** Responds to the post with a feeling, joke, genuine question, praise or criticism of fan-made content, a pleasantry, or a personal moment, memory or result, without explaining, advising, or taking a position on Pokémon in general.

**Example 1:**
>Such an elegant depiction of the pokemon. Lovely art!

**Example 2:**
>Zangoose -> Zangeese

### The hardest boundary

**Which two labels:** `explanation` vs `opinion`.

**The decision rule I used every time:**
<!-- e.g. "If the post names a specific checkable fact, it's `analysis`, even
     if the tone is heated." -->

If the takeaway is a judgement or preference, it's `opinion`, even with facts in it. It's `explanation` only if the takeaway is how something works or what's true. For a guess or a hedged claim, I ask: could it be checked against the games or canon, even in principle? If yes, it's `explanation` ("most Mystery Dungeon Pokémon eat berries?"). If it can only be agreed or disagreed with, it's `opinion` ("Lightning American was just early installment weirdness").

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** Top-level comments only (no replies) from r/pokemon. I took comments from art, media, discussion, info and venting posts. A first random pass turned up almost no suggestions and few explanations, so I then sampled on purpose from posts that ask for advice (which game to play, team help, Nuzlocke rules) and info posts with mechanics questions. Within each post I took comments in order rather than picking the best ones. Spam and self-promotion were dropped. Because of the targeted sampling, the label balance is more even than the subreddit's real mix, which is mostly reactions.

**How I labelled them:** Cold first. I labelled every comment myself, then had AI check each label against my written rules. Where we disagreed, I made the call and wrote it into the rules, then relabelled to match. 44 labels changed this way. 8 comments were pre-labelled by AI and checked by me. The `note` column in `labels.csv` records which is which (see the disclosure under How I Used AI).

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `reaction` | 66 | 28.6% |
| `suggestion` | 63 | 27.3% |
| `opinion` | 57 | 24.7% |
| `explanation` | 45 | 19.5% |
| **Total** | 231 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* "Thank god there's another Throh enjoyer out there, he's just so damn solid, his stats and moveset are so perfectly placed that during a playthrough he can easily match legendaries. And he's obtainable by the SECOND GYM. Love that guy"
>
> *Could have been:* `explanation` or `opinion`
>
> *I chose, because:* `opinion`. The stats and the second-gym fact are real, but they're there to justify "love that guy". When facts support a judgement, the judgement is the takeaway.

**2.**
> *The post:* "GODDAMMIT. Another perfect fakemon that will never get used because someone else made it before GF:("
>
> *Could have been:* `reaction` or `opinion`
>
> *I chose, because:* `opinion`. It praises a fan-made design, which on its own would be `reaction`, but it also takes an explicit position on a Game Freak policy. When a comment does both, I let the franchise position win.

**3.**
> *The post:* "Not really a lot of good fits. Naveen is just trying to do his thing despite what his family would want from him, Absol/M-Absol don't really have anything related to that… Griselle is hotheaded/aggressive/rude, M-Delphox on the other hand is very calm and collected, very mismatched…" (trimmed; the full comment is in `labels.csv`)
>
> *Could have been:* `reaction` or `opinion`
>
> *I chose, because:* `reaction`. It's long and full of facts about official characters, but every point judges the post's fan-made Mega pairings rather than anything Game Freak did. Judging fan-made content is `reaction`. I labelled this one `opinion` in my blind relabel check, so it's a boundary I don't apply perfectly.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** <!-- epochs, learning rate, batch size, seed --> 3 epochs, learning rate 2e-5, batch size 16, seed 42, max length 128 tokens.

**Anything I changed from the defaults, and why:** Nothing except `LABELS`, which I set to my four labels.

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. --> 161 train / 35 val / 35 test.

| Label | Train | Val | Test |
|---|---|---|---|
| `explanation` | 31 | 7 | 7 |
| `opinion` | 40 | 8 | 9 |
| `reaction` | 46 | 10 | 10 |
| `suggestion` | 44 | 10 | 9 |

`explanation` has only 7 examples in the test split, so its F1 will move a lot between seeds.

---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* 20 comments from my own set for a blind relabel, to check whether I was applying my rules consistently.
- *What came back:* 5 per label, shuffled, with my labels hidden. I matched my first labels on only 12 of 20 (60%). 5 of the 8 misses moved toward `opinion`, mostly hedged guesses about mechanics or lore that I'd first called `explanation`.
- *What I changed:* I added the canon test to my explanation-vs-opinion rule. I also settled two open calls: a fan-design comment that complains about Game Freak policy is `opinion`, and "Lightning American was just early installment weirdness" stays `opinion`. On a fresh set of 20 I then matched the rules on 20 of 20. That set was a little easier, since none of its comments had needed correcting before.

**Moment 2**

- *What I asked for:* How to collect around 200 comments when most of the subreddit is reactions.
- *What came back:* Per-label quotas instead of random sampling, targeting posts that ask for advice or have mechanics questions, a cap of about 8–10 comments per post so one topic doesn't dominate a label, and taking comments in order within a post.
- *What I changed:* I'd already found that random sampling gave almost no suggestions (9 in about 45 minutes of digging), so I kept all four labels and sampled advice and info posts on purpose instead of dropping `suggestion`. I didn't record each comment's post type or post ID, so I can't show that the per-post cap held.

**Pre-labelling disclosure:** I labelled 223 of the 231 comments myself first. After that, AI checked every label against my written rules, and I decided each disagreement. 176 cold labels stayed as they were. 44 were changed after AI review. 3 that I'd left blank or marked unsure were labelled after AI review. The other 8, from my targeted suggestion sample, were pre-labelled by AI, and I read and approved each one. AI also helped me draft and tighten the wording of the definitions and decision rules. The `note` column in `labels.csv` marks every row as `cold`, `changed … after AI review`, or `pre-labelled by AI, checked`.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline with Labels | Baseline without Labels | Trained | Difference between Trained and Untrained with Labels |
|---|---|---|---|---|
| Overall accuracy | 0.400 | 0.600 | 0.429 | +0.029 |
| Macro F1 | 0.299 | 0.596 | 0.278 | -0.022 |
| F1 — `explanation` | 0.471 | 0.471 | 0.000 | -0.471 |
| F1 — `opinion` | 0.545 | 0.615 | 0.000 | -0.545 |
| F1 — `reaction` | 0.182 | 0.667 | 0.519 | +0.337 |
| F1 — `suggestion` | 0.000 | 0.632 | 0.593 | +0.593 |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->
I predict the trained model will do better on the highly context dependent labels which rely a lot on the definition, boundaries and other rule constraints not in the label_definitions.txt document. Better than 0% on suggestion at least.

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->
Did better on suggestion at the cost of explanation and opinion completely. It seems like the label definitions aren't worth much (seems like a net negative mostly) without the additional rules developed in notes.md.

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
