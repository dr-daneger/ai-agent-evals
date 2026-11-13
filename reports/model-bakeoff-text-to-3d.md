# Text-to-3D model bake-off: 11 models, 12 blind briefs

Dane Stanfield (GitHub: dr-daneger). Run on 2026-09-24.

## Finding

3 frontier models formed 1 tier, with scores of 82.0 to 87.6 out of 100.
Each of the 3 exported closed solids on 7 of 9 print briefs.
The next model scored 63.8.
Equal weights on the 5 score terms give the same order.

## The question

Fabulora is a web app that turns a text prompt into a 3D scene.
In print mode it exports a watertight mesh sized for a 3D printer.
A language model writes the code that builds the geometry.
The app runs that code in a sandboxed frame and exports the mesh.

The question: which models produce a printable object that matches a written brief?

## The setting

The benchmark is SDS v8 (Standard Data Set, version 8).
It holds 12 blind briefs: 9 print and 3 creative.
Each brief gives a size envelope per axis and 4 criteria.
Opus 5.5 wrote the briefs before any model generated against them.
The briefs were frozen by a SHA-256 (Secure Hash Algorithm) hash.

Every model got the same prompt contract.
Reasoning effort was set to low wherever a model offers that control.
Gemini 3 ran at its minimal setting.
Models with no effort control ran with reasoning off.

| Group | Models | Samples per brief | Cells |
|---|---|---|---|
| Frontier | Fable 5.1, GPT-6 Astra, Opus 5.5 | 1 | 36 |
| Slower open-weight | Kimi K3, GLM 5.3 Flash, DeepSeek V4.1 Flash | 1 | 36 |
| Cheaper | Gemini 3.0 Flash, GPT-6 Luna, Gemma 4 31B, Gemini 3.1 Flash-Lite, Qwen 3.8 Flash | 2 | 120 |
| All | 11 | 1 or 2 | 192 |

Some reasoning models ran out of output tokens at the product cap of 8,192.
Those models re-ran with a cap of 32,768.
Failures stayed in the results as failures.
Opus 5.5 refused 1 brief.
3 later retries of that brief did not refuse.
DeepSeek ran past 32,768 tokens on 4 briefs, and GLM on 1.

## Method

Score = 100 x (0.30 P + 0.30 R + 0.20 B + 0.10 D + 0.10 K)

| Term | Weight | What it measures | Who decides |
|---|---|---|---|
| P, printable | 0.30 | Print briefs: the exported mesh is a closed solid. Creative briefs: the code runs. | Deterministic check |
| R, rubric | 0.30 | Pass, partial or fail on each of the brief's 4 criteria | Judge agent |
| B, pairwise | 0.20 | Bradley-Terry strength, as mean win probability against the other 10 models | Judge agent |
| D, dimensions and orientation | 0.10 | Mean of the size check and the per-axis orientation check | Deterministic check |
| K, parameters | 0.10 | The product's parameter-rebuild gate passes | Deterministic check |

The printability check requires a closed solid of positive volume that fits the printer bed.
It ran on the raw export, before any mesh repair.
P, D and K carry 50% of the score and involve no judge.

Judging was blind.
Each brief got 1 fresh judge agent.
The judge saw only the brief, lettered candidate sheets and pair images.
The key from letters to models was kept apart.

Each brief had 55 pair images, 1 for every pair of the 11 models.
The pairs used each model's first sample.
The left and right sides were random.
12 briefs gave 660 pairwise games.

The Bradley-Terry model fits 1 strength per model from the win and loss record.
The chance that model i beats model j is s_i / (s_i + s_j).

A side-bias check:

| Outcome | Games |
|---|---|
| Left won | 303 |
| Right won | 314 |
| Tie | 43 |
| Total | 660 |

Left won 49% of the 617 decisive games.

## Results

| # | Model | Samples | Score | P | R | B | D | K | Print briefs closed |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Fable 5.1 | 1 | 87.6 | 0.83 | 0.91 | 0.81 | 0.92 | 1.00 | 7 of 9 |
| 2 | GPT-6 Astra | 1 | 86.3 | 0.83 | 0.91 | 0.83 | 0.75 | 1.00 | 7 of 9 |
| 3 | Opus 5.5 | 1 | 82.0 | 0.83 | 0.83 | 0.77 | 0.75 | 0.92 | 7 of 9 |
| 4 | Kimi K3 | 1 | 63.8 | 0.33 | 0.81 | 0.55 | 0.83 | 1.00 | 1 of 9 |
| 5 | Gemini 3.0 Flash | 2 | 60.5 | 0.62 | 0.63 | 0.43 | 0.50 | 0.92 | 9 of 18 |
| 6 | GPT-6 Luna | 2 | 59.3 | 0.46 | 0.74 | 0.43 | 0.62 | 0.83 | 5 of 18 |
| 7 | GLM 5.3 Flash | 1 | 56.3 | 0.50 | 0.62 | 0.46 | 0.58 | 0.75 | 3 of 9 |
| 8 | DeepSeek V4.1 Flash | 1 | 52.6 | 0.42 | 0.59 | 0.53 | 0.50 | 0.67 | 3 of 9 |
| 9 | Gemma 4 31B | 2 | 49.3 | 0.50 | 0.58 | 0.32 | 0.21 | 0.83 | 6 of 18 |
| 10 | Gemini 3.1 Flash-Lite | 2 | 36.1 | 0.46 | 0.36 | 0.21 | 0.21 | 0.50 | 5 of 18 |
| 11 | Qwen 3.8 Flash | 2 | 31.1 | 0.33 | 0.32 | 0.14 | 0.21 | 0.67 | 2 of 18 |

- Kimi K3 scored 0.81 on the rubric and closed 1 of 9 print briefs.
- Gemini 3.0 Flash closed 9 of 18, the most among the cheaper models.
- Pairwise brief wins were spread. DeepSeek won 2 briefs outright and Kimi won 1.

## Re-judging

After the run, a text-extraction fault was found.
9 saved candidates had shown stray text over the scene in their images.
Fresh blind judges re-judged those 9 briefs on 2026-09-26.
A later re-judge on 2026-09-28 added a 12th model, Sonnet 5.5.
It also added a coherence term and changed the weights.

| Judging | Models | Games | Top 3 scores | 4th place |
|---|---|---|---|---|
| 2026-09-24 (this report) | 11 | 660 | 87.6, 86.3, 82.0 | Kimi K3, 63.8 |
| 2026-09-26 re-judge | 11 | 660 | 86.8, 86.7, 83.7 | Kimi K3, 65.4 |
| 2026-09-28 re-judge | 12 | 792 | 89.0, 86.4, 82.5 | Sonnet 5.5, 74.0 |

The same 3 models held the top 3 places in all 3 judgings.
The first 2 places swapped in the 2026-09-26 re-judge.
The before-and-after gave a judge-noise estimate of about 0.07 on a win share.

## Why the method looks this way

Earlier rounds taught 3 lessons:

1. A contract check saturated at 147 of 147 and stopped telling models apart.
2. A judge agent was misled by a metric label.
3. One model rendered 4 of 4 holdout objects and exported 0 of 4 printable meshes.

So P measures the exported mesh, and the judges see no labels.

## Limits

- Each cell has 1 or 2 samples. The report gives no confidence intervals.
- The order within the top 3 is inside the judge noise.
- The judges share a model family with 1 contestant, Opus 5.5. The judges were blind to labels.
- The same model family wrote the briefs.
- Half the score is deterministic and involves no judge.
- Printability is measured before mesh repair. The product's repair step was not applied.
- The weights were set by the author. Equal weights give the same order.
- The run used 12 briefs, 1 prompt contract and 1 reasoning-effort setting.

## What would make the result stronger

- 3 or more samples per cell for every model, with bootstrap confidence intervals over briefs and samples.
- Judges from 2 or more model families outside the contestant set, with their agreement reported.
- Briefs written by a person or by a model outside the contestant set.
- A "printable after repair" column next to the raw score.
- A sweep of reasoning effort for each model.
- A physical print of a sample of objects.

## Records

- Journal entry `journal/2026-09-24.md` on branch `sds-v8` of the private Fabulora repository.
- Results directory `bakeoff_results/sds_v8_20260924/` at commit `d3a0abf`.
- Re-judges at commits `4a312c4` (2026-09-26) and `1ac9731` (2026-09-28).
- Side counts and cell counts were recounted from the verdict files for this report.
