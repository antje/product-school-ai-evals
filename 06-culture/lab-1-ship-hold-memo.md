# Ship/Hold Memo · Ascend IQ

> **Decision:** 🛑 HOLD

**To:** CPO · cc Eng Lead · Trust & Safety  
**From:** Antje Barth · AI Evals Cohort · Oct 1, 2026

## The Answer

I recommend we hold the Ascend IQ launch to our top 50 accounts until it passes the zero-fabrication audit, because in our beta 6 of 20 answers stated a detail the source does not support, and shipping now puts more than $2.5M of annual renewals in front of an answer a VP has a 97% chance of catching out within ten questions.

## The Arguments

### 1. The failure we would ship is the one clients pay us to prevent

Ascend IQ is sold on one promise: an answer a VP can put in front of their board without checking the source. Our top failure breaks exactly that. The agent invents specifics (a seat minimum, a "confirmed" speaker, a funding stage) and drops the qualifier that makes a true fact false ("native" export, "seamless" integration). None of the beta answers cite a source, so the VP has no way to catch it before the board does.

### 2. The exposure lands on our most valuable accounts, all at once, where we can't take it back

The launch cohort is the 50 accounts that matter most to Q4 retention. At the beta rate, nearly every one of them meets a fabricated detail in the first week. And an invented number doesn't stay in our product: it goes into a client's board deck, so we learn about it from them.

### 3. We can't yet prove it's fixed, but we know exactly what would

The pieces are in place: a judge that agrees with human reviewers (κ 0.824), a CI gate that already blocked a regressing change (PR #218), and continuous coverage funded for fabrication and attribution. What's missing is the proof itself, the 300-claim audit. The hold is a measurement we can schedule, not an open-ended fix.

## Evidence · Trust Metrics

```
- Fabricated specifics: 6 of 20 beta answers (Gate: 0 unsupported claims in a 300-claim held-out audit) FAIL · 02-failure-discovery/failure-taxonomy.md
- All hallucination failures: 9 of 20 beta answers (fabricated specifics 6, dropped qualifiers 3); 11 of 20 failing overall, the 11th a P2 brand-voice miss outside the top three (Gate: 0) FAIL · 02-failure-discovery/audit-log.md
- Answers citing a source: 0 of 20 (Gate: every answer shows its sources) FAIL · 03-eval-suites/lab-2-eval-spec.md
- Hard release gate, 300-claim audit: not yet built; dropped qualifiers count as unsupported claims (Gate: 0 in 300, bounds the per-claim rate under 1% at 95%) NOT MEASURED · 04-eval-gates/lab-2-launch-strategy.md
- CI faithfulness on PR #218: 87, down 9 from main (Gate: floor 95, max regression 3) FAIL, merge blocked · 04-eval-gates/lab-ci-gate-policy.md
- Latency P95 on the measured case: 4.2s (Gate: ≤ 2.0s Soft, 10s ceiling) FAIL, override band · 04-eval-gates/lab-1-gate-map.md
- Usage-drop trajectory T-01-A: 1 of 6 dimensions (Gate: path-aware verdict) HOLD · 03-eval-suites/lab-1b-trajectory.md
- Judge agreement with humans: κ 0.824 on the 12 calibration traces the rubric was tuned on (Gate: κ ≥ 0.6 on a held-out set) PASS on the tuning set, held-out NOT MEASURED · 03-eval-suites/lab-judge-calibration.md
- Judge coverage of confirmed failures: 9 of 11, code layers 1 of 11 (Gate: none, informational) · 03-eval-suites/lab-1-eval-suite.md
- Continuous coverage funded: fabrication + attribution at Level 3, $150K (Gate: ≤ $200K, ≤ 3 slots) PASS · 05-scale/lab-2-budget-crisis.md
```

## Business Risk

**If we ship now:** the 50 launch accounts carry at least $2.5M of annual renewals (50 at the $50k floor). At the beta rate a VP asking ten questions has a 97% chance of meeting a fabricated detail, so nearly every account meets one in the first week, and it surfaces in their board deck rather than in our logs. That is also the end of "verified" as the reason they pay.

**If we hold:** the cost is time, not trust. The engagement problem Ascend IQ was built to fix continues while we hold. With the plan below, the first wave ships Nov 9, a few weeks later than the November target, still before Q4 renewal conversations, and with a measured fabrication rate instead of a hope.

**What the audit buys, and what it doesn't:** passing it bounds the per-claim rate under 1%, not at zero. At about ten claims an answer, that still allows up to one answer in ten to carry an error. That residual is why inline citations and the pre-send hold are launch conditions rather than extras: a flagged answer is held before it is sent, and anything that gets past the check carries the source a VP can open.

## Next Step · Decision Needed

Approve the hold and this plan by **Friday, Oct 2**, and ask Sales to stop promoting Ascend IQ to key accounts until the go/no-go. Engineering ships the fixes by Oct 23: an unsupported-addition rule in the judge, a pre-send check that holds flagged answers (inside the latency budget), inline citations, a live-price check, and a rule that the agent verifies a cause before it drafts a reply, the step the usage-drop trajectory skipped. Before the audit, the judge is recalibrated on a held-out labelled set, since its κ of 0.824 was measured on the traces the rubric was tuned on. We run the 300-claim audit from Oct 26 and I bring you the result on **Nov 9**. If it passes, the first 10 accounts go live that day. If it fails, we fix and re-run on a fresh 300, with no partial launch; a second failure comes back to you with a revised date, and Sales keeps the pause.

**After Nov 9:** I own the go/no-go and the expansion call. Each account sits behind a feature flag. The first wave is the 10 accounts that asked the most questions in the beta, and the Level 3 judge already funded for fabrication and attribution scores their production answers continuously. Expansion to all 50 needs 14 days with zero human-confirmed unsupported claims in that sampling and P95 latency inside the Soft gate. The Eng Lead on call turns the flag off for the wave on the first confirmed fabrication, and the affected account hears from us within one business day with the corrected figure and its source.

## Reflection

_Defining "good enough" turned out to mean defining it in numbers with a sample size behind them. Twenty rows told me Ascend IQ had a fabrication problem, but they could not tell me whether a fix had worked, which is why the hold ends in an audit and not a date. It also forced me to treat the judge as part of the eval: one rubric paragraph moved its agreement with human reviewers from 0.286 to 0.824. And the product's own promise set the bar. "Without checking the source" is why one invented detail fails a whole answer._
