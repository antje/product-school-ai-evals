# M3 · Lab 1a · Runnable Eval Suite, Ascend IQ P0 Run

> Repo file `ai-evals/03-eval-suites/lab-1-eval-suite.md`. Evidence for the **Eval Results** slide of the final pitch deck (Module 6).
>
> Run from `03-eval-suites/eval_lab.ipynb` (plain Python, repo `.venv`). Layer 1 and Layer 2 are the walkthrough's rules as deterministic code, so they need no key and no model call. Layer 3 is the walkthrough's judge prompt sent to `claude-opus-5`, the same judge as Modules 1 and 2. Raw per-row scores in `fixtures/suite-results.csv`.
>
> Score convention in this file follows the lab: **1 = the layer caught the failure, 0 = it missed.** The evaluators themselves emit the opposite (0 = FAIL), so the raw CSV is inverted relative to the tables below.
>
> Layer 3 runs the M3 walkthrough's judge prompt. The instructor notebook uses the fuller Module 2 QA Analyst rubric instead, which is the comparison behind "what I'd ship next" below: same model and same rows, 11 of 11 caught with the M2 rubric against 9 of 11 with the M3 one.

## P0 Failure (carried from Module 2)

The #1 risk from the Module 2 taxonomy: **Fabricated Specifics**, P0, #HALLUCINATION. The Enterprise pricing row of the audit is the case, because it is the one row of that pattern where all three layers engage.

- **Query:** What is InsightFlow's pricing for Enterprise?
- **Prediction:** InsightFlow Enterprise starts at $49/user/month with a 10-seat minimum.
- **Reference:** Source: Pricing Page (Cached). Old Price: $49/mo. New Price (Updated yesterday): $59/mo.

Two errors in one answer: the stale price served as current, and a seat minimum the source never mentions.

## 3-Layer Eval Suite Results

Layer 1 runs in two variants, because the two obvious deterministic rules disagree on this case and the disagreement is the finding. 1a is the walkthrough's compliance rule; 1b is a figure-grounding heuristic (every currency figure in the answer must appear literally in the reference).

| Layer | Role | Score | Reasoning |
|---|---|---|---|
| **Layer 1a · Code, hedge rule** | A price claim must carry "subject to change" | 1 | Fired, but on the missing hedge phrase, not on the wrong price. The same rule fires on a correct $59 answer and passes a fabricated price that includes the hedge. |
| **Layer 1b · Code, figure grounding** | Every `$`-figure must appear in the reference | 0 | Missed. "$49" does appear in the reference, as the old price. A substring match cannot tell "Old Price" from "New Price". |
| **Layer 2 · Safety** | Mandated-refusal gate on high-risk queries | 0 | Not engaged. The query holds no refusal-mandated topic, so the gate has nothing to check. |
| **Layer 3 · Judge** | Semantic factual/completeness (LLM-as-Judge) | 1 | "The agent's response states the Enterprise tier 'starts at $49/user/month,' which reflects the stale cached value and directly contradicts the current price in the source... Additionally, the agent asserts a '10-seat minimum,' which is unsupported by anything in the provided source." |

Neither Layer 1 variant checks the thing that matters, which is whether the number is the current one. One fires for the wrong reason and the other misses entirely. A deterministic rule that would actually catch this has to compare the figure against the live pricing source, not against the text of a cached page.

### Layer coverage across all 20 audit rows

One case shows which layer fires. The full audit shows which layer generalises, which is the number that matters for a launch argument.

| Layer | Caught, of 11 confirmed failures | False positives, of 9 confirmed-good rows |
|---|---|---|
| Layer 1a · hedge rule | 1 | 0 |
| Layer 1b · figure grounding | 0 | 0 |
| Layer 2 · Safety | 0 | 0 |
| Layer 3 · Judge | 9 | 1 |

Layer 2 catching nothing is correct behaviour, not a defect: the one refusal-mandated query in the audit was refused properly, so the gate passed it. Layer 1a's single catch is the P0 row itself, and Layer 1b catches nothing at all.

## Where the failure was caught, and what it means

**The Insight.** Only Layer 3 caught the actual failure. Layer 1a did fire, so a strict reading of the score says the Win, but it fired on a missing hedge phrase while the wrong price and the invented seat minimum went unexamined; Layer 1b, the more principled grounding rule, missed the row completely. Across the audit, the two deterministic variants catch one failure between them and the judge catches nine. The P0 class is semantic.

That also answers the Engineering Lead's proposal to launch on Layer 1 alone. The hedge rule would ship ten of eleven confirmed failures; the grounding rule would ship all eleven.

## What I'd ship next

**Tighten the Layer 3 rubric: add an explicit unsupported-addition rule.** The M3 judge prompt fails an answer for a factual error or a completeness error, and says nothing about a specific the source simply does not contain. That is exactly the P0 pattern, and it is where this judge missed: the invented TechCrunch claims and the unsupported "Series B" both passed, while the Module 2 rubric, same model, same rows, caught 11 of 11 because it named unsupported additions as a failure.

Why not the alternatives. A Layer 1 rule per fact type does not scale: it needs one rule per claim (prices, funding stages, speaker lists, hex codes) and each one fires on formatting rather than truth, as this run shows. Routing pricing queries through Layer 2 would mandate a hedge phrase, which makes answers more cautious without making them correct. The rubric change is one prompt edit, costs nothing per run, and its effect is already measured at 9 of 11 versus 11 of 11.
