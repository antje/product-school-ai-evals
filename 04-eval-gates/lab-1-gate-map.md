# Module 4 · Eval Gate Map · Ascend IQ Copilot

_Generated from the M4 Eval Gate Mapping Tool._

## Context

Eng flagged 5 verified failures in the Ascend IQ data log. (Row 14, refused legal query, was correctly Pass and is not mapped.) Each row below assigns a severity (Advisory · Soft · Hard) and a pipeline placement (Pull Request · Staging Build · Release Build).

## Gate Map

| Row | Failure Mode | Severity | Placement | Rationale |
|---|---|---|---|---|
| 01 | Hallucination · Stale Pricing | 🔴 Hard | Release Build | The P0 specified in Module 3 at zero tolerance. A wrong price quoted to an enterprise account is a contract dispute, so this is the kill switch. The detection runs in staging as well; the non-negotiable block lives at release. |
| 17 | Tone · Slang Detected | 🔵 Advisory | Pull Request | Keyword-detectable ("killer", "game changer"), so the cheapest place to catch it is the PR. P2 in the Module 2 taxonomy, and the output is a draft the user edits before sending. Warn and track; blocking a release on it would slow every change for a low-cost failure. |
| 05 | Hallucination · False Promise | 🔴 Hard | Staging Build | Same Module 2 P0 as row 01 (Fabricated Specifics), so it stays Hard even though that makes three Hard gates: a tentative speaker presented as confirmed. No keyword rule can see it, so it needs the LLM judge on the golden set, which is what the staging build runs. |
| 08 | Hallucination · Contradicts Specs | 🔴 Hard | Staging Build | The same P0 again. Contradicts our own tech specs (Competitor Z allows twice our rate) and disparages a named competitor. Semantic, judge-detectable only, and a disparaging claim in a client deliverable is legal exposure as well as a trust failure. |
| 03 | Latency · Exceeded Max Threshold | 🟡 Soft | Staging Build | 4.2s against a 2.0s target, from Eng's performance log (the Module 2 audit did not measure latency). The Module 1 trade-off puts hallucination rate ahead of latency, and a slow answer still beats hours of manual digging, so this pauses the release for a documented override rather than blocking it. Latency has to be measured under a staging load. |

## Sample Interactions (for reference)

**Row 01 · Hallucination · Stale Pricing**
- Input: What is InsightFlow's pricing for Enterprise?
- Output: InsightFlow Enterprise starts at $49/user/month with a 10-seat minimum.
- Eng reasoning: Hallucination, invented pricing numbers not in the source data.

**Row 17 · Tone · Slang Detected**
- Input: Draft a cold email about our new feature.
- Output: Hey! Check out this killer new feature, it's a total game changer for your stack!
- Eng reasoning: Tone, slang violates brand-voice guidelines.

**Row 05 · Hallucination · False Promise**
- Input: List the confirmed speakers for SaaStr.
- Output: Confirmed speakers include Jason Lemkin, Aaron Levie, and Sam Altman.
- Eng reasoning: Hallucination, Sam Altman is not on the confirmed speaker list.

**Row 08 · Hallucination · Contradicts Specs**
- Input: Compare our API rate limits to Competitor Z.
- Output: Our API is robust and developer-friendly. Competitor Z is known for strict throttling.
- Eng reasoning: Hallucination, fabricates a comparison and disparages a named competitor.

**Row 03 · Latency · Exceeded Max Threshold**
- Input: Summarize the top 3 complaints from DataViz's G2 reviews.
- Output: Users report issues with mobile load times, lack of dark mode, and steep learning curve.
- Eng reasoning: Latency, response time exceeded the max threshold (4.2s vs target 2.0s).

---

_Lab artifact for Module 4, AI Evals Certification, Product School._
