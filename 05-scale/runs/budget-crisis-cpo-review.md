# M5 Budget Crisis, CPO pressure test (cold run, verbatim)

> Run 2026-09-29 in a fresh context (Agent tool, no session history, tools disallowed). Prompt: the Budget Crisis Tool's own critique prompt ("You are the CPO of Ascend Analytics...") with the uncommitted `05-scale/lab-2-budget-crisis.md` pasted in, plus the tool's level definitions, failure-mode descriptions and L3 costs as context.
>
> Changed as a result: Source Attribution moved to Level 3 and Context Specificity down to Level 2 with a re-ask signal; one judge checks each claim against its cited source before sending; the bias source count runs in code on all traffic; a hard per-request token cap added.

---

CPO pressure-test of the Ascend IQ eval portfolio (answered from the provided text only, no tools used)

Bottom line: you funded the wrong P1 at Level 3. Keep Data Fabrication at L3. Swap Context Specificity down to L2 and move Source Attribution up to L3. The swap costs $4,500 less than your current plan. Your written justifications also would not survive an incident review.

---

## 1. Did I fund the right P0/P1 risks at Level 3?

**P0 Data Fabrication at L3 is correct.** Nothing else is on the table.

**The P1 choice is wrong.** Your case for Context Specificity is "only a judge can see it." Your own Attribution section admits the same about its hardest failure: "a citation to the wrong but related table" is "the one kind of miss code cannot see." So that argument does not separate the two. What separates them is who can see the failure:

- **Context Specificity fails in plain sight.** The VP asked for negative reviews and got all of them. They notice, re-ask, and maybe grumble. The cost is one extra query and some trust.
- **Attribution fails where nobody is looking.** A citation to the wrong but related table looks verified. That is the product: "verified market intelligence" for $50k+ a year. The VP puts the number in a board deck. Months later the client's compliance or audit team traces the citation and finds it does not support the claim. Now it is their audit finding, with our name on it.

Put continuous detection where the customer cannot detect the failure. **Downgrading Attribution is the most catastrophic strategic exposure in this portfolio.** It attacks the one word you sell on, "verified," at the 50 accounts you are unlocking next month. It also lands in the one place you cannot quietly fix: a Fortune 500 compliance process.

Your runtime guardrail makes this worse, not better. It removes the easy attribution failures, so what gets through is mostly the class it cannot see. The guardrail concentrates the remaining risk in exactly the failures your L2 audit is weakest at.

## 2. Are the fallbacks sufficient, or box-checking?

**Attribution, L2 (weekly audit of 30 answers plus the runtime guardrail): the guardrail is real, the audit is box-checking.**
- The guardrail is a good control. It blocks missing citations and figures the cited source does not contain, at close to zero cost. Keep it whatever else you decide.
- It has gaps you have not addressed:
  - **Derived figures** such as averages, growth rates, or a counted review total do not appear in the source word for word. The guardrail either rejects them, which breaks the product, or exempts them, which lets fabrication through.
  - **Number formats** differ: "$1.2B" and "1,200 million" are the same figure but a text match treats them as different.
  - **Qualitative claims** with a wrong citation pass untouched.
  - **The same figure in the wrong table** passes, for example the right number from the 2023 table or the wrong region.
- The 30-sample audit cannot keep its promise. If 1% of answers carry a wrong-table citation, a 30-answer sample finds none 74% of weeks. The chance of catching at least one is 1 − 0.99^30 ≈ 26%, so first detection takes about 4 weeks on average. At 2% you still miss it 55% of weeks.
- A clean week only tells you the rate is below roughly 10% (rule of three, 3/30). For a P1 compliance risk, that is no assurance.
- Even when the audit fires, it gives you a rate. It does not tell you which already-shipped answers to recall.

**Data Bias, L2 (bi-weekly audit of 40 summaries): the method fits, but the measurement is wrong.**
- A periodic, pattern-level check suits drift. That part is right.
- Counting US against APAC sources relative to the source set is a code metric. Run it automatically on every multi-region summary for about L1 cost, instead of hand-counting 40.
- Spend the human hours on what code cannot see: framing. A summary can cite APAC sources and still lead with US conclusions.
- Forty summaries will catch a gross skew and miss a moderate one.

**Cost Overruns, L1 (CI gate plus billing alert): mostly adequate for a P3, with three holes.**
- The gate only warns; it does not block. "5 points" has no stated unit, and in a review nobody will know what it meant.
- The realistic overrun is a runaway agent loop on real production queries. A per-release gate on a fixed eval set will not see it. The missing control is a hard per-request token and step cap in the runtime. It is cheap, and it is the real fix.
- The trust metric is Latency, but the method only measures tokens. Retries and tool calls add latency without showing up in token counts.

## 3. Would your justification language hold up in a customer incident review?

**No.** Five specific problems:

- **Your P0 has no written justification at all.** The document justifies only the fallbacks. For Fabrication at L3 you state no judge recall, no judge-vs-human agreement, no alert threshold, no owner, and no response when it fires. You also do not say whether the judge blocks answers before they ship or monitors them after. If it only monitors, the review's first question is: "Your monitor saw the fabricated number. Why did the customer see it first?" Saying "we spent $85K on L3" is not a defense.
- **"Every answer passed the citation guardrail."** In an incident review this is an admission that the guardrail passed the bad answer. It is not a defense.
- **"Caught by the weekly audit within seven days."** This is false at realistic failure rates; see the math in section 2. A reviewer who does that math once will stop trusting the rest of the document.
- **"Most attribution failures are mechanical."** Most according to what? No measured split is cited. That unsupported claim is the entire basis for the downgrade.
- **"A skew found two weeks late costs a correction note."** To an APAC-heavy Fortune 500 client making a market-entry decision, it costs a bad decision. Do not write it that way where a customer could read it.

## 4. The one eval to re-prioritize, with cost

**Swap Attribution to L3 and Context Specificity to L2.**

| Item | Current | Proposed |
| --- | ---: | ---: |
| Data Fabrication | L3 $85,000 | L3 $85,000 |
| Source Attribution | L2 $6,500 | **L3 $65,000** |
| Context Specificity | L3 $70,000 | **L2 $7,000** |
| Data Bias | L2 $5,500 | L2 $5,500 |
| Cost Overruns | L1 $1,250 | L1 $1,250 |
| **Total** | **$168,250** | **$163,750** |

- The proposed plan is $4,500 cheaper and leaves $36,250 under the cap. L3 spend drops from $155K to $150K. The $50K of L3 headroom still cannot buy Bias ($55K) at L3, so the third slot stays empty.
- Keep the runtime citation guardrail running under the L3 attribution judge.
- For Context Specificity at L2, use a cheap continuous proxy: per-account re-ask and reformulation rate within a session. The user is your detector for this failure, so instrument them.
- Use some of the headroom to automate the Bias source-count on full traffic, and add the per-request token cap.

**One question before you sign the $65K.** Does your Fabrication judge check each claim against its *cited* source, or only against "does this exist anywhere"? If it checks the cited source, it is already most of an attribution judge. In that case, add a "does the cited source support this claim" check to the same judge for marginal cost, and you may be able to keep Context Specificity at L3 as well. If it only checks existence, a fabricated-but-plausible number with a wrong-table citation passes both of your controls today. That is your worst-case incident.
