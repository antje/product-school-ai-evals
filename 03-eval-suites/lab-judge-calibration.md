# Lab, Judge Calibration (Ascend IQ grounding rubric)

> Repo file `ai-evals/03-eval-suites/lab-judge-calibration.md`. Twelve grounding traces from the M3 Judge Calibration tool, in `fixtures/calibration-traces.csv` with my labels and the tool's canned judge labels. Measured in `03-eval-suites/eval_lab.ipynb`; judge `claude-opus-5`.

**Cohen's κ:** 0.824 (near-perfect) on the third rubric, PASSES the κ ≥ 0.60 gate. The first two rubrics failed it.

| Judge | κ | Raw agreement p₀ | Chance agreement pₑ | Disagreements |
|---|---|---|---|---|
| The tool's canned judge | -0.286 (worse than chance) | 50.0% | 61.1% | 6/12 |
| Ours, rubric v1, "is the answer grounded?" | 0.286 (fair) | 58.3% | 41.7% | 5/12 |
| Ours, rubric v2, abstention made explicit | 0.286 (fair) | 58.3% | 41.7% | 5/12 |
| Ours, rubric v3, evidence scope stated | 0.824 (near-perfect) | 91.7% | 52.8% | 1/12 |

- Traces labeled: 12/12. My labels: 8 PASS, 4 FAIL (T-02, T-03, T-07, T-09).

### Confusion matrix (judge × me), rubric v3

| | Me: PASS | Me: FAIL |
|---|---|---|
| **Judge: PASS** | 7 | 0 |
| **Judge: FAIL** | 1 | 4 |

### Confusion matrix (judge × me), the tool's canned judge

| | Me: PASS | Me: FAIL |
|---|---|---|
| **Judge: PASS** | 6 | 4 |
| **Judge: FAIL** | 2 | 0 |

## Diagnosis

Two miscalibrations, and only one is the textbook case.

**The tool's judge rewards fluency and punishes honesty.** It passes all four ungrounded answers: an invented annual renewal (T-02), SLA penalty figures that are not in the retrieved doc (T-03), a Salesforce integration absent from the workspace config (T-07), and a confident 12% tiered discount that appears nowhere in the source (T-09). It fails both honest abstentions (T-06, T-10), where saying "I could not find that field" is the correct grounded behaviour. Its κ of -0.286 is worse than chance. That is the worst outcome for an evaluator, because it is systematically inverted rather than noisy, so trusting it would train the product toward fluent fabrication.

**Our judge had the opposite problem, and its cause was the data rather than a length bias.** All five of its disagreements (T-01, T-04, T-05, T-08, T-11) are the same shape: it failed answers whose claims are fine, because the fixture only *describes* the retrieved source ("matches the retrieved usage record") and never includes it. The judge refused to accept a description of evidence as evidence. Rubric v2, which spelled out that abstention is a pass, changed nothing, because v1 already passed both abstentions. The rubric never said which part of the trace counts as the source.

## Rubric revision

v3 adds one paragraph naming the evidence scope:

> What counts as evidence in these traces: the answer's own description of the retrieved source is authoritative for what the source contains. When the answer says a figure matches the retrieved record, treat the source as containing that figure and judge only whether the claims are consistent with what the answer says the source holds. FAIL when the answer asserts something its own description says is absent, not in the source, or contradicted.

κ moves from 0.286 to 0.824. One disagreement survives: T-05, where the judge wants the underlying weekly figures before it accepts a computed 12% drop. That is defensible rather than wrong, and in production it is the right instinct, since a derived number should be checkable. It stays on the record rather than being tuned away.

Two things this changes for the eval spec. First, κ has to be re-measured on every rubric edit, not just on a model change: two of these three rubrics differ by one paragraph and the κ differs by 0.54. Second, calibration needs traces that carry their retrieved spans, not summaries of them, or the judge is being graded on a question the data cannot answer.
