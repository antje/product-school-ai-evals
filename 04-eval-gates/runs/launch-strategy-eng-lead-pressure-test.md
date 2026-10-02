# M4 Launch Strategy, Skeptical Eng Lead pressure test (cold run, verbatim)

> Run 2026-09-24 in a fresh context (Agent tool, no session history, tools disallowed). Prompt: the course's "Pressure-test with AI" prompt from the Launch Strategy Builder, with the committed `04-eval-gates/lab-2-launch-strategy.md` at commit f824240 pasted in.
>
> Changed as a result: a 10s latency ceiling with sign-off and expiry; human-confirmed claims, a fresh 300 on re-run and a snapshotted pricing source in the Hard gate; repeatable CI runs and floors read as case counts; per-account flags, wave exit criteria, a rollback trigger, an abandonment signal and streaming as the first latency fix.

---

Here is the Engineering Lead review of Section 4.0. I used only the text you gave me.

## The weakest line: "< 2.0s, PM override documented"

This is the only threshold with no floor under it. The override has no ceiling, no named approver beyond "PM", and no expiry. Your own 4.2 plan already ships at more than twice the target. The reason it gives, "four seconds still beats the hours these clients spend digging today", would also justify eight seconds, or twenty. Your Director will ask one question: "What is the number we refuse to ship at?" Today the document has no answer. That makes 2.0s a wish, and 24 hours from launch the override will be used because nothing stops it.

**Fix:**
> P95 end-to-end latency (request received to last token returned, server-side) ≤ 2.0s. Measured on ≥ 1,000 requests from the frozen production query mix at full 50-account peak concurrency (X RPS). **Override band:** 2.0s to [ceiling]s may ship only under staged rollout, with PM and Eng Lead sign-off recorded in the release ticket, and it expires at wave 2. **Above [ceiling]s P95 or [Y]s P99: blocked, no override.**

Derive the target and the ceiling. Don't pick them. Derive them from user tolerance, for example the wait at which users abandon or retry in the beta log, or a usability test. Then check your 4.2 example against the ceiling. If 4.2 is above it, the mitigation example has to change. If 4.2 is below it, write down why a 4-second wait is tolerable. Either way, the target and the story must agree.

## 1. Threshold hardness

**Hard: hallucination = 0% in 300.** The number is hard. The procedure around it is soft in four places:
- **The judge is imperfect, so a raw zero from it proves little.** κ 0.824 measures agreement with humans, not recall. It doesn't tell you how many fabrications the judge misses. Say who adjudicates judge-flagged claims (a human confirms each one) and report the judge's miss rate on the calibration set. The gate should count human-confirmed failures, not raw judge flags. Otherwise one false positive blocks launch and people learn to argue with the gate.
- **No rule for what happens after a failure.** If you fix the problem and re-run the same 300, the set is no longer held out. State that a failed audit needs a fresh sample, or a fresh portion of a larger pool.
- **"Live pricing source" changes over time.** Snapshot it with a timestamp for the audit. Otherwise a price change mid-audit creates or hides failures.
- **Claim segmentation is unspecified.** Who splits an answer into claims: a human, the judge, or a script? That choice changes the denominator.
- Also, "fabricated specifics" in the Metric column and "unsupported claims" in the Threshold column are different definitions. Pick one. Legal will read the difference.

The stats are right: the rule of three gives 3/300 = 1% and 3/20 = 15%. Keep that sentence. Legal will like it.

**Soft: latency.** Covered above. Also say whether a streamed answer counts to first token or to last token. Say what concurrency the test runs at, and how many requests.

**Advisory: brand voice ≥ 4.0/5.** Ambiguous in three ways:
- Is 4.0 the mean across the sample, or a per-email minimum?
- How big is the sample?
- Is the tone judge calibrated? No κ is given, unlike the hallucination judge.

The slang-list check is also misplaced. It is a PR check, but the CI Gate Policy never mentions it, so Engineering can't tell whether it blocks or warns.

## 2. Severity vs threshold

- **Hard at 0%:** matches the severity. Good.
- **Soft:** the mismatch is the missing ceiling. A soft gate means "review, with limits". An unbounded override turns it into an advisory.
- **Advisory:** fine to ship below 4.0, as long as a human sends the drafted email. State that assumption. If emails ever go out automatically, this becomes a Soft gate. Also say what happens when the score falls below 4.0, for example a review ticket within N days. A threshold with no consequence looks odd.

## 3. CI gate policy

The strongest part of the section. It is per-dimension, it names blocking vs warning, and PR #218 is a strong proof point. Four things would still stop Engineering from wiring it:

- **Frozen inputs don't make the outputs deterministic.** The candidate model and the LLM judge can both vary between runs. A rule that allows zero flipped cases on a system that varies will give random red checks. Then people re-run until green, which kills the gate. Specify:
  - temperature 0 or a fixed seed
  - pinned model and judge versions
  - a flake rule, for example 3 runs with majority vote per case, and no manual re-runs allowed.
- **Write the floors as case counts.** On 30 cases:
  - safety 98 → 30/30 (really 100%)
  - faithfulness 95 → 29/30
  - task completion 89 → 27/30
  - tool selection 87 → 27/30

  A safety "max regression 1" suggests precision a 30-case set doesn't have, since one case moves the score 3.3 points. Say "zero safety flips" instead.
- **Latency 70 and cost 70 aren't units.** Engineering can't compute a "latency score". Use ms at P95 on the fixtures, and $ per query, each with an allowed % regression against the `main` baseline.
- **Fixed floors vs moving baseline.** You say floors "sit at most one case below the main baseline", but the floors are written as fixed numbers. Which is it? Also define the baseline as `main` at the PR's merge-base. And state that no admin bypass is allowed on blocking dimensions.

Two more gaps:
- The price-vs-source code check is cheap and deterministic. It belongs in CI as a blocking check, not only in the release audit.
- CI allows 1 faithfulness failure in 30, while release allows 0 in 300. Add one sentence explaining the difference: CI catches regressions, release certifies the rate. Otherwise a reviewer will flag it as a contradiction.

## 4. Mitigation lever fit

Partly right, for the wrong reason, and not wireable yet.

- **The mechanism is backwards for latency.** Latency depends on load. A first wave of 10 accounts runs at about 20% of full load, so it will look faster than the 50-account launch will be. The wave doesn't "show us the real speed", it shows a flattering one. What staged rollout does do is limit how many accounts are affected. Say that, and measure load separately: keep the load test at 50-account concurrency.
- **Engineering doesn't know what to build.** Specify:
  - a per-account feature flag
  - which 10 accounts, and why those
  - what gets measured in wave 1: real P95, plus an abandonment or retry rate. That second one is your "puts people off" signal, and nothing measures it yet.
  - exit criteria for wave 2, for example P95 ≤ X for 7 days and abandonment ≤ Y%
  - a rollback trigger and who pulls it.
- **Nothing actually reduces latency.** Name at least one engineering lever to pair with the rollout: streaming the first token, caching repeat pricing lookups, or a smaller model for routing. Without one, rollout only buys time.

Keep "Accuracy still has to pass in full before the first account gets access." It is the right line and it protects the hard gate.

## Priority order
1. Put a ceiling, an approver and an expiry on the latency override, and derive the 2.0s target.
2. Add human adjudication and a fresh-sample re-test rule to the hallucination gate. Snapshot the pricing source.
3. Make CI runs repeatable, write the floors as case counts, and replace the latency and cost scores with ms and $.
4. Turn staged rollout into flags, exit criteria and a rollback trigger, with an abandonment metric and one latency-reduction lever.

One process note: your instruction said to use no tools, but this handoff call is the only way my answer reaches you. I used nothing else.
