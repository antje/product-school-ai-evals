# Lab, CI Eval Gate Policy (Ascend IQ PR #218)

| Dimension | main | PR | Δ | Floor | Max reg | Blocking | Result |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Faithfulness (grounding) | 96 | 87 | -9 | 90 | 3 | yes | ✕ FAIL |
| Task completion | 92 | 93 | 1 | 85 | 5 | yes | ✓ pass |
| Tool selection | 90 | 88 | -2 | 80 | 5 | yes | ✓ pass |
| Safety / policy | 99 | 99 | 0 | 98 | 1 | yes | ✓ pass |
| Latency (p95) | 84 | 80 | -4 | 70 | 8 | no | ✓ pass |
| Cost per task | 88 | 82 | -6 | 70 | 10 | no | ✓ pass |

**Gate result:** ⛔ BLOCKED, required dimension regressed past policy

## Merge decision

BLOCK the merge. Faithfulness fell 9 points, three times the 3-point limit, and landed at 87, below the 90 floor. Faithfulness is the grounding dimension, the one that guards the Module 2 P0 (fabricated specifics), so it is blocking and there is no override at the PR level. The other five dimensions pass. Latency and cost regressed but are warn-only by design: the Module 1 trade-off puts factual integrity ahead of speed, and cost is tracked rather than gated at the PR.

The fix goes back to the retrieval prompt this PR changed, not to the thresholds.

---

## How to read the thresholds

The golden set has 30 cases, so **one case is 3.3 points**. Read in case units, the policy says:

| Dimension | Max reg | In case units |
|---|---:|---|
| Faithfulness | 3 | No case may flip. Matches the golden-set rule that any P0 regression fails the gate |
| Safety / policy | 1 | No case may flip |
| Task completion, tool selection | 5 | One case may flip |
| Latency, cost | 8, 10 | Two to three cases' worth, and warn-only |

Faithfulness's -9 is about three cases that were grounded on main and are not on the PR.

Tool selection stays at 5 rather than tightening to 3. A stricter setting would block a PR whenever one case picks a different tool, including cases where the different tool still reaches the right answer. The tool choices that matter show up as a task-completion drop, and task completion is blocking; that is how the Module 3 trajectory `T-01-A` failed, on both dimensions at once.

## Why per-dimension, not blended

Averaged across all six dimensions, main scores 91.5 and the PR 88.2, a drop of 3.3 points, about one case. A blended gate with any tolerance above one case would merge this PR, shipping three newly ungrounded answers behind a small gain in task completion. The per-dimension policy is what makes the faithfulness regression visible.

CI replays deterministic fixtures for this, not live model calls, so the same PR produces the same scores on every run and a red check means the change, not the model's variance.
