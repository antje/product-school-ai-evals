# M6 Final Pitch, VP of Product critic (cold run, verbatim)

> Run 2026-10-01 in a fresh context (Agent tool, no session history). Prompt: the Final Pitch Prompt Generator's own VP-of-Product critic prompt, followed by the visible text of the first generated deck (v1) and the README the generator builds from the same fields.
>
> Changed as a result: dropped qualifiers counted as unsupported claims in the gate; the 11th failure reconciled; κ labelled a tuning-set figure with held-out recalibration before the audit; a verify-the-cause fix for the usage-drop trajectory; an "After Nov 9" plan with an owner, expansion criteria, a kill switch, client notification and a fail-twice stop; the per-answer residual stated in the memo.

---

(a) I would approve the HOLD today. I would not fund the Nov 9 ship on this memo. Holding is easy to approve: 6 of 20 answers fabricated, 50 × $50k = $2.5M exposed, and 1 − 0.7^10 ≈ 97%. That math survives a skeptical exec. The strongest paragraph is the Slide 8 "Risk-tolerance rationale". It admits that zero in 20 only bounds the rate under 15%, and it uses the rule of three to justify 300 claims. That is the sentence that tells me the PM knows what proof costs. The weakest is "Coverage matrix outcome (M5)". It switches to a different product, AI-Powered Report Summaries, in the middle of a ship memo for Ascend IQ. Its dates (drift closed "by Nov 13") land after the go-live and have nothing to do with it. Reading it, I wonder whether I'm looking at a launch decision or a homework appendix.

There are holes I'd send back before Nov 9:
- **The gate measures the wrong unit.** The gate bounds fabrication under 1% per claim. The risk was sized per answer (30%), and the bar is "board-safe without checking." At 5 to 10 claims per answer, a 1% claim rate is still roughly a 5 to 10% answer rate. That is about 40 to 65% odds over ten questions. The gate as written does not deliver the promise the memo opens with.
- **The second P0 is not clearly gated.** Dropped Qualifier is P0 (3 of 20), but the gate's target risk says "fabricated specifics." Say explicitly that dropped qualifiers count as unsupported claims.
- **The failure counts don't reconcile.** The taxonomy shows 6 + 3 + 1 = 10 failures, but the judge "caught 9 of 11." Show where the 11th comes from.
- **The kappa may be overfit.** The 0.824 kappa came after two rubric revisions, and the memo doesn't say on what sample. If it is the same 20 rows, that kappa is tuned to the test set. I need agreement on a held-out set.
- **A known failure has no fix.** The usage-drop trajectory scored 1 of 6 (HOLD), yet nothing in the fix list or the gates addresses it.

(b) The thinking is strongest in **M4 gates**, closely followed by **M2**:
- **M4:** the hard gate has a statistical basis and a re-run on a fresh 300. There is a CI regression gate with a real block (PR #218 at −9). Latency is a soft gate with an override band, and brand voice is advisory. That is a tiered gate design, not just a list of thresholds.
- **M2:** each failure mode has a count, a priority and a business consequence in the same sentence.
- **M3 is solid but thin:** the finding that the P0 class is semantic (judge 9, deterministic layers 1) is a real insight, but there is no held-out calibration.
- **M6 is the weakest, and it barely exists.** The slide is labeled "M5 + M6," but I see no named owner for the ship call and no incident plan for when a fabrication reaches a client's board deck after launch. There is no client disclosure about AI-generated content or about citations. There is no monitoring or rollback trigger for the first 10 accounts, and no answer to "what if we fail the audit twice." "No partial launch" with no limit on re-runs is not a leadership position; it is an open-ended slip.
- **M1 has gaps too.** Robustness is a top-3 trust metric with zero evidence behind it. The P95 2.0s latency target has no measured baseline and sits next to a pre-send hold and source checking that will cost seconds.
- **M5's budget doesn't add up.** It leaves $50K and one Level 3 slot unexplained, and it never totals the L2 and L1 spend.

(c) Add one section: **"After Nov 9: residual risk and rollout."**
1. Turn the audit's bound into a per-answer and per-account number. If 1% per claim is not board-safe, raise the audit size until it is.
2. Set explicit criteria for going from 10 to 50 accounts: live faithfulness sampling on production traffic, a kill switch, a named owner, and a client-notification playbook for the first escaped fabrication.
3. Set a hard stop: what we do and tell Sales if the audit fails twice, and what the slip costs against Q4 renewals.

Right now the memo proves we shouldn't ship. A CPO funds the version that also shows how we stay safe once we do.
