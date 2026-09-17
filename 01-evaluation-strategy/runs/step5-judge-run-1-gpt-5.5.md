# M1 First Eval, Step 5: notebook run (cold run, verbatim outputs)

> Executed 2026-09-17 with `jupyter nbconvert --execute` from the repo `.venv`. Generator `gpt-4.1-nano`, judge `gpt-5.5`. Note: this is a fresh generation run, so A and B differ from the Step 4 outputs (default temperature).

## Cell 3 output

```
=== Version A (Concise) ===
- Social media engagement increased by 25% MoM with new content and paid ads.
- Newsletter open rate decreased to 35%; testing new subject lines planned.
- Product video campaign delayed to November; tracking links due Friday. 
(37 words)

=== Version B (Narrative) ===
The Q4 marketing efforts have yielded positive results, with social media engagement increasing by 25% month-over-month thanks to our new content calendar and paid ad initiatives. While the newsletter open rate experienced a slight dip from 42% to 35%, plans are already underway to test new subject lines to improve performance. The product video campaign, a key upcoming initiative, is delayed until November pending legal approval, but all creative assets are prepared, and tracking links will be implemented by Friday. Moving forward, we will continue optimizing our strategies to drive engagement and ensure on-time execution of campaigns. 
(97 words)
```

## Cell 4 output

```
Candidate A:

Fidelity violations:
- “Newsletter open rate decreased to 35%” conflicts by omission with “Newsletter open rate dropped from 42% to 35%” — drops the baseline number 42%.
- “testing new subject lines planned” conflicts by omission with “we'll test new subject lines next week” — drops the date “next week.”
- “Product video campaign delayed to November” conflicts by omission with “delayed until November, pending legal approval” — drops the named blocker “pending legal approval.”

Passes Test 1: No.  
Test 2: Not evaluated.

Candidate B:

Fidelity violations:
- “experienced a slight dip from 42% to 35%” conflicts with “Newsletter open rate dropped from 42% to 35%” — softens a 7-point drop.
- “plans are already underway to test new subject lines” conflicts by omission with “we'll test new subject lines next week” — drops the date “next week” and implies current action not stated.
- “a key upcoming initiative” adds characterization not in the email.
- “tracking links will be implemented by Friday” changes the ask “Please ensure all campaign tracking links are added by Friday” into a statement of certainty.
- “Moving forward, we will continue optimizing our strategies to drive engagement and ensure on-time execution of campaigns” adds content not in the email and softens/contradicts the delay risk.

Passes Test 1: No.  
Test 2: Not evaluated.

WINNER: TIE
```

## Cell 6 output

```
22 rows in Module1Output
```

## Cell 7 output

```
 1  first-pass=good  judge=0  It drops part of the named blocker: the legal review is specifically of the revised claims language and delay depends on late comments.  <-- disagrees with first-pass label
 2  first-pass=good  judge=0  It drops the number of affected customers ('two') and the escalation condition tied to signatures potentially slipping.  <-- disagrees with first-pass label
 3  first-pass=good  judge=0  It drops the named blocker detail that the security review is for the tokenization change and omits that release risk depends on material findings.  <-- disagrees with first-pass label
 4  first-pass=good  judge=0  It drops the stated risk consequence that the August close distortion may make the board packet look inconsistent.  <-- disagrees with first-pass label
 5  first-pass=good  judge=0  It changes the risk from pickup windows slipping if volumes spike to carrier capacity slipping, dropping the specific blocker condition.  <-- disagrees with first-pass label
 6  first-pass=good  judge=1  It preserved the 91% CSAT, outage-ticket backlog risk to response times, overtime-budget ask, and July 19 deadline in under 60 words.
 7  first-pass=good  judge=0  It drops the baseline that 34% active use was among invited accounts and omits the explicit decision of whether to ship without advanced exports.  <-- disagrees with first-pass label
 8  first-pass=good  judge=1  It preserved the 96% tested figure, the HR-system auditor access/provisioning blocker, the evidence-collection delay risk, and the approval ask by Mar 22.
 9  first-pass=good  judge=1  It preserves the 3.8% click rate, contractor SSO configuration blocker, possible access-policy enforcement delay, and May 3 approval ask without adding or softening details.
10  first-pass=good  judge=0  It drops the named blocker detail that the supplier lead-time risk is specifically on the top-selling charger and omits the national expansion condition.  <-- disagrees with first-pass label
11  first-pass=bad   judge=0  It changes 'cost below plan' into clicks being below plan and drops the Apr 18 deadline and reference-account permission blocker.
12  first-pass=bad   judge=0  The summary changes the new qualified opportunities figure from $900,000 to $950,000, violating fidelity.
13  first-pass=bad   judge=0  The summary reverses the named blocker by saying all customer dashboards are stable and migration is ready, while the email says one customer-specific dashboard still fails under load and could delay that account.
14  first-pass=bad   judge=0  The summary adds unsupported considerations such as morale, department planning, and a communication plan, and softens the risk from affecting monthly-close accuracy to creating "noise."
15  first-pass=bad   judge=0  It upgrades the risk by saying service levels are protected and drops the pushed-back conveyor maintenance window/equipment downtime risk.
16  first-pass=bad   judge=0  It changes the required approval deadline from May 14 to May 17 and drops the consequence that withdrawn pricing would reopen negotiations.
17  first-pass=bad   judge=0  The summary reverses the named blocker by saying CRM integration is complete and alerts can be used immediately, while the email says delayed CRM field mapping could prevent managers from seeing alerts in time.
18  first-pass=bad   judge=0  It drops the 87% completion rate from the source email, which is a required number under the fidelity test.
19  first-pass=bad   judge=0  The summary adds unsupported context and implications such as a 'reasonably broad view,' executive-level alignment, customer trust, renewal protection, product reliability, and finance impact.
20  first-pass=bad   judge=0  It falsely says coworking space has already been reserved, drops the Jun 3 approval ask and the permitting-delay blocker, and omits the lease-exit condition for the savings.
 A  first-pass=-     judge=0  It drops the 42% baseline, the 'next week' test timing, and the named blocker 'pending legal approval' for the November video delay.
 B  first-pass=-     judge=0  It softens the newsletter open-rate decline from 42% to 35% by calling it a “slight dip,” which changes the risk.

Judge agrees with first-pass label on 13/20 rows.
Version A score: 0   Version B score: 0
```

