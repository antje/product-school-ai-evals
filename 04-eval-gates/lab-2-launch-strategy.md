# Module 4 · Launch Strategy · Section 4.0 Release Criteria

## 4.0 Release Criteria

The following thresholds must be met by Model Candidate v1.x before approval for production deploy. Eval Specs from Module 3 define the measurement methodology.

| Severity | Metric | Threshold | Dataset | Method |
|---|---|---|---|---|
| 🔴 Hard (Blocker) | Hallucination rate per claim (fabricated specifics) | = 0%: zero unsupported claims in 300 | 300-claim held-out audit | M3 Eval Spec (`03-eval-suites/lab-2-eval-spec.md`): LLM judge calibrated at κ 0.824, plus a code check of every price against the live pricing source |
| 🟡 Soft (Review) | Latency, P95 | < 2.0s, PM override documented | Staging load test on the production query mix | Server-side timing |
| 🔵 Advisory (Monitor) | Brand voice in drafted emails | ≥ 4.0 / 5, slang-list hits flagged at the PR | Sample of drafted outbound emails | LLM judge on a tone rubric, plus the PR keyword check |

**Dataset note.** The builder labels every row `Ascend_IQ_Logs`, the 20-row beta audit. That set cannot prove the Hard threshold: 20 rows cannot tell a 5% fabrication rate from a 20% one, and zero failures in 20 only bounds the true rate under 15%. The Hard gate needs the 300-claim held-out audit, where zero failures bounds the rate under 1% at 95% confidence. That audit does not exist yet, and building it is the schedule risk for next month's launch.

## 4.1 CI Gate Policy

These thresholds run in a GitHub Actions gate on every pull request, replaying deterministic fixtures from the regression golden set (≥ 30 cases). PM owns the policy; Engineering owns the YAML.

> Per dimension, never one blended score. Faithfulness (floor 90, max regression 3), task completion (85, 5), tool selection (80, 5) and safety (98, 1) block the merge; latency (70, 8) and cost (70, 10) warn only. At 30 cases one case is 3.3 points, so faithfulness and safety allow no case to flip, which matches the rule that any P0 regression fails the gate. Fixtures are replayed, not re-generated, so a red check reflects the change rather than model variance. Applied to PR #218, this policy blocks the merge on a 9-point faithfulness regression that a blended score would have passed (`04-eval-gates/lab-ci-gate-policy.md`).

## 4.2 Mitigation Plan · Soft Gate

**Selected Lever:** Staged Rollout

> If our Soft Gate fails (P95 latency is 4.2s against the 2.0s target), we recommend **Staged Rollout** because a first wave of 10 of the 50 accounts gets answers that still beat hours of manual digging, measures latency on real production queries rather than a staging load, and caps any latency-driven drop-off at a fifth of the cohort before the rest see it.

The alternatives were weaker for this failure. A feature flag is a kill switch but all or nothing. A beta label sets expectations without limiting exposure. Delaying the launch gives up the quarter for a failure that is about speed, not correctness. The staged rollout only covers the Soft gate: the Hard gate still has to pass before the first wave ships.

---

_Lab artifact for Module 4, AI Evals Certification, Product School. Becomes the Eval Gates slide of the Final Project deck._
