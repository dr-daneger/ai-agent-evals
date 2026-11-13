# AI agent evals

Dane Stanfield (GitHub: dr-daneger)

2 short reports on empirical tests of AI agents and models.
Each report states the question, the method, the results, the limits and the next steps.

## Findings

**Held-out mutation exams.**
6 agent-written test suites had passed a mutation gate on mutants their author saw.
On 15 new mutants per suite that the author never saw, the suites killed 0 to 8.
[Read the report](reports/held-out-mutation-exams.md).

**Text-to-3D model evaluation.**
11 models built 3D objects for 12 blind briefs, scored by a deterministic printability check and 660 blind pairwise games.
3 frontier models formed 1 tier at 82.0 to 87.6 of 100, each closing 7 of 9 print briefs.
[Read the report](reports/text-to-3d-model-evaluation.md).

## Authorship

I designed the experiments, set the gates and the stopping rules, and decided what counted as a result.
AI coding agents wrote the code and ran the runs under my direction.

## Data

The underlying code and records are private.
The reports hold aggregate counts and method facts only.

## License

MIT. See [LICENSE](LICENSE).
