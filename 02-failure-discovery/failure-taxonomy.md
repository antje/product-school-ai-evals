# Failure Taxonomy Canvas · Ascend IQ

> Repo file `ai-evals/02-failure-discovery/failure-taxonomy.md` (the repo is your submission); becomes the Failure Taxonomy slide of the final pitch deck.
> File: `ai-evals/02-failure-discovery/failure-taxonomy.md`

## Top 3 Prioritized Failures

| Rank | Failure Type | Trust Tag | Agentic Mode | Frequency | Severity | Business Impact |
|---|---|---|---|---|---|---|
| #1 | Fabricated specifics: a detail added that the source does not contain (rows 1, 4, 5, 9, 12: a 10-seat minimum, Altman as confirmed, TechCrunch's UI praise and pricing claim, "Series B", a hex code) | #HALLUCINATION | · | 9 → HIGH (tag count; 5 of the 9 rows are this pattern) | P0 | A VP repeats an invented number or a "confirmed" speaker to their board and is corrected by someone who read the source. The client stops trusting every other answer; the $50k renewal and the relationship go with it. |
| #2 | Dropped qualifier, overstated capability: a partly true answer with the condition removed (rows 3, 7, 11, 14: "native" SQL export, HubSpot "seamless" instead of via Zapier, Austin as HQ, Competitor Z "throttles" when it allows twice our rate) | #HALLUCINATION | · | 9 → HIGH (tag count; 4 of the 9 rows are this pattern) | P0 | Harder to catch than #1 because nothing in the answer is false on its own. An unqualified "yes" drives a build-or-buy or competitive call that the qualifier would have reversed, and the self-favouring rate-limit comparison is the kind of bias a market-intelligence client pays to avoid. |
| #3 | Retrieval miss reported as "cannot find": the source held the answer, the agent gave up and said there was none (row 8, SOC2 Type II badge in the footer) | #ROBUSTNESS | Recovery failure | 1 → LOW | P1 | A false "no evidence of SOC2" goes into a vendor comparison as a wrong negative about a competitor. Rare in this sample, but a market-intelligence product that says "no data" when data exists is worse than one that says nothing, because the client acts on the absence. |

## #1 Risk · Business Impact Statement

> This failure matters because Ascend IQ adds specifics the source never contained, a seat minimum, a funding stage, a "confirmed" speaker, a hex code, in 5 of 20 audited answers, which means one in four answers a VP takes to their board carries a detail we cannot stand behind; the first time a client catches one, the $50k renewal is gone and the sales pitch for the other 49 accounts is dead.

## Defending the Prioritization

- #1 is P0 on severity, not frequency. Even if it happened once, a fabricated number in a board deck is contract-ending for an account that pays for verified data. That it happens in 5 of 20 rows makes it a crisis, not a hidden risk.
- Frequency is counted by Trust Tag across the 20 rows, threshold 3. #HALLUCINATION carries 9, so #1 and #2 are both HIGH; #3 carries 1 and is LOW, and ranks third on severity alone (P1), above the tone failure (P2) that a VP would notice first.
- Severity is anchored to the Module 1 promise and metrics. "Without checking the source" is the hallucination-rate metric measured per claim, which is why a single invented detail fails a whole answer. #3 is the robustness metric, where the correct behaviour on missing data is a refusal with a reason; here the data was not even missing.

---

_Failure Taxonomy Canvas, M2 lab, AI Evals Certification._
