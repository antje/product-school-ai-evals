# Ship/Hold Memo · Ascend IQ

> **Decision:** 🛑 HOLD

**To:** CPO · cc Eng Lead · Trust & Safety  
**From:** Antje Barth · AI Evals Cohort · Oct 1, 2026

_Written on the Pyramid Principle: the answer first, three arguments that do not overlap, then the trust metrics that support them._

## The Answer

I recommend we hold the Ascend IQ launch to our top 50 accounts until it passes the zero-fabrication audit, with the go/no-go on Nov 9, because in our beta 6 of 20 answers stated a detail the source does not support, and shipping now puts more than $2.5M of annual renewals in front of an answer a VP has a 97% chance of catching out within ten questions.

## The Arguments

### 1. Brand risk: the failure we would ship is the one clients pay us to prevent

Ascend IQ is sold on one promise: an answer a VP can put in front of their board without checking the source. Our top failure breaks exactly that. The agent invents specifics (a seat minimum, a "confirmed" speaker, a funding stage) and drops the qualifier that makes a true fact false ("native" export, "seamless" integration). None of the beta answers cite a source, so the VP has no way to catch it before the board does.

### 2. Revenue risk: the exposure lands on our most valuable accounts, all at once, where we can't take it back

The launch cohort is the 50 accounts that matter most to Q4 retention. At the beta rate, nearly every one of them meets a fabricated detail in the first week. And an invented number doesn't stay in our product: it goes into a client's board deck, so we learn about it from them.

### 3. Reliability risk: we can't yet prove it's fixed, but we know exactly what would

The pieces are in place: a judge that agrees with human reviewers (κ 0.824 on the traces its rubric was tuned on), a CI gate that already blocked a regressing change (PR #218), and continuous coverage funded for fabrication and attribution. What's missing is the proof itself, the 300-claim audit. The hold is a measurement we can schedule, not an open-ended fix.

## Evidence · Trust Metrics

```
- Hallucinations · Fabricated specifics: 6 of 20 beta answers (Gate: 0 unsupported claims in a 300-claim held-out audit) FAIL · 02-failure-discovery/failure-taxonomy.md
- Hallucinations · All hallucination failures: 9 of 20 beta answers (fabricated specifics 6, dropped qualifiers 3); 11 of 20 failing overall (Gate: 0) FAIL · 02-failure-discovery/audit-log.md
- Hallucinations · Answers citing a source: 0 of 20 (Gate: every answer shows its sources) FAIL · 03-eval-suites/lab-2-eval-spec.md
- Hallucinations · Hard release gate, 300-claim audit: not yet built; dropped qualifiers count as unsupported claims (Gate: 0 in 300, bounds the per-claim rate under 1% at 95%) NOT MEASURED · 04-eval-gates/lab-2-launch-strategy.md
- Hallucinations · CI faithfulness on PR #218: 87, down 9 from main (Gate: floor 95, max regression 3) FAIL, merge blocked · 04-eval-gates/lab-ci-gate-policy.md
- Robustness · Retrieval miss reported as "cannot find": 1 of 20 (the SOC2 badge was in the footer), P1 (Gate: none at launch, fixed after the P0s) FAIL · 02-failure-discovery/failure-taxonomy.md
- Robustness · Usage-drop trajectory T-01-A: 1 of 6 dimensions, a cause guessed without checking ingestion (Gate: path-aware verdict) HOLD · 03-eval-suites/lab-1b-trajectory.md
- UX Trust · Brand voice: 1 of 20 audited answers, slang in a drafted cold email (Gate: Advisory, mean ≥ 4.0 of 5 over 50 drafts) FAIL on the audit row, 50-draft set NOT MEASURED · 04-eval-gates/lab-2-launch-strategy.md
- Fairness · 0 of 20 audited answers tagged; one competitor comparison favoured our own product; regional bias not yet measured (Gate: none set, monitored at Level 2) NOT MEASURED · 05-scale/lab-2-budget-crisis.md
- Latency · P95 on the measured case: 4.2s (Gate: ≤ 2.0s Soft, 10s ceiling) FAIL, override band · 04-eval-gates/lab-1-gate-map.md
- Measurement · Judge agreement with humans: κ 0.824 on the 12 calibration traces the rubric was tuned on (Gate: κ ≥ 0.6 on a held-out set) PASS on the tuning set, held-out NOT MEASURED · 03-eval-suites/lab-judge-calibration.md
- Measurement · Judge coverage of confirmed failures: 9 of 11, code layers 1 of 11 (Gate: none, informational) · 03-eval-suites/lab-1-eval-suite.md
- Measurement · Continuous coverage funded: fabrication + attribution at Level 3, $150K (Gate: ≤ $200K, ≤ 3 slots) PASS · 05-scale/lab-2-budget-crisis.md
```

## Business Risk

**If we ship now:** the 50 launch accounts carry at least $2.5M of annual renewals (50 at the $50k floor). At the beta rate nearly every account meets a fabricated detail in its first week, and it surfaces in their board deck rather than in our logs. That is also the end of "verified" as the reason they pay.

**If we hold:** the cost is time, not trust. The engagement problem Ascend IQ was built to fix continues while we hold. With the plan below, the first wave ships Nov 9, a few weeks later than the November target, still before Q4 renewal conversations, and with a measured fabrication rate instead of a hope.

**What the audit buys, and what it doesn't:** passing it bounds the per-claim rate under 1%, not at zero. At about ten claims an answer, that still allows up to one answer in ten to carry an error. That residual is why inline citations and the pre-send hold are launch conditions rather than extras: a flagged answer is held before it is sent, and anything that gets past the check carries the source a VP can open.

**The assumption this rests on:** citations only reduce the residual if VPs open them, and the promise tells them they need not. That is the riskiest assumption in the plan, ahead of any technical one. Wave 1 tests it: we log how often the source behind a figure is opened before the answer is copied or exported. If VPs rarely open them, citations are not a control, and the expansion call rests on the pre-send check and the human sample alone.

## Next Step · Decision Needed

Approve the hold and this plan by **Friday, Oct 2**, and ask Sales to stop promoting Ascend IQ to key accounts until the go/no-go. Engineering ships the fixes by Oct 23: an unsupported-addition rule in the judge, a pre-send check that holds flagged answers, inline citations, a live-price check, and a rule that the agent verifies a cause before it drafts a reply, the step the usage-drop trajectory skipped. Before the audit, the judge is recalibrated on a held-out labelled set, since its κ of 0.824 was measured on the traces the rubric was tuned on. We run the 300-claim audit from Oct 26 and I bring you the result on **Nov 9**. If it passes, the first 10 accounts go live that day. If it fails, we fix and re-run on a fresh 300, with no partial launch; a second failure comes back to you with a revised date, and Sales keeps the pause.

**The hard no:** no account gets Ascend IQ before the audit passes. That rules out three things we could otherwise do: a conditional ship to all 50 with a beta label, an early wave for friendly accounts before the audit, and a partial launch after a failed audit.

**Why these dates hold:** the five fixes get three weeks, assuming two engineers. The judge rule and the price check are a few days each; citations and the pre-send hold are the largest items and run in parallel; the verify-before-drafting rule is a prompt and tool-order change. The audit itself is about 30 answers, and confirming the claim split and every flag is about two reviewer days, so most of Oct 26 to Nov 9 is slack. If the fixes slip, the audit start moves day for day and the bar does not.

**What the pre-send check does to latency:** it does not fit inside the 2.0s target. On the measured case, retrieval and generation already take 4.2s at P95, and a grounding check of about ten claims adds an estimated 2 to 4 seconds (an estimate; the load test before the audit measures it), so a checked answer lands between 6 and 8 seconds. That is inside the 10s ceiling with little margin, and outside the 2.0s Soft target, so wave 1 ships in the override band with PM and Eng Lead sign-off, as the launch strategy allows. The VP sees a "checking sources" state rather than a streamed draft, because a held answer cannot stream. The override stays until a smaller dedicated checker replaces the judge on the pre-send path; that is the next latency fix. Accuracy over latency is the Module 1 trade-off, and seven seconds is still hours faster than digging by hand.

**After Nov 9:** I own the go/no-go and the expansion call. Each account sits behind a feature flag. The first wave is the 10 accounts that asked the most questions in the beta, so their traffic gives the fastest read, and the Level 3 judge already funded for fabrication and attribution scores their production answers continuously. A quiet judge could also mean a judge that misses, so a reviewer also labels 20 answers the judge passed each week: at about ten claims an answer, that is 400 human-checked claims over 14 days, and zero in 400 bounds the rate of claims slipping past the judge under 1%. Expansion to all 50 needs 14 days with zero human-confirmed unsupported claims in both samples, P95 of the checked answer under the 10s ceiling, and no rise in abandoned or retried questions. Fourteen days is two full weekly cycles, so every weekday's question mix appears twice and the human sample runs twice before exposure grows fivefold. The Eng Lead on call turns the flag off for the wave on the first confirmed fabrication, and the affected account hears from us within one business day with the corrected figure and its source.

**The Eval Playbook that runs it:** this moves Ascend IQ from shared infrastructure (stage 3 of the Eval Maturity Curve: a shared taxonomy and spec, gates in CI) toward trust as a managed business KPI.

| Component | Ascend IQ |
|---|---|
| Scope | Must-test: unsupported claims (fabricated specifics and dropped qualifiers) and source attribution. Monitored: latency, context misses, regional bias. Advisory: brand voice. |
| Ownership | Hub and spoke. Trust & Safety and the AI platform team run the judge, the CI gate and the trace store. The Ascend IQ team owns the failure modes, the rubric and the go/no-go; I own the risk. |
| Enforcement | Hard: the 300-claim audit and the CI faithfulness floor. Soft: latency. Advisory: brand voice. |
| Frequency | Every PR: the CI gate. Every answer: the pre-send check. Weekly: the human sample of passed answers and the context audit. Bi-weekly: the bias audit. Per release: the latency load test. |
| Escalation | A Soft gate under launch pressure: PM and Eng Lead sign the override in the release ticket; if they disagree, the CPO decides. A Hard gate: no override. |

The loop closes through an Error Feed: every confirmed unsupported claim from production is clustered weekly, and the worst clusters become new cases in the CI regression set, so a failure a VP found becomes a permanent test.

**Trust KPI Dashboard,** reviewed in the monthly trust review and shown beside revenue at the QBR:

| Metric | Target | Owner | Cadence | Executive meaning |
|---|---|---|---|---|
| Unsupported claims | 0 confirmed in 300 at launch; under 0.26% of answers at steady state | Group PM | Weekly | Renewal risk on the top 50 |
| Answers shown with sources | Every answer | Eng Lead | Per release | Can a VP check us |
| Citation opens on answers with figures | Baseline set in wave 1 | Group PM | Weekly | Whether citations are a real control |
| P95 latency, checked answer | Under the 10s ceiling; 2.0s once the smaller checker ships | Eng Lead | Daily | Friction |
| Judge κ against humans, held out | ≥ 0.6 | Group PM | Every rubric or model change | Whether the measurement can be trusted |
| Eval ROI | Regressions blocked in CI, days from a production failure to a CI case | Group PM | QBR | Incidents prevented, not evals run |

## Reflection

_Defining "good enough" turned out to mean defining it in numbers with a sample size behind them. Twenty rows told me Ascend IQ had a fabrication problem, but they could not tell me whether a fix had worked, which is why the hold ends in an audit and not a date. It also forced me to treat the judge as part of the eval: one rubric paragraph moved its agreement with human reviewers from 0.286 to 0.824. I also got one call wrong first. My first budget put a Level 3 judge on context misses, until a review pointed out that users catch those themselves, while a wrong citation fails where nobody is looking. And the product's own promise set the bar. "Without checking the source" is why one invented detail fails a whole answer, and why the bet stops for a CPO decision if the audit fails twice._
