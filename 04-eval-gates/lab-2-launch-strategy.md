# Module 4 · Launch Strategy · Section 4.0 Release Criteria

_Generated from the M4 Launch Strategy Builder. Drop this into your PRD as Section 4.0._

## 4.0 Release Criteria

The following thresholds must be met by Model Candidate v1.x before approval for production deploy. Eval Specs from Module 3 define the measurement methodology.

| Severity | Metric | Threshold | Dataset | Method |
|---|---|---|---|---|
| 🔴 Hard (Blocker) | Hallucination rate per claim (fabricated specifics) | = 0%: zero unsupported claims in 300 | 300-claim held-out audit | M3 Eval Spec (`03-eval-suites/lab-2-eval-spec.md`): LLM judge calibrated at κ 0.824, plus a code check of every price against the live pricing source. Not the 20-row beta log: zero failures in 20 only bounds the rate under 15%, zero in 300 bounds it under 1% at 95% confidence. The 300-claim audit does not exist yet, which is the schedule risk for launch |
| 🟡 Soft (Review) | Latency, P95 | < 2.0s, PM override documented | Staging load test on the production query mix | Server-side timing |
| 🔵 Advisory (Monitor) | Brand voice in drafted emails | ≥ 4.0 / 5, slang-list hits flagged at the PR | Sample of drafted outbound emails | LLM judge on a tone rubric, plus the PR keyword check |

## 4.1 CI Gate Policy

These thresholds run in a GitHub Actions gate on every pull request, replaying deterministic fixtures from the regression golden set (≥ 30 cases). PM owns the policy; Engineering owns the YAML.

> Per dimension, never one blended score. Faithfulness (floor 95, max regression 3), task completion (89, 3), tool selection (87, 5) and safety (98, 1) block the merge; latency (70, 3) and cost (70, 5) warn only. A full flip of one case moves a score 3.3 points on the 30-case set, so every blocking dimension except tool selection allows no case to flip, which matches the rule that any P0 or P1 regression fails the gate. Floors sit at most one case below the `main` baseline, so slow erosion across small PRs still trips the gate. Inputs and retrieved context are frozen fixtures replayed on every run, so a red check reflects the change rather than a different input. Applied to PR #218, this policy blocks the merge on a 9-point faithfulness regression that a blended score would have passed, and warns on the latency and cost regressions that the template thresholds hid (`04-eval-gates/lab-ci-gate-policy.md`).

## 4.2 Mitigation Plan · Soft Gate

**Selected Lever:** Staged Rollout

> If our Soft Gate fails (answers take 4.2 seconds against our 2-second target), we recommend **Staged Rollout** because the risk is that real customer questions behave differently from our test set, and a first wave of 10 of the 50 accounts shows us the real speed before everyone gets it. Four seconds still beats the hours these clients spend digging today, and if the wait does put people off, it affects a fifth of the accounts rather than all fifty. A beta label would not limit who is affected, and a delay would give up the quarter over speed rather than accuracy. Accuracy still has to pass in full before the first account gets access.

---

_Lab artifact for Module 4, AI Evals Certification, Product School. Becomes the Eval Gates slide of the Final Project deck._
