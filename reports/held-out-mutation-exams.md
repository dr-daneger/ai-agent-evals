# Held-out mutation exams for agent-written test suites

Dane Stanfield (GitHub: dr-daneger). Experiment run on 2026-10-08.

## Finding

6 agent-written test suites faced an exam of 15 new mutants each.
The author of each suite had never seen these mutants.
The suites killed 0 to 8 of the 15.
Across all 6 suites, the tests killed 16 of 90 unseen mutants.

| Author model | Suites | Unseen mutants killed, per suite | Total killed |
|---|---|---|---|
| Frontier model (Opus) | 2 | 8, 2 | 10 of 30 |
| Small model (Haiku) | 4 | 3, 3, 0, 0 | 6 of 60 |
| All | 6 | 0 to 8 | 16 of 90 |

Every suite had already been through a mutation gate.
In that gate, the author saw the planted mutants while fixing the tests.
The examiner rated all 6 suites as "does not generalize".
The parser outputs matched the source documents in every spot check.
The weakness was in the tests.

## The question

Mutation testing plants a small deliberate bug in the code.
That bug is a mutant.
A test suite kills a mutant when at least 1 test fails.

In this pipeline, a reviewer plants named mutants and the author must kill them.
The author sees which mutants survive and adds tests.
That loop can teach to the test.
Goodhart's law names the risk: a measure that becomes a target stops measuring well.

The question: do tests that kill the seen mutants also kill mutants nobody showed the author?

## The setting

The code is a set of statement parsers in a private data pipeline.
Each parser reads documents and writes structured rows.
AI coding agents write each parser and its tests.

The pipeline gates work by risk tier.
The author never gates their own work.
A reviewer plants named mutants and confirms each one is killed.
A stopping rule bounds recheck rounds by the severity of new findings.

The code and data are private.
This report holds counts and method facts only.

## Method

Each suite got 1 examiner agent.
The examiner was Opus 5.5 at high reasoning effort.
The examiners ran in parallel.

Blinding was by instruction.
Each examiner was told not to read prior reviews, gate files, mutant lists, verdicts or commit messages.
It formed its view from the code, the tests, the specification, the output and the source documents.

Each examiner wrote exactly 15 new mutants as exact-string patches.
The mutants targeted code that the suite's change had added or modified.
The brief named the mutant kinds.
These were flipped operators, boundaries, dropped checks, swapped fields, wrong signs and wrong date fields.
They also covered count checks, missing fields and skipped quarantine of unknown rows.

A shared runner applied each mutant and ran only that suite's own test files.
The runner had 3 rules:

1. Each test set must pass on the unmutated code first.
2. Python bytecode is disabled, so a stale cache cannot run unmutated code.
3. A pytest usage error or an empty test selection (exit codes 3 to 5) is invalid. It never counts as a kill.

About 770 mutant checks were re-run by hand to check the runner.
They gave 0 mismatches.

Each examiner then classed every surviving mutant.
A survivor was either equivalent (no change in behavior) or a real blind spot.
The examiner rated the suite on a fixed rule:

- "yes": no real blind spot survives and the spot checks agree.
- "partly": 1 or 2 real blind spots.
- "no": any other case.

Each examiner also compared at least 10 output values with the text of the source documents.

The run held an author-swap control.
4 suites came from the small model through a review loop.
2 suites came from the frontier model.
All 6 got the same protocol in the same run.

## Results

| Suite | Author model | Killed of 15 | Survived | Invalid | Examiner rating |
|---|---|---|---|---|---|
| F1 | Frontier (Opus) | 8 | 7 | 0 | no |
| F2 | Frontier (Opus) | 2 | 13 | 0 | no |
| S1 | Small (Haiku) | 3 | 12 | 0 | no |
| S2 | Small (Haiku) | 3 | 12 | 0 | no |
| S3 | Small (Haiku) | 0 | 15 | 0 | no |
| S4 | Small (Haiku) | 0 | 15 | 0 | no |

- 0 of 90 mutants were invalid.
- The best suite killed 8 of 15.
- 2 suites killed none.
- The output spot checks found 0 mismatches in all 6 suites.

The suites passed their gate and produced correct output.
5 of the 6 suites still let at least 12 of 15 unseen mutants survive.

## The earlier exam and the runner fix

An earlier exam ran on the same day.
It examined 3 other suites from the small model.
Its kill counts were 15, 2 and 1.

Reviewers then found 3 faults in the first runner:

1. Stale bytecode ran unmutated code, which produced false survivals.
2. Usage errors and empty test selections counted as kills.
3. Tests that already failed before mutation were trusted.

A fix was committed at 20:08 local time.
The corrected exam started at 20:11.
The headline uses the corrected run only.
The faults pushed kill counts in both directions, so the earlier counts are not comparable.

## Limits

- The control has 2 frontier-authored suites. This report makes no claim about which model writes better tests.
- Blinding was by instruction only. No access control kept the examiner away from gate files.
- The control suites had also been exposed to gate mutants. The run cannot separate "saw the gate mutants" from "author model".
- The first exam ran before the runner fix. 2 of its suites were never re-measured.
- 1 examiner wrote the mutants for each suite. Mutant difficulty depends on that examiner.
- The examiner shares a model family with the frontier author.
- 15 mutants per suite gives a coarse scale. 1 kill moves the rate by about 7 points.

## What would make the result stronger

- A matched design: the same parser, with suites written with and without sight of the gate mutants. That isolates teaching to the test.
- At least 10 suites per author model, in balanced arms.
- Blinding by access control: the examiner works in a copy with no gate files.
- 2 or more examiners per suite from different model families, with their agreement reported.
- A standard mutation tool as a cross-check on agent-written mutants.
- A pre-registered kill threshold and analysis plan.
- A re-run of the first-exam suites on the corrected runner.

## Records

- Corrected exam: workflow run `wf_687f3aeb-197` (6 examiner agents, 2026-10-08).
- First exam: workflow run `wf_c09e823a-cc7` (3 examiner agents, 2026-10-08).
- Runner fix: commit `41d22532` in the private pipeline repository.
- The records are private because they sit beside private data.
