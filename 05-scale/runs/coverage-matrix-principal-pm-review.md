# M5 Coverage Matrix, Principal PM pressure test (cold run, verbatim)

> Run 2026-09-29 in a fresh context (Agent tool, no session history, tools disallowed). Prompt: the Coverage Matrix Tool's own pressure-test prompt ("You are a Principal PM at Ascend Analytics...") with the current `05-scale/lab-1-coverage-matrix.md` pasted in (uncommitted working copy, after the ground-truth fix and the restored hallucination method).
>
> Changed as a result: a pre-send grounding check and a new-template launch gate, with the weekly sample as backstop; humans label unflagged summaries; recalibration dated before the controls; bias given an owner and method; the late-summary behaviour defined; the per-summary rate reported beside the per-claim bar; the audit drawn from at least 100 summaries.

---

**Verdict:** the Toxicity acceptance mostly holds. The other three answers have real holes. The biggest problem is the mitigation plan, which detects a bad summary a week or more after a client has read it.

---

## 1. Are the ❌ marks defensible for an External product?

**Bias ❌: not defensible. A regulator or auditor would flag this first.**

- **It is an orphan.** Bias is neither the accepted gap nor the mitigated one. It gets half a sentence ("goes into the Q1 quarterly human audit"). That means no owner, no method, no kill criterion, and nothing for three months. An auditor reads that as an unmanaged risk on a client-facing product.
- **You are thinking of the wrong kind of bias.** In a research-summary product, bias does not mean demographic bias. It means the summary is more bullish or bearish than the source. It drops the risks section or softens a downgrade. It shifts a price target or rating. It leaves out disclosures.
- **A regulator calls that a fairness problem.** If Ascend or its clients are regulated for research distribution, rules on fair and balanced communications apply (for example FINRA 2210 and 2241, or MiFID II research rules). Under those rules, misleading by omission is a violation even when every sentence is true.
- **Your grounding judge cannot catch it.** It only checks that each stated claim is supported. A summary can pass with zero unsupported claims and still misstate the analyst's view.
- **The fix is cheap.** Add a code check that the rating, price target, direction and required disclosures are preserved. Add a judge check that the stance matches the source. Run both on the same held-out set. That turns Bias into ⚠️ within weeks, and arguably makes it part of the Hallucination cell.

**Toxicity ❌: defensible on likelihood.** See section 2 for why the rationale is still weak.

**Drift ❌: an honest mark.** The mitigation is the problem (section 3).

**Latency ✅: wrong mark.**
- You say yourself that the 5-minute bar is an unconfirmed assumption. A ✅ against a made-up threshold should be ⚠️.
- The real latency question is missing: what happens to the 5% of summaries that miss P95? Does the email wait, go out without the summary, or send the summary late on its own? That failure behaviour needs defining before the cell can be ✅.

**Hallucination ⚠️: generous.** The judge has never been validated on this content type. If the product is already sending to clients, the P0 gate is effectively unvalidated, which behaves like ❌. An auditor will ask what evidence supports ⚠️ rather than ❌. Right now the only answer is "a judge calibrated on a different product."

## 2. Is the "accept" rationale anchored in low impact / high cost?

**Neither. It rests on low likelihood.** That is a legitimate basis, but it is not the test you asked me to apply.

- **Impact is high, not low.** Offensive language in an email you cannot recall, sent to a $50k+ client, is a serious incident. Your own Drift section argues exactly this.
- **Cost to close is low, not high.** An off-the-shelf moderation check on the output before it is sent costs milliseconds and an afternoon of work. "Building a toxicity classifier" overstates the effort. Nobody is asking you to build one.
- **So this is not rationalizing a P0.** Toxicity really is the least likely failure here. But the rationale should say "low likelihood, and we accept a lagging trigger." Or you should add the cheap pre-send filter and mark the cell ⚠️. The second is the better look in front of an auditor.
- **Your kill criterion triggers after the harm.** "Any client reports offensive language" means the first detection is a client incident.
- **Your second trigger has no detector.** "Source reports start quoting user-generated content" is right, but nobody watches for it. It only works if new templates go through a formal gate (see section 3).
- **"No user text shapes the output" is looser than it sounds.** Reports quote management, earnings calls and third parties. The residual risk is less about profanity and more about defamatory statements about named executives. That belongs under Hallucination, so make sure the grounding judge covers attributed quotes.

## 3. Is the mitigation plan specific enough to close the gap?

**It is specific on inputs** (judge, sample of at least 50, weekly, owners, a date). **It is aspirational on the parts that decide whether it works.**

1. **It does not match the risk you described.** You call drift critical because emails cannot be recalled. Then you prescribe a weekly sample that detects problems after sending. The alert needs two consecutive bad weeks, so exposure runs up to 14 days plus review time. For an unrecallable channel, the control has to act before sending.

2. **Most of your "drift" is a known change, not drift.** Ascend creates the new sectors and templates itself, and knows when they launch. Handle that with a launch gate, not a statistical monitor:
   - No new template or sector goes live until it passes the offline grounding eval on labelled examples.
   - The first N summaries of any new template go to human review before sending.

   That closes most of the gap on day one. Keep the monitor for true drift: model version changes, gradual shifts in source content.

3. **The alert threshold is undefined.** "Above the offline baseline" by how much, and measured how?
   - With a baseline near zero, one flag in a week of about 500 claims either trips the alert every time or means nothing.
   - The sample also splits across sectors, so a new sector might contribute 5 summaries. Nothing is powered to detect a change there.
   - State the claim count per sample, the alert rule, and the false-alarm rate you expect.

4. **"Distribution shift" has no metric.** No measure is named (such as PSI) and no threshold. A change in input mix is also not a failure in itself, so this alert will be noisy.

5. **No response is defined.** When the alert fires, does auto-send pause for that template? Who decides? Do affected clients get a correction? An alert without a runbook is monitoring theatre.

6. **Nobody checks the judge's misses.** Humans only see summaries the judge flagged, so its false negatives on new sectors are never seen. A small random slice of unflagged summaries needs human labels every week.

7. **There is a hidden dependency.** The plan runs on the same judge you rated ⚠️ because it is uncalibrated for summaries. Recalibration has to finish before Nov 13 and is not on the plan.

8. **Nothing covers the gap before Nov 13.** Six weeks of unmonitored sending are not addressed.

**Problems in the Hallucination ground truth itself:**

- **Per-claim versus per-summary.** Under 1% unsupported per claim, at roughly 10 claims per summary, still allows about 1 in 10 summaries to reach a client with a fabrication. Report the rate per summary. That is the unit the client experiences.
- **Clustered claims.** If the 300 claims come from about 30 summaries, claims within one summary are correlated. The "under 1% at 95%" bound is then overstated.
- **The recalibration bar is too low.** κ ≥ 0.6 is lower than the 0.824 you already have, and 30 summaries gives a very wide confidence interval on κ. For a P0 gate, κ is also the wrong headline number. What matters is the judge's recall on unsupported claims, with a confidence interval.
- **Self-preference risk.** If the summarizer is also a Claude model, say so and account for it.

## 4. The CPO's sharpest question

> **"If a summary misstates a rating or invents a number for a top client tomorrow morning, what in this row stops that email from going out?"**

The honest answer today is **nothing**. Every control in the row is offline or after the email is sent.

A good answer looks like this:
- Run the grounding judge and the numeric code check on every summary before it is sent. That fits inside the 5-minute budget.
- Hold anything flagged and route it to a human.
- Put new templates through the launch gate.
- The weekly monitor then becomes a backstop, not the primary control.
