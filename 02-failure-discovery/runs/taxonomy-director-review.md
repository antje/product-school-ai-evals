# M2 Failure Taxonomy, Director of Product cold review (verbatim)

> Run 2026-09-17 in a fresh context (Agent tool, no session history, no tools allowed). Prompt: the course's self-review prompt ("You are a Director of Product reviewing a junior PM's Failure Taxonomy Canvas...") with the committed `failure-taxonomy.md` at e0fb1f2 pasted in, plus two sentences of scenario context (Ascend IQ, $50k+ accounts, top-50 launch next month, 20-row audit).
>
> Changed as a result: counts stated as "6 of 20" with a 95% interval instead of a rate; P0 defined by irreversibility and detectability, with frequency ordering fixes inside the bucket; the stale-cache root cause recorded separately; the open Go threshold and the missing citations named before Go/No-Go. Frequency stays pooled by Trust Tag, with the reasoning written into the taxonomy.

---

Director of Product review of the Ascend IQ Failure Taxonomy Canvas. Answered from the markdown alone, no tools used.

**1. The single weakest row: #2, Dropped Qualifier / Overstated Capability.**

Row #3 looks weakest at a glance (one row of evidence, an admitted guess for agentic mode), but it is honest about that. Row #2 is a P0 whose weaknesses are hidden, which is worse. Four problems:

- The frequency is borrowed. The column says 9 → HIGH, but only 4 of those 9 rows are this pattern. The PM pooled by Trust Tag, so rows #1 and #2 are counting the same 9 rows twice. The number would still clear the threshold of 3 on its own (4 rows), so the pooling gains nothing and costs credibility. Frequency should be counted per failure type, or the column is not measuring the row.
- The row merges two failure modes. "Dropped qualifier" (a true statement with its condition removed) and "overstated capability" (a claim that is simply too strong) need different fixes and different judges. A taxonomy row should be one mode.
- The examples contradict the description. The row says "nothing is false on its own," then lists "Austin as HQ" and "Z throttles at twice our rate." If Austin is not the HQ, that is false. If Z's throttle rate is not twice ours, that is a fabricated specific and belongs in row #1. Either the description or the row assignments are wrong.
- The Trust Tag is arguable. A dropped qualifier is a faithfulness or omission failure, not a hallucination. Tagging it #HALLUCINATION lets it ride on row #1's severity without earning it.

Minor, same theme: row #1 stuffs a second root cause (stale cache, $49 vs $59) into a fabrication row. Stale retrieval is its own failure type. If the audit log records both, the taxonomy should too.

**2. Is P0 defensible against "frequency should rule"? Yes, but the PM is defending it the hard way.**

The engineer's argument is: you fix what you hit most, and severity claims are speculative until a contract is actually lost. Two things the PM should say and currently does not:

- For row #1 the argument is moot. Frequency and severity point the same direction (5 of 20, contract-ending). Say that first and the debate collapses. The PM instead leads with "even if it happened once," which invites the engineer to attack the hypothetical.
- Frequency at the account level, not the query level, makes P0 stronger, not weaker. At a 25% per-answer rate, a VP who asks ten questions has a 1 - 0.75^10 = 94% chance of receiving at least one fabrication. Across 50 accounts, nearly every account hits one in week one. The engineer's own metric says this is a launch blocker.

Where the engineer is right, and the PM should concede it: frequency should rule within a severity band, and a 20-row sample cannot support "one in four" as a rate. 5/20 has a 95% confidence interval of roughly 9% to 49%. Use "5 of 20 audited answers," not "one in four," until there is a bigger sample. The PM should also say plainly that no contract has been lost yet; the contract-loss claim is a hypothesis grounded in what the client pays for (verified data), not in an observed churn event. Stating that openly is stronger than letting the engineer find it.

The principle to state: P0 means "blocks launch" and is decided by irreversibility and detectability. Fabricated specifics are invisible to the user and unrecoverable once caught in a boardroom. Frequency decides fix order inside the P0 bucket, not whether something is in it.

**3. Sharper Business Impact Statement.**

Current version is one long sentence, treats 5/20 as a rate, and ends on "the sales pitch for the other 49 accounts is dead," which is asserted, not derived. Rewrite:

> In 5 of 20 audited answers, Ascend IQ stated a specific the source never contained: a seat minimum, a funding stage, a confirmed speaker, a hex code. The user cannot tell these from verified facts, so each one travels into a board deck unchecked. Ascend sells verified data at $50k+ a year; one caught fabrication ends that promise for the account. At the observed rate, a VP asking ten questions has a better than 90% chance of hitting one (1 - 0.75^10), so the launch cohort of 50 accounts would nearly all encounter it in the first week. This is a launch gate, not a backlog item: the per-claim fabrication rate must be below [target] on a held-out set of at least [n] rows before any enterprise account sees the agent.

Every number in that version has its derivation beside it, and the two brackets are the honest gaps.

**4. The question the PM has not answered, and needs before Go/No-Go:**

What is the Go threshold for fabrication rate per claim, and how many held-out rows does it take to know you have cleared it?

The canvas names the metric (hallucination rate per claim) and the failure, but never the bar. Without the bar there is no Go/No-Go, only a feeling. And with 20 rows the PM cannot distinguish a 5% rate from a 20% rate after a fix ships; the confidence interval is too wide. Clearing a bar of, say, 2% per claim with any confidence needs a few hundred audited claims, not 20 rows. That has schedule consequences for a CPO who wants to launch next month, and the PM should surface that now rather than at the review.

Runner-up question, if there is room for a second: does the agent show sources, so a VP could catch a fabrication before the board does? Detectability is the hinge of the whole severity argument and the canvas never says whether it exists.

**Housekeeping:** the header repeats the file path on two consecutive lines, and the Agentic Mode column is blank ("·") for rows #1 and #2. Either fill it or drop the column for those rows with a one-line reason.
