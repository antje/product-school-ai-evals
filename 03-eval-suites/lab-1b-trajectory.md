# Lab, Trajectory Eval (Ascend IQ usage-drop task)

Grades the agent's **path**, not just the final answer. Scored in `03-eval-suites/eval_lab.ipynb` against `trajectory-traces.csv`. Dimensions 1, 2, 3 and 5 are checked in code (set comparison, argument equality, duplicate detection, precedence); 4 and 6 lean on the fixture's `expected_flag` column, because the signal for recovery and grounding is in the prose observations rather than in a typed error.

**Matching mode:** unordered, plus one precedence rule: `draft_reply` must come after `compare_weeks`.
**Score:** 1/6 · **Verdict:** HOLD

Task: an Enterprise weekly report shows active users down 30%. Pull four weeks of usage, check for a data-ingestion gap, compare week over week, draft a reply explaining the cause.

Reference path (`T-GOLD-A`): `get_account` → `get_usage(weeks=4)` → `get_ingestion_status` → `compare_weeks` → `draft_reply`.
Graded path (`T-01-A`): `get_account` → `get_usage(weeks=4)` → `get_usage(weeks=4)` → `search_web("SaaS weekly active users seasonal dip")` → (no ingestion check, no comparison) → `draft_reply("Your 30% dip looks like a seasonal trend...")`.

Why unordered: for this P0 the risk is an unverified cause, which is a missing-step problem rather than an ordering problem. Strict matching on a research task creates noise, since pulling usage and checking ingestion are independent. The one precedence rule covers the ordering failure that does matter, drafting a conclusion before verifying it, which is how `T-03-A` fails.

## Dimension scores

| Dimension | Score | Note |
|---|---|---|
| Tool selection | FAIL | Never called `get_ingestion_status` or `compare_weeks`, and added an off-scope `search_web` that answers the question from the open web instead of the account's data. |
| Argument correctness | PASS | `account_id="ACME-2231"` and `weeks=4` match the request. The only clean dimension. |
| No redundant / looping steps | FAIL | `get_usage` called twice with identical arguments; the second call returns the same result and buys nothing. |
| Recovery | FAIL | Nothing errored, so there was no step to recover from, yet the run ends with an unverified conclusion and no attempt to check it. Recorded as FAIL rather than PASS because the trace never recovers the missing verification. |
| Plan coherence | FAIL | Drafted the reply with no verification step anywhere in the path. |
| Task completion | FAIL | The job was to explain the cause. The reply names "seasonal" as the cause without ever testing it, and the golden path shows the real cause is a three-day ingestion gap. |

## Verdict

HOLD. The reply reads fine, so an output-only eval would ship it. The agent guessed a cause that the data contradicts, and the path shows it never had the evidence to make that claim. On a P0 where the promise is an answer a VP can use without checking the source, a plausible answer from a broken path is the failure mode, not an edge case.

## Second scorecard, `T-04-A`, the case that changes the argument

| Dimension | Score | Note |
|---|---|---|
| Tool selection | PASS | Full reference set, no off-scope calls. |
| Argument correctness | PASS | Arguments match the request. |
| No redundant / looping steps | FAIL | `compare_weeks` re-run three times with identical arguments. |
| Recovery | PASS | No failed step. |
| Plan coherence | PASS | Respects the precedence rule. |
| Task completion | PASS | Correct, grounded reply: the dip is a three-day ingestion gap. |

5/6, verdict SHIP with a cost defect. Worth keeping in the deck because it separates correctness from efficiency, which a single pass or fail hides. `T-01-A` is wrong; `T-04-A` is right and wasteful. A gate that blocks both equally would block a correct answer, and a gate that passes both would ship a guess. That is why the scorecard is six dimensions rather than one score.

## What the fixture cannot score, and why that matters

`trajectory-traces.csv` ships a golden path for TASK-A only. `T-05-B` (billing dispute) and `T-06-C` (Salesforce sync) are therefore not scorable: dimensions 1 and 6 need a reference set to compare against, and the precedence rule names TASK-A's verification step. Scoring them anyway produced a false 6/6 for `T-05-B`, because "missing tools" of an empty reference set is always empty, and a false plan-coherence failure for `T-06-C`. The notebook now reports both as not scorable instead.

That is the real cost of trajectory evals: every task needs its own reference path, written by someone who knows what good looks like. Output evals scale across tasks with one rubric; trajectory evals scale with one reference per task. That is the number to carry into the Module 5 budget.
