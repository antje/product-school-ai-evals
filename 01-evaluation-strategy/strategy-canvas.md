# AI Evaluation Strategy Canvas

> Repo file `ai-evals/01-evaluation-strategy/strategy-canvas.md` (the repo is your submission).
> Becomes the **Strategy Canvas** slide of the final pitch deck you assemble in Module 6.

## 1. Product Strategy, The Context

**Target user:** VP-level strategists and product leaders at Fortune 500 companies who pay $50k+ a year for verified market intelligence.

**Key use case:** Getting a specific, verified competitive insight (a pricing comparison, a summary of negative G2 reviews) from a plain-language question, without digging through the platform by hand.

**Value proposition:** Instant answers grounded in verified data, so the time between a strategic question and a defensible answer drops from hours to seconds.

## 2. Measurements, The Execution

**User promise.** For VP-level strategists at Fortune 500 accounts, Ascend IQ promises an answer they can put in front of their board without checking the source, in seconds instead of hours, so that Ascend keeps its $50k+ renewals through Q4.

**Top 3 trust metrics:**
- **Hallucination rate**, the share of factual claims in an answer that do not trace to a cited source in Ascend's verified data. A claim with no source, or a source that says something different, counts as a hallucination. Measured per claim, not per answer, so one invented number in an otherwise correct answer still registers.
- **Latency**, P95 time from question to complete answer. P95 rather than median because the VP remembers the one slow answer, and the value proposition only holds if every answer beats digging by hand.
- **Robustness**, the share of answers that stay correct and sourced when the question is messy, ambiguous, or out of scope. For a competitor Ascend has no data on, the correct behaviour is a refusal with a reason; a confident guess counts as a failure.

**Why these three:** Each one guards one clause of the promise. "Without checking the source" is hallucination rate: one fabricated competitor stat is the poor answer to the wrong client that costs a $50k renewal. "In seconds instead of hours" is latency: speed is the only reason to use Ascend IQ over the platform itself. Robustness is what makes the promise hold for real questions rather than demo questions; the top 50 accounts will not ask tidy ones. UX Trust is left out because it is the result of these three rather than something we can set directly. Fairness is left out because the brief describes one user group and one language; adding it would be inventing a requirement.

## 3. Strategic Trade-Offs, The Cost

### Trade-off 1 · Hallucination Rate ↔ Latency

We prioritize hallucination rate over latency because retrieval and source checking cost seconds, and we pay them. A wrong answer in front of a board ends a $50k relationship; a 4-second answer instead of a 2-second one is still hours faster than digging by hand. The promise says "in seconds", not "in two seconds".

### Trade-off 2 · Robustness ↔ Latency

We prioritize robustness over latency for the launch cohort. Concretely: when a question is ambiguous or out of scope, Ascend IQ asks a clarifying question or refuses with a reason instead of answering fast. That costs a round trip and some perceived speed. We accept it because the first 50 accounts are the most valuable relationships in the book; a confident wrong answer there does more damage than a clarifying question.

---
_AI Evaluation Strategy Canvas, M1 lab, AI Evals Certification._
