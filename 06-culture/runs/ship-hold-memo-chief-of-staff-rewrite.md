# M6 Ship/Hold Memo, Senior Chief of Staff rewrite (cold run, verbatim)

> Run 2026-10-01 in a fresh context (Agent tool, no session history, no tools). Prompt: the Ship/Hold Memo Builder's own "Copy Prompt" text (polish your draft in any LLM), filled with every field of the committed memo (decision HOLD).

---

# Memo: Hold Ascend IQ Until It Passes the Fabrication Audit

## The Answer

I recommend we hold the Ascend IQ launch to our top 50 accounts until it passes a zero-fabrication audit of 300 claims, with the go/no-go on **Nov 9**. In beta, 6 of 20 answers stated a detail the source does not support. Shipping now puts more than $2.5M of annual renewals in front of an answer that a VP has a 97% chance of catching out within ten questions.

## Arguments

**1. Brand: we would ship the failure that clients pay us to prevent.** Ascend IQ promises an answer a VP can take to the board without checking the source. Today the agent invents specifics and drops the qualifiers that keep a fact true. No answer cites a source, so the board finds the error before the VP does.

**2. Revenue: the risk hits our most valuable accounts all at once.** Together the 50 launch accounts carry at least $2.5M of renewals going into Q4. At the beta rate, almost every one of them would see a made-up detail in week one. We would hear about it from their board deck, not from our logs.

**3. Reliability: we can't prove a fix yet, but the proof is scheduled.** The automated grader, the release gate and the funded monitoring are all in place. The only missing piece is the audit, so the hold ends on a set date with a measured result.

## Evidence

| Trust metric | Result | Gate | Status |
|---|---|---|---|
| Hallucinations | 6 of 20 answers invent a detail; 9 of 20 with dropped qualifiers counted | 0 unsupported claims in 300 | FAIL; audit not yet run |
| UX Trust | 0 of 20 answers cite a source | Every answer | FAIL |
| Robustness | Quality score 87 on a recent change, down 9 | Floor 95, max drop 3 | FAIL; release blocked |
| Latency | 4.2s at P95 | 2.0s target, 10s ceiling | FAIL; under the ceiling |
| Fairness | Regional bias audit not yet run | Every two weeks | NOT MEASURED |

The grader agrees with human reviewers at κ 0.824 (gate ≥ 0.6), but that was measured on its tuning set. We will recheck it on held-out data before the audit.

## Business Risk

**If we ship now:** more than $2.5M of renewals is exposed, and we lose "verified" as the reason clients pay us.

**If we hold:** we lose a few weeks. We keep their trust. Wave 1 still lands before Q4 renewal talks, and it ships with a measured error rate.

**What a pass does not guarantee:** a pass caps the error rate below 1% per claim, not at zero. That is why inline citations and the pre-send check are launch conditions. Checked answers will take 6 to 8 seconds. That is under the 10s ceiling, but it needs PM and Eng Lead sign-off.

## Next Step

Approve the hold by **Friday, Oct 2**, and have Sales stop promoting Ascend IQ to key accounts. Engineering ships five fixes by Oct 23. The audit starts Oct 26, and I bring you the result on **Nov 9**. If it passes, 10 accounts go live that day, and we expand to all 50 after 14 clean days. If it fails, we fix it and run a new 300-claim audit, with no partial launch. If it fails a second time, the decision comes back to you with a new date.

## Reflection

*Twenty beta answers were enough to show a problem. They could not show whether a fix had worked. That is why the hold ends in an audit, not a date. The product's own promise, "without checking the source," set the bar: one invented detail fails the whole answer.*
