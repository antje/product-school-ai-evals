# Failure Taxonomy Canvas · Ascend IQ

> Repo file `ai-evals/02-failure-discovery/failure-taxonomy.md` (the repo is your submission); becomes the Failure Taxonomy slide of the final pitch deck.
> File: `ai-evals/02-failure-discovery/failure-taxonomy.md`

## Top 3 Prioritized Failures

| Rank | Failure Type | Trust Tag | Agentic Mode | Frequency | Severity | Business Impact |
|---|---|---|---|---|---|---|
| #1 | Fabricated Specifics (rows 1, 4, 5, 9, 12) | #HALLUCINATION | · | 9 → HIGH | P0 | Contract Loss, the agent adds details the source never contained (a seat minimum, a funding stage, a "confirmed" speaker, a hex code). A VP repeats one to their board, gets corrected, and stops trusting every other answer; the $50k renewal goes with it. |
| #2 | Dropped Qualifier, Overstated Capability (rows 3, 7, 11, 14) | #HALLUCINATION | · | 9 → HIGH | P0 | Wrong Decisions, partly true answers with the condition removed ("native" export, "seamless" HubSpot, Austin as HQ, Z "throttles" at twice our rate). Nothing is false on its own, so nobody catches it, and an unqualified "yes" drives a build-or-buy or competitive call the qualifier would have reversed. |
| #3 | Retrieval Miss Reported as "Cannot Find" (row 8) | #ROBUSTNESS | Recovery failure (inferred: the audit data has no trace, so the mode is a hypothesis to confirm on a trajectory in the next audit) | 1 → LOW | P1 | False Negatives, the SOC2 badge was in the footer and the agent said there was no evidence. A wrong "no" about a competitor goes into a vendor comparison, and a market-intelligence product that says "no data" when data exists is worse than one that says nothing. |

## #1 Risk · Business Impact Statement

> This failure matters because Ascend IQ adds specifics the source never contained, a seat minimum, a funding stage, a "confirmed" speaker, a hex code, in 5 of 20 audited answers, which means one in four answers a VP takes to their board carries a detail we cannot stand behind; the first time a client catches one, the $50k renewal is gone and the sales pitch for the other 49 accounts is dead.

## Defending the Prioritization

- #1 is P0 on severity, not frequency. Even if it happened once, a fabricated number in a board deck is contract-ending for an account that pays for verified data. That it happens in 5 of 20 rows makes it a crisis, not a hidden risk.
- Frequency is the count of every row carrying the same Trust Tag across the 20 audited rows, threshold 3. #HALLUCINATION carries 9 (five rows are #1's pattern, four are #2's), so both are HIGH; #ROBUSTNESS carries 1 and is LOW, so #3 ranks on severity alone (P1), above the tone failure (P2) that a VP would notice first.
- Severity is anchored to the Module 1 promise and metrics. "Without checking the source" is the hallucination-rate metric measured per claim, which is why a single invented detail fails a whole answer. #3 is the robustness metric, where the correct behaviour on missing data is a refusal with a reason; here the data was not even missing.
- #1 hides a second root cause: row 1 also served a cached price ($49) as current when the source had moved to $59. Fabrication and stale retrieval need different fixes (grounding constraints vs cache freshness), which is why the audit log records both.

---

_Failure Taxonomy Canvas, M2 lab, AI Evals Certification._
