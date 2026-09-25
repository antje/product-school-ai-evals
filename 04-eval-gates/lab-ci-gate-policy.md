# Lab, CI Eval Gate Policy (Ascend IQ PR #218)

| Dimension | main | PR | Δ | Floor | Max reg | Blocking | Result |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Faithfulness (grounding) | 96 | 87 | -9 | 95 | 3 | yes | ✕ FAIL |
| Task completion | 92 | 93 | 1 | 89 | 3 | yes | ✓ pass |
| Tool selection | 90 | 88 | -2 | 87 | 5 | yes | ✓ pass |
| Safety / policy | 99 | 99 | 0 | 98 | 1 | yes | ✓ pass |
| Latency (p95) | 84 | 80 | -4 | 70 | 3 | no | ! warn |
| Cost per task | 88 | 82 | -6 | 70 | 5 | no | ! warn |

**Gate result:** ⛔ BLOCKED, required dimension regressed past policy

## Merge decision

BLOCK the merge. Faithfulness fell 9 points, three times the 3-point limit, and landed at 87, below the 95 floor. Faithfulness guards the Module 2 P0 (fabricated specifics), so it is blocking and there is no override at the PR level. The other three blocking dimensions pass. Latency (-4) and cost (-6) both trip their warnings: they do not block, because the Module 1 trade-off puts factual integrity ahead of speed, but the developer reworking the retrieval prompt should know this change also made answers slower and costlier.

The fix goes back to the retrieval prompt this PR changed, not to the thresholds.

---

## How the thresholds were set

The template ships a floor and a max regression per dimension. All six were reviewed against the Module 4 rules and our earlier modules, and five were changed.

Two facts frame every number. The golden set has 30 cases, so **a full flip of one case moves a score by 3.3 points** (the scores are averaged per-case scores, so a partial change on one case moves them less). And the course rule is that **any regression on a P0 or P1 case fails the gate, while P2 warns.**

From those, two rules replace the template's mix:

- **Max regression.** Blocking dimensions allow less than one full flip, so no P0 or P1 case can be lost in a single PR. Warn-only dimensions get tight numbers too, because a warning that never fires tells nobody anything.
- **Floor.** The `main` baseline minus at most one case, so slow erosion across several small PRs, each inside its max regression, still hits the floor. The template floors sat 6 to 10 points below `main`, which let two to three cases erode without the gate firing.

| Dimension | Template | Ours | Why |
|---|---|---|---|
| Faithfulness | floor 90, max 3, block | floor **95**, max 3, block | The P0. `main` is 96, and a floor of 90 tolerates roughly three ungrounded cases while the Module 3 spec says zero. 95 leaves a point of slack for measurement |
| Task completion | floor 85, max 5, block | floor **89**, max **3**, block | A P1: the Module 3 trajectory `T-01-A` gave a plausible answer from an unfinished path. Max 5 would let a full P1 case flip |
| Tool selection | floor 80, max 5, block | floor **87**, max 5, block | Max 5 stays: a different but valid tool is not a regression, and a tool choice that matters shows up as a task-completion drop. The floor rises to stop erosion |
| Safety / policy | floor 98, max 1, block | unchanged | Already baseline minus one point and under a full flip. This dimension also covers off-scope tool calls, which task completion does not catch: the Module 3 trace `T-06-C` finished its task with an off-scope call |
| Latency (p95) | floor 70, max 8, warn | floor 70, max **3**, warn | Warn-only by the Module 1 trade-off. At max 8 this PR's -4 is silent; at 3 it warns, which is early notice ahead of the staging Soft gate that caught the 4.2s answer |
| Cost per task | floor 70, max 10, warn | floor 70, max **5**, warn | Warn-only, but the eval budget is capped, so a 6-point cost regression should be visible on the PR |

The merge call is the same under either policy. What changed: the two warnings now fire, and tool selection passes at 88 against a floor of 87, so the next PR that drops it by two points trips the floor.

## Why per-dimension, not blended

Averaged across all six dimensions, `main` scores 91.5 and the PR 88.2, a drop of 3.3 points, the size of one full case flip. A blended gate with any tolerance above one case would merge this PR, shipping three cases' worth of lost grounding behind a small gain in task completion. The per-dimension policy is what makes the faithfulness regression visible.

CI replays deterministic fixtures for this, not live model calls, so the same PR produces the same scores on every run and a red check means the change rather than the model's variance.
