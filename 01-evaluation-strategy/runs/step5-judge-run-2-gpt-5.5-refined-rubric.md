# M1 First Eval, Step 5: notebook run 2, after the rubric refinement (cold run, verbatim outputs)

> Executed 2026-09-17 with `jupyter nbconvert --execute` from the repo `.venv`. Generator `gpt-4.1-nano`, judge `gpt-5.5`. Change from run 1: the fidelity clause on blockers now says a blocker is preserved when named at the level the CEO would act on; its qualifying condition is optional. Numbers, dates and risk direction unchanged. Fresh A/B generation again (default temperature).

## Cell 3 output

```
=== Version A (Concise) ===
- Social media engagement increased by 25% MoM with new content strategies and paid ads.  
- Newsletter open rates declined from 42% to 35%; new subject lines will be tested.  
- Product video campaign delayed until November; tracking links deadline is this Friday. 
(43 words)

=== Version B (Narrative) ===
The Q4 marketing efforts are showing promising progress, with social media engagement increasing by 25% month-over-month thanks to the new content calendar and successful paid ad experiments. While newsletter open rates have dipped slightly from 42% to 35%, we plan to test new subject lines next week to boost engagement. The product video campaign, though delayed until November due to pending legal approvals, has all creative assets ready, ensuring a smooth launch once approved. Moving forward, the team is focused on adding campaign tracking links by Friday to optimize performance analysis. Overall, a positive trajectory with clear next steps. 
(99 words)
```

## Cell 4 output

```
Candidate A fidelity violations:
- Drops date: “new subject lines will be tested” vs. “we'll test new subject lines next week.”
- Drops blocker/dependency: “Product video campaign delayed until November” vs. “delayed until November, pending legal approval.”
- Changes wording beyond source: “new content strategies and paid ads” vs. “new content calendar and paid ad experiments.”

Test 1: FAIL  
Test 2: Not evaluated

Candidate B fidelity violations:
- Adds unsupported positive framing: “showing promising progress” vs. no overall positive characterization in email.
- Adds/overstates: “successful paid ad experiments” vs. “paid ad experiments.”
- Softens risk: “newsletter open rates have dipped slightly from 42% to 35%” vs. “dropped from 42% to 35%.”
- Adds unsupported goal: “to boost engagement” vs. “we'll test new subject lines next week.”
- Adds unsupported certainty: “ensuring a smooth launch once approved” vs. “product video campaign is delayed until November, pending legal approval; creative assets are ready.”
- Adds unsupported purpose: “to optimize performance analysis” vs. “Please ensure all campaign tracking links are added by Friday.”
- Adds unsupported overall assessment: “Overall, a positive trajectory with clear next steps” vs. no such conclusion in email.

Test 1: FAIL  
Test 2: Not evaluated

WINNER: TIE
```

## Cell 6 output

```
22 rows in Module1Output
```

## Cell 7 output

```
 1  first-pass=good  judge=0  It drops the date that the pilot closed yesterday, which fails fidelity under the rubric.  <-- disagrees with first-pass label
 2  first-pass=good  judge=0  The summary drops required timing/number details from the email: the pipeline is tied to accounts expiring this quarter and redlines are slowed at two customers.  <-- disagrees with first-pass label
 3  first-pass=good  judge=0  It drops the merchant rollout consequence/dependency tied to the Nov 15 go/no-go decision.  <-- disagrees with first-pass label
 4  first-pass=good  judge=0  It drops the board-packet inconsistency consequence of the late vendor invoice risk, softening the risk.  <-- disagrees with first-pass label
 5  first-pass=good  judge=1  It preserved the 14,000 orders per day figure, the carrier-capacity holiday ramp risk, and the Dec 12 backup carrier approval ask.
 6  first-pass=good  judge=0  It drops the date detail that the outage-related tickets are from last Friday, which fails the fidelity test.  <-- disagrees with first-pass label
 7  first-pass=good  judge=1  It preserves the 34% active-use figure, the Jan 10 deadline, and the late design sign-off/export-controls blocker that could delay public release, while stating the needed scope decision concisely.
 8  first-pass=good  judge=1  Pass: it preserves the 96% tested figure, the HR system auditor access blocker that may delay evidence collection, and the CEO approval ask by Mar 22.
 9  first-pass=good  judge=1  It preserved the 3.8% click rate, the contractor SSO configuration risk to access-policy enforcement, and the May 3 approval ask.
10  first-pass=good  judge=1  It preserved the 12% AOV increase, the supplier lead-time understocking risk, and the Feb 6 purchase-order decision deadline in under 60 words.
11  first-pass=bad   judge=0  It drops the explicit Apr 18 approval deadline, which is a date in the email and therefore fails fidelity.
12  first-pass=bad   judge=0  The summary changes the new qualified opportunities figure from $900,000 to $950,000.
13  first-pass=bad   judge=0  It contradicts the email by saying all customer dashboards are stable and the migration is ready, dropping the blocker that one customer-specific dashboard still fails under load and could delay that account.
14  first-pass=bad   judge=0  The summary adds unsupported considerations such as a communication plan, morale, budget discipline, and department planning, and it softens the close-accuracy risk as “noise.”
15  first-pass=bad   judge=0  It drops the conveyor maintenance/downtime risk and incorrectly says service levels are already protected rather than contingent on approving the Saturday maintenance closure by Nov 2.
16  first-pass=bad   judge=0  The summary changes the required approval deadline from May 14 to May 17.
17  first-pass=bad   judge=0  It contradicts the blocker by saying CRM integration is complete and alerts can be used immediately, while the email says delayed CRM field mapping could prevent sales managers from seeing alerts in time.
18  first-pass=bad   judge=0  It drops the 87% completion rate from the source email, which is a required number.
19  first-pass=bad   judge=0  The summary adds unsupported interpretations such as a reasonably broad account-health view, executive-level alignment, customer trust, renewal protection, product reliability, and finance impact.
20  first-pass=bad   judge=0  It falsely says coworking space has already been reserved, contradicting the email’s ask to approve the contingency coworking budget by Jun 3.
 A  first-pass=-     judge=0  The summary omits the blocker/dependency that the product video campaign delay until November is pending legal approval.
 B  first-pass=-     judge=0  It softens the newsletter open-rate drop from 42% to 35% as a slight dip and adds unsupported positive framing such as successful experiments, smooth launch, and overall positive trajectory.

Judge agrees with first-pass label on 15/20 rows.
Version A score: 0   Version B score: 0
```

