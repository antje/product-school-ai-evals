# M3 · Lab 2 · Eval Spec, Ascend IQ P0

## Part 1 · The 5-Part Eval Spec

| Question | Answer |
|---|---|
| **01 · Target Risk** | Fabricated Specifics: the agent states a specific the source does not support or contradicts. Observed as an invented seat minimum, a stale price served as current, a "confirmed" speaker who is tentative, an unsupported funding stage, a hex code that is not in the brand guidelines. |
| **Risk Type** | Output. The trajectory risk from Lab 1b is a separate spec; its scorecard is in `03-eval-suites/lab-1b-trajectory.md`. |
| **Trust Metric** | Hallucination rate, measured per claim rather than per answer. |
| **02 · Evaluator** | Hybrid. Layer 3 LLM-as-Judge with an explicit unsupported-addition rule, a Layer 1 code assertion on figures that must match the live source, and human review on a sample to keep the judge honest. |
| **Detection logic** | Split the answer into factual claims. Each claim must trace to a span in the retrieved source. A claim with no span, or a span that says something different, fails the answer. Currency figures are additionally compared against the live pricing source, not the cached page text, because both regex variants we tried pass a stale price that appears in the source as an old value. |
| **03 · Threshold** | Launch gate: zero unsupported claims in a 300-claim held-out audit. Zero of 300 bounds the true rate below 1% at 95% confidence (rule of three, 3/300). Steady-state target: 0.26% per answer, derived from 40 questions per VP per month and a tolerance of fewer than 10% of VPs meeting one fabrication in month one; proving that bound needs about 1,150 claims, so it is a post-launch target rather than a launch gate. Judge itself: Cohen's κ ≥ 0.6 against human labels, re-measured on every rubric or model change. |
| **Strategy** | Safety First (maximise TPR). We accept the judge flagging good answers, because a missed fabrication is the contract-ending one and a hedged answer is not. |
| **04 · Business Stakes** | Each caught fabrication ends the promise this product is sold on, an answer a VP can use without checking the source, for an account paying $50k+ a year. At the audit's rate of 6 in 20 answers, a VP asking ten questions has a 97% chance of carrying one into a board deck, so the 50-account launch cohort meets it in week one. |
| **05 · Owner** | Group PM owns the gate and the ship or hold sign-off. The Ascend IQ engineering lead owns keeping it green in CI. |

## Part 2 · Three Audience Messages

### A. For Engineering (Jira ticket)

**AIQ-412: Block unsupported factual claims before they reach the user**

GIVEN an Ascend IQ answer and the source spans retrieved for it,
WHEN any factual claim in the answer has no matching span, or contradicts its span,
THEN the answer is not returned as-is: the claim is removed, the answer is served with a note that a detail was not in our sources, and the event is logged with the claim, the query and the retrieved span ids.

GIVEN a price-shaped claim (a currency amount plus one of user, month, plan, seat),
WHEN the amount does not equal the current value in the live pricing source,
THEN block deterministically at Layer 1, before the judge runs.

GIVEN the nightly eval run,
WHEN the held-out audit reports any unsupported claim in 300,
THEN the build is red and the release does not promote.

Also required: judge agreement with human labels at κ ≥ 0.6, re-measured whenever the rubric or the judge model changes.

### B. For UX / Design

When the gate fires, the user must not see a blank, an error, or a silent omission. Three states to design:

1. **Claim removed.** The answer is served without the unsupported claim, plus one line, "One detail was removed because it is not in our sources", and a link to what was retrieved.
2. **Nothing verifiable left.** The agent says what it checked and what it could not confirm, then offers the source list to open by hand. This is a real answer, not a failure page.
3. **Stale source.** When the live source has a newer value than the cached one, show the current value with its as-of date.

Every answer shows its sources inline. None of the 20 audited beta answers cites a source today, while every reference does, which is why a VP cannot catch a fabrication before their board does. Safety First means more of these states than we would like; the cost of that is an answer that feels more careful, and the cost of the alternative is the renewal.

### C. For Leadership (bi-weekly update)

Ascend IQ's top risk is fabricated specifics: 6 of 20 audited beta answers stated a detail the source does not support, and at that rate a VP asking ten questions has a 97% chance of carrying one into a board deck. We have a per-claim grounding gate wired into CI. The launch bar is zero unsupported claims in a 300-claim held-out audit, which bounds the rate below 1% with 95% confidence and protects at least $2.5M of annual renewals across the 50-account cohort (50 accounts at the $50k floor). Tracking: per-claim fabrication rate, judge agreement (κ), and the share of answers that ship with sources shown. The audit is the schedule risk rather than the fix: 300 claims is the smallest sample that can clear the bar, and the 20 rows we have cannot.

---

_Save this to `03-eval-suites/lab-2-eval-spec.md` in your repo (the repo is your submission). It underpins the Eval Results slide of your final pitch deck._
