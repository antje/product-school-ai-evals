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

> Per dimension, never one blended score. Faithfulness (floor 95, max regression 3), task completion (89, 3), tool selection (87, 5) and safety (98, 1) block the merge; latency (70, 3) and cost (70, 5) warn only. A full flip of one case moves a score 3.3 points on the 30-case set, so every blocking dimension except tool selection allows no case to flip, which matches the rule that any P0 or P1 regression fails the gate. Floors sit at most one case below the `main` baseline, so slow erosion across small PRs still trips the gate. Fixtures are replayed, not re-generated, so a red check reflects the change rather than model variance. Applied to PR #218, this policy blocks the merge on a 9-point faithfulness regression that a blended score would have passed, and warns on the latency and cost regressions that the template thresholds hid (`04-eval-gates/lab-ci-gate-policy.md`).

## 4.2 Mitigation Plan · Soft Gate

**Selected Lever:** Staged Rollout

> If our Soft Gate fails (P95 latency is 4.2s against the 2.0s target), we recommend **Staged Rollout** because a first wave of 10 of the 50 accounts gets answers that still beat hours of manual digging, measures latency on real production queries rather than a staging load, and caps any latency-driven drop-off at a fifth of the cohort before the rest see it. A feature flag is all or nothing, a beta label limits no exposure, and a delay gives up the quarter for a speed problem. The Hard gate still has to pass before the first wave ships.

---

_Lab artifact for Module 4, AI Evals Certification, Product School. Becomes the Eval Gates slide of the Final Project deck._
