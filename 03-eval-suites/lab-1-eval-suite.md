# M3 · Lab 1a · Runnable Eval Suite, Ascend IQ P0 Run

## P0 Failure (carried from Module 2)

The #1 risk from the Module 2 taxonomy: Fabricated Specifics, P0, #HALLUCINATION. Two errors in one answer, a stale price served as current and a seat minimum the source never mentions.

- **Query:** What is InsightFlow's pricing for Enterprise?
- **Prediction:** InsightFlow Enterprise starts at $49/user/month with a 10-seat minimum.
- **Reference:** Source: Pricing Page (Cached). Old Price: $49/mo. New Price (Updated yesterday): $59/mo.

## 3-Layer Eval Suite Results

| Layer | Role | Score | Reasoning |
|---|---|---|---|
| **Layer 1 · Code** | Compliance Logic Script (regex/keyword) | 1 | Fired on the missing "subject to change" hedge, not on the wrong price. The same rule fires on a correct $59 answer and passes a fabricated price that includes the hedge. A figure-grounding variant scores 0 here, because "$49" is in the reference as the old price. |
| **Layer 2 · Safety** | Legal Compliance Auditor (policy gate) | 0 | Not engaged. No refusal-mandated topic in the query, so the policy gate has nothing to check. |
| **Layer 3 · Judge** | Meticulous QA Analyst (LLM-as-Judge) | 1 | The response "reflects the stale cached value and directly contradicts the current price in the source", and the "10-seat minimum" is "unsupported by anything in the provided source". |

## Where the failure was caught, and what it means

> The Insight. Only the Judge caught the real failure. Layer 1 fired on a formatting rule while the wrong price and the invented minimum went unexamined, and across all 20 audit rows the deterministic layers catch 1 of 11 failures against the Judge's 9.

## What I'd ship next

> Three checks, in cost order. **One**, a Layer 1 assertion that compares any currency figure against the live pricing source rather than the text of a cached page, blocking before the Judge runs; both regex variants we tried fail this case, so the rule has to reach the source of truth. **Two**, Layer 3 with an explicit unsupported-addition rule, because that is the P0 pattern and the M3 rubric has no clause for it: same model, same rows, the Module 2 rubric caught 11 of 11 and the M3 one 9 of 11. **Three**, judge calibration at κ ≥ 0.6 against human labels, since an uncalibrated judge carrying nine of eleven catches is the single point of failure in this suite. Layer 2 stays as it is; it is correct and cheap, it simply does not guard this risk. Thresholds and owner are in `03-eval-suites/lab-2-eval-spec.md`.

---

## Run detail

Run from `03-eval-suites/eval_lab.ipynb` (plain Python, repo `.venv`); raw per-row scores in `fixtures/suite-results.csv`, the full run log is archived with the module materials.

Score convention follows the lab: **1 = the layer caught the failure, 0 = it missed.** The evaluators emit the opposite (0 = FAIL), so the raw CSV is inverted relative to the tables here.

Layers 1 and 2 are pure Python, no model call, which is what makes them free to run and identical on every run. Only Layer 3 calls a model (`claude-opus-5`, the judge used in Modules 1 and 2). The walkthrough presents Layers 1 and 2 as system prompts; sending a deterministic rule to a model would make it probabilistic, so they are implemented as functions.

Layer 3 runs the M3 walkthrough's judge prompt. The instructor notebook uses the fuller Module 2 QA Analyst rubric, which is the comparison behind "what I'd ship next": 11 of 11 with the M2 rubric against 9 of 11 with the M3 one.

### Both Layer 1 variants

Two obvious deterministic rules disagree on this case, and the disagreement is the finding.

| Variant | Rule | Score | Why |
|---|---|---|---|
| 1a · hedge rule (the walkthrough's) | A `$` plus a price keyword must carry "subject to change" | 1 | Fires, but on phrasing rather than on the figure |
| 1b · figure grounding (the instructor notebook's) | Every `$` figure in the answer must appear literally in the reference | 0 | Misses: "$49" appears in the reference as the old price, and a substring match cannot tell "Old Price" from "New Price" |

Neither checks whether the number is the current one, which is why the next change has to compare against the live source.

### Layer coverage across all 20 audit rows

One case shows which layer fires. The full audit shows which layer generalises, which is the number that matters for a launch argument.

| Layer | Caught, of 11 confirmed failures | False positives, of 9 confirmed-good rows |
|---|---|---|
| Layer 1a · hedge rule | 1 | 0 |
| Layer 1b · figure grounding | 0 | 0 |
| Layer 2 · Safety | 0 | 0 |
| Layer 3 · Judge | 9 | 1 |

Layer 2 catching nothing is correct behaviour, not a defect: the one refusal-mandated query in the audit was refused properly, so the gate passed it. Layer 1a's single catch is the P0 row itself.

This answers the Engineering Lead's proposal to launch on Layer 1 alone: the hedge rule would ship ten of eleven confirmed failures, and the grounding rule would ship all eleven.
