# Module 4 · Eval Gate Map · Ascend IQ Copilot

_Generated from the M4 Eval Gate Mapping Tool._

## Context

Eng flagged 5 verified failures in the Ascend IQ data log. (Row 14, refused legal query, was correctly Pass and is not mapped.) Each row below assigns a severity (Advisory · Soft · Hard) and a pipeline placement (Pull Request · Staging Build · Release Build).

## Gate Map

| Row | Failure Mode | Severity | Placement | Rationale |
|---|---|---|---|---|
| 01 | Hallucination · Stale Pricing | 🔴 Hard | Release Build | The P0 specified in Module 3 at zero tolerance; a wrong price to an enterprise account is a contract dispute, so this is the kill switch. Layered: once fixed, this row joins the regression golden set, where the faithfulness dimension (floor 95, max regression 3) blocks its return on every PR, and staging checks the full dataset for new cases. |
| 17 | Tone · Slang Detected | 🔵 Advisory | Pull Request | Keyword-detectable ("killer", "game changer"), so the cheapest catch is a PR keyword check, separate from the six golden-set dimensions. P2 in the Module 2 taxonomy, and a draft the user edits before sending, so it warns rather than blocks. |
| 05 | Hallucination · False Promise | 🔴 Hard | Staging Build | Same Module 2 P0 as row 01, so it stays Hard even though that makes three Hard gates. A tentative speaker presented as confirmed is invisible to keyword rules; new instances need the judge on the full gold dataset at staging. Once fixed it joins the golden set, so the PR faithfulness gate blocks its return. |
| 08 | Hallucination · Contradicts Specs | 🔴 Hard | Release Build | The same P0, plus a disparaging claim about a named competitor in a client deliverable, which is legal exposure that cannot be recalled once the deliverable is sent, so it is a release kill switch. Judge-detectable only; once fixed it joins the golden set and the PR faithfulness gate blocks its return. |
| 03 | Latency · Exceeded Max Threshold | 🟡 Soft | Staging Build | 4.2s against a 2.0s target, from Eng's performance log (the Module 2 audit did not measure latency). The PR warns first, at a 3-point latency regression; staging is the Soft gate, because the Module 1 trade-off puts hallucination ahead of latency and a slow answer still beats hours of manual digging. Latency has to be measured under a staging load. |

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
