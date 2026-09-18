# Failure Taxonomy Canvas · Ascend IQ

> Repo file `ai-evals/02-failure-discovery/failure-taxonomy.md` (the repo is your submission); becomes the Failure Taxonomy slide of the final pitch deck.
> File: `ai-evals/02-failure-discovery/failure-taxonomy.md`

## Top 3 Prioritized Failures

| Rank | Failure Type | Trust Tag | Agentic Mode | Frequency | Severity | Business Impact |
|---|---|---|---|---|---|---|
| #1 | Fabricated Specifics (audit rows: Enterprise pricing, SaaStr speakers, TechCrunch sentiment, API rate limits, funding round, brand colors) | #HALLUCINATION | · | 9 → HIGH | P0 | Contract Loss, the agent states a specific the source does not support or contradicts (a seat minimum, a "confirmed" speaker, praise the article never gave, "Competitor Z throttles" when Z allows twice our rate, a funding stage, a hex code). A VP repeats one to their board, gets corrected, and stops trusting every other answer; the $50k renewal goes with it. |
| #2 | Dropped Qualifier (audit rows: native SQL export, HubSpot integration, HQ locations) | #HALLUCINATION | · | 9 → HIGH | P0 | Wrong Decisions, each answer is true of something in the source, and the condition that makes it false is missing ("yes" to native SQL export when it exists only via the API, "seamless" HubSpot when it runs through Zapier, Austin as an HQ when it is an engineering hub). Nothing reads as false, so nobody catches it, and an unqualified "yes" drives a build-or-buy call the qualifier would have reversed. |
| #3 | Retrieval Miss Reported as "Cannot Find" (audit row: Competitor X SOC2) | #ROBUSTNESS | Recovery failure (inferred: the audit data has no trace, so the mode is a hypothesis to confirm on a trajectory in the next audit) | 1 → LOW | P1 | False Negatives, the SOC2 badge was in the footer and the agent said there was no evidence. A wrong "no" about a competitor goes into a vendor comparison, and a market-intelligence product that says "no data" when data exists is worse than one that says nothing. |

## #1 Risk · Business Impact Statement

> This failure matters because Ascend IQ stated a specific the source does not support in 6 of 20 audited answers, and at that rate a VP asking ten questions has a 97% chance of carrying one into a board deck (1 - 0.7^10), which results in the first caught fabrication ending a $50k renewal and nearly every account in the 50-account launch cohort meeting one in its first week.

## Defending the Prioritization

- For #1 the severity-versus-frequency debate does not arise: it is the most frequent pattern (6 of 20 rows) and the most severe. Where frequency would argue against a P0, it is #3: one row, ranked on severity alone.
- P0 means "blocks launch", decided by irreversibility and detectability, not by count. A fabricated specific is invisible to the user and unrecoverable once it has been said in a boardroom. Frequency orders the fixes inside the P0 bucket; it does not decide membership.
- 20 rows do not give a rate. "6 of 20" is a sample, with a 95% interval of roughly 14% to 52%, and no contract has been lost yet: the contract-loss consequence rests on what these clients pay for (verified data), not on an observed churn event. The compounding is the argument: at the sample rate, a VP asking ten questions has a 97% chance of meeting one fabrication, so the 50-account cohort would meet it in week one.
- Frequency is the count of every row carrying the same Trust Tag across the 20 audited rows, threshold 3. #HALLUCINATION carries 9 (six rows are #1's pattern, three are #2's), so both are HIGH; #ROBUSTNESS carries 1 and is LOW. #2 keeps the #HALLUCINATION tag because the audit guide merges completeness errors under hallucination for a B2B platform, and for this client an unqualified "yes" costs the same as an invented number.
- Severity is anchored to the Module 1 promise and metrics. "Without checking the source" is the hallucination-rate metric measured per claim, which is why a single unsupported detail fails a whole answer. #3 is the robustness metric, where the correct behaviour on missing data is a refusal with a reason; here the data was not even missing.
- #1 hides a second root cause: the Enterprise pricing row also served a cached price ($49) as current when the source had moved to $59. Fabrication and stale retrieval need different fixes (grounding constraints vs cache freshness), which is why the audit log records both.
- Open before the Go/No-Go call: the Go threshold for the per-claim fabrication rate, and the size of the held-out audit needed to show it has been met (20 rows cannot tell a 5% rate from a 20% one). This audit counted answers; the metric counts claims, so the held-out audit has to score per claim. And detectability: none of the 20 beta predictions cites a source while every reference does, so today a VP cannot catch a fabrication before the board does.

---

_Failure Taxonomy Canvas, M2 lab, AI Evals Certification._
