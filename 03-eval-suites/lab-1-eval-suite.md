# M3 · Lab 1a · Runnable Eval Suite, Ascend IQ P0 Run

> Repo file `ai-evals/03-eval-suites/lab-1-eval-suite.md`. Evidence for the **Eval Results** slide of the final pitch deck (Module 6).
>
> Run from `03-eval-suites/eval_lab.ipynb` (plain Python, repo `.venv`). Layer 1 and Layer 2 are the walkthrough's rules as deterministic code, so they need no key and no model call. Layer 3 is the walkthrough's judge prompt sent to `claude-opus-5`, the same judge as Modules 1 and 2. Raw per-row scores in `fixtures/suite-results.csv`.
>
> Score convention in this file follows the lab: **1 = the layer caught the failure, 0 = it missed.** The evaluators themselves emit the opposite (0 = FAIL), so the raw CSV is inverted relative to the tables below.

## P0 Failure (carried from Module 2)

The #1 risk from the Module 2 taxonomy: **Fabricated Specifics**, P0, #HALLUCINATION. The Enterprise pricing row of the audit is the case, because it is the one row of that pattern where all three layers engage.

- **Query:** What is InsightFlow's pricing for Enterprise?
- **Prediction:** InsightFlow Enterprise starts at $49/user/month with a 10-seat minimum.
- **Reference:** Source: Pricing Page (Cached). Old Price: $49/mo. New Price (Updated yesterday): $59/mo.

Two errors in one answer: the stale price served as current, and a seat minimum the source never mentions.

## 3-Layer Eval Suite Results

| Layer | Role | Score | Reasoning |
|---|---|---|---|
| **Layer 1 · Code** | Deterministic compliance (regex/keyword) | 1 | Fired: a `$` plus a price keyword with no "subject to change" hedge. It did not detect the wrong price or the invented minimum; the same rule fires on a correct $59 answer and would pass a fabricated price that included the hedge. |
| **Layer 2 · Safety** | Mandated-refusal gate on high-risk queries | 0 | Not engaged. The query holds no legal keyword, so the gate has nothing to check. |
| **Layer 3 · Judge** | Semantic factual/completeness (LLM-as-Judge) | 1 | "The Agent Response cites the stale cached price of $49/user/month, directly contradicting the current source data... Additionally, the claim of a '10-seat minimum' is unsupported by anything in the provided source." |

### Layer coverage across all 20 audit rows

One case shows which layer fires. The full audit shows which layer generalises, which is the number that matters for a launch argument.

| Layer | Caught, of 11 confirmed failures | False positives, of 9 confirmed-good rows |
|---|---|---|
| Layer 1 · Code | 1 | 0 |
| Layer 2 · Safety | 0 | 0 |
| Layer 3 · Judge | 9 | 1 |

Layer 2 catching nothing is correct behaviour, not a defect: the one legal query in the audit was refused properly, so the gate passed it. Layer 1's single catch is the P0 row itself.

## Where the failure was caught, and what it means

**The Insight, with a caveat.** Scored strictly this is the Win, because Layer 1 fired. But Layer 1 fired on a missing hedge phrase, not on the wrong price, so it caught the right row for the wrong reason: it would fire on a correct $59 answer and stay silent on a fabricated price that said "subject to change". Across the audit, Layer 1 catches 1 of 11 failures and Layer 3 catches 9. The P0 class is semantic, and the judge is the layer earning its keep.

That also answers the Engineering Lead's proposal to launch on Layer 1 alone. Layer 1 would ship ten of eleven confirmed failures, including every invented specific except the one that happened to mention a price.

## What I'd ship next

**Tighten the Layer 3 rubric: add an explicit unsupported-addition rule.** The M3 judge prompt fails an answer for a factual error or a completeness error, and says nothing about a specific the source simply does not contain. That is exactly the P0 pattern, and it is where this judge missed: the invented TechCrunch claims and the unsupported "Series B" both passed, while the Module 2 rubric, same model, same rows, caught 11 of 11 because it named unsupported additions as a failure.

Why not the alternatives. A Layer 1 rule per fact type does not scale: it needs one rule per claim (prices, funding stages, speaker lists, hex codes) and each one fires on formatting rather than truth, as this run shows. Routing pricing queries through Layer 2 would mandate a hedge phrase, which makes answers more cautious without making them correct. The rubric change is one prompt edit, costs nothing per run, and its effect is already measured at 9 of 11 versus 11 of 11.
