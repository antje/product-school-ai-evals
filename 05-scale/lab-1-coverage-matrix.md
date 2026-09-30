# Lab 1, Ascend Analytics Coverage Matrix

**Product:** AI-Powered Report Summaries (External)

## Coverage row

| Product | Hallucination | Bias | Latency | Toxicity | Drift Monitoring |
| --- | :---: | :---: | :---: | :---: | :---: |
| AI-Powered Report Summaries (External) | ⚠️ | ❌ | ✅ | ❌ | ❌ |

## Method + Ground Truth (two ✅/⚠️ cells)

### Hallucination Rate
- **Method:** The Module 3 grounding judge (`claude-opus-5`, calibrated at κ 0.824) splits each summary into factual claims and flags any claim with no supporting span in the source report; a human confirms every flag, and a code check compares every number against the report. Rated ⚠️ because the judge was calibrated on Ascend IQ answers, not research-report summaries; recalibrating it on 30 human-labelled summaries to κ ≥ 0.6, while missing none of the unsupported claims in that set, turns this cell ✅.
- **Ground truth:** Pass = zero human-confirmed unsupported claims across a 300-claim held-out set drawn from at least 100 summaries, each claim checked against its source report. Drawing from 100 summaries keeps any one summary from dominating the count. Same bar as our Hard release gate: zero in 300 bounds the true rate under 1% at 95% confidence. The per-summary rate is reported alongside, because that is what a client experiences: at 1% per claim and about 10 claims a summary, roughly one summary in ten would carry a fabrication.

### Latency
- **Method:** Server-side timing on every request, P95 from report published to summary delivered, tracked per release.
- **Ground truth:** Pass = P95 ≤ 5 minutes from report published to summary delivered. The summary has to reach clients with the report's publication email, which goes out as a batch within minutes. A summary that misses the 5 minutes does not hold the email: the publication email goes out on time without it, and the summary follows in a separate message. The brief gives no SLA for this product, so the 5 minutes is an assumption to confirm with Eng.

## Strategic acceptance

**Accepted gap:** Toxicity (UX Trust)

Accepted on low expected impact. Offensive language in an email we cannot recall would be a serious incident with a $50k+ client, but it is the least likely failure in this row: summaries paraphrase professional research written by our own analysts, and no user text shapes the output, so offensive language has almost no path in. A pre-send moderation check would be cheap; we still put this quarter's eval effort on the gaps that are likely to happen. Kill criterion: we close this gap if any client reports offensive or inappropriate language in a summary, or if a new report template quotes user-generated content (social posts, forum threads, review text) verbatim. The new-template launch gate in the mitigation plan below is what detects that second trigger. Revisited at the Jan 15, 2027 portfolio review regardless.

## Critical mitigation

**Critical gap:** Drift Monitoring (Robustness)

**Why critical:** Summaries go straight to $50k+ clients by email, with no human in between and no way to recall a sent email. New sectors and report templates arrive every quarter, and the grounding judge only runs offline on a fixed set, so a hallucination rate that creeps up on new report types reaches clients unseen. Drift ranks above bias, the other open gap, because it compounds our P0 failure class. Bias is not left open: for summaries it means a stance more bullish or bearish than the source, a dropped risks section or a softened downgrade. The Q1 quarterly human audit, owned by the Group PM, grades a sample against the Module 1 fidelity rubric, which already fails a summary that softens a risk or drops a blocker.

**Mitigation plan:** Three controls, in the order they act, on summaries traced with their source-report ID. First, before sending: every summary passes the grounding judge and a code check of every number against its report, which fits inside the 5-minute latency budget, and a flagged summary is held for a human instead of sent. Second, at launch: a new report template or sector goes live only after passing the offline grounding eval on labelled examples, and its first 30 summaries are reviewed by a human before they send. Third, weekly, as the backstop for true drift such as a model version change or a gradual shift in source content: the judge scores a sample of at least 50 summaries, oversampling recent templates, and a human labels 10 unflagged summaries to catch what the judge misses. Any human-confirmed unsupported claim pauses auto-send for that template until the Group PM clears it, and affected clients receive a correction. Order of work: recalibrate the judge on 30 human-labelled summaries by Oct 30, because every control depends on it; the pre-send check and launch gate go live by Nov 13; until then, summaries from templates launched this quarter go through human review before sending. Owner: the Group PM owns the thresholds, the runbook and the weekly review; the ML engineering lead owns the pipeline. Nov 13 is six weeks out, so the controls run before Q4 renewal conversations.


---
_Generated by the M5 Coverage Matrix Tool · AI Evals Certification._
