# First LLM-as-a-Judge Eval, Module 1

## Version A, Concise, system prompt used

```
You are an executive briefing assistant.
Summarize in exactly 3 bullet points under 60 words. No preamble, no extra text.
```

## Version B, Narrative, system prompt used

```
You are a PR communications assistant.
Write a 100-word narrative summary highlighting wins first, then risks and next steps. Keep a positive tone. No bullets.
```

Both run against the same user message: the Q4 Marketing Campaign Update email from Priya (social engagement up 25% MoM, newsletter open rate down from 42% to 35%, product video delayed to November pending legal approval, tracking links due Friday).

## Eval setup, dataset name + judge model/family

- Harness: `01-evaluation-strategy/eval_lab.ipynb`, plain Python, keys loaded from a gitignored `.env`. Executed end to end with `jupyter nbconvert --execute`; outputs are stored in the notebook.
- Generator: `gpt-4.1-nano` (OpenAI), default temperature. Chosen as a fast, cheap chat model, which is all the summary task needs.
- Judge: `claude-opus-5` (Anthropic). A different model family from the generator, which avoids self-preference bias, and more capable than the model it grades.
- Dataset `Module1Output`: 22 rows. 20 starter rows from the cold-start prompt below (`fixtures/module1-starter-dataset.csv`, with the generating model's first-pass labels) plus Version A and Version B.
- Evaluator: Conciseness LLM-as-a-Judge. Per row it returns `fidelity_pass`, `brevity_pass`, a score of 1 (good) or 0 (bad) and a one-sentence reason, under the golden-set criteria below. Results in `fixtures/module1-judge-results.csv`.
- A second judge call compares A and B head to head and names a winner.

Results:

| Row | Score | Decisive reason from the judge |
|---|---|---|
| Version A (37 words) | 0 | Drops the 42% baseline and the legal-approval dependency behind the November delay |
| Version B (102 words) | 0 | Recasts "delayed" as "on track to launch in November", adds unsupported optimism |
| Head to head | TIE | Both fail at the same gate. In the judge's words, A leaves the CEO under-informed and B leaves the CEO misinformed |
| 20 starter rows | 10 good, 10 bad | Judge agrees with the first-pass label on 20/20 rows |

The run shows that prompt design is a measurable lever, and the measurement cuts both ways. The concise prompt buys brevity by dropping facts; the narrative prompt buys polish by softening risk. Neither system prompt clears the bar on its own, so the next iteration is a prompt change, not a model change.

The shape of the rubric is the same shape as the hallucination-rate metric in the strategy canvas: every claim traceable to the source, or the answer fails, and only then does form get scored. This harness is the pattern Ascend IQ's evals will follow.

Two things I learned about the judge itself. First, rubric wording matters more than I expected: with the same judge, one clause rewritten (what counts as a preserved blocker) moved agreement with the starter labels from 13/20 to 15/20. Second, judge choice matters as much: with the rewritten rubric, `gpt-5.5` (same family as the generator) reached 15/20 and failed rows for narrative timestamps like "the pilot closed yesterday"; `claude-opus-5` reached 20/20 on the identical rubric. The judge is part of the eval. The module warns that untuned judges default to "everything is great"; this one did the opposite and failed rows for detail a CEO would never miss, until the rubric said what a preserved blocker means.

All three runs are in `runs/`, outputs verbatim.

## Cold-start, the prompt you used to seed a starter dataset

Run once with `gpt-5.5`, fresh context, no system prompt:

```
Generate 20 example rows for evaluating executive email-summary quality. Each row: (1) a short internal business email to a CEO, 60 to 120 words, with at least one metric, one risk or delay, and one deadline; (2) a candidate summary of it; (3) a first-pass label "good" or "bad"; (4) a one-line reason. Make about half "good": concise, every metric and deadline preserved, no invented facts. Make the other half "bad" in varied ways: drops the deadline, changes a number, adds a claim not in the email, or is padded past 100 words. Vary the domains (marketing, sales, engineering, finance, ops). Return a markdown table with columns id, email, summary, label, reason.
```

The model returned 20 rows, 10 good and 10 bad, across the five domains, with every bad type represented (dropped deadline, changed number, invented claim, padding past 100 words, dropped metric). It did not honour the email length spec: emails came out at 49 to 66 words. The "good" reasons are formulaic ("preserves the metric, risk, and deadline"), so those labels still need a human check.

## Definition of good vs bad (golden-set criteria)

The reader is a CEO who acts on the summary without opening the email. A summary is judged in two ordered tests.

**Test 1, fidelity. A summary that fails this test fails overall.** The summary is bad if it:

- drops or changes any number or date in the email. A baseline figure counts: "down to 35%" without the 42% hides how big the drop was;
- drops or contradicts a blocker or dependency. A blocker counts as preserved when it is named at the level the CEO would act on ("legal review may delay launch" preserves "legal review of the revised claims language could delay launch if comments come back late"); its qualifying condition is optional detail, not a required fact. This clause was added after the first judge run failed seven usable summaries for leaving out that kind of detail;
- adds anything the email does not say;
- softens or upgrades a risk. A 7-point drop is not "a slight dip". "Delayed" is not "moving closer to rollout".

**Test 2, brevity. Only for summaries that pass Test 1.** Good means under 60 words, the deadline or ask stated explicitly, and nothing the CEO cannot act on. Bullets or prose are both fine.

The exact wording the judge saw is `GOLDEN_CRITERIA` in `eval_lab.ipynb`. This section is the same rules with the reasoning behind them.

**What a summary must never do:** make the CEO feel better informed than they are. A short summary that drops the blocker and a polished one that softens the risk fail for the same reason.

Reflection on the two prompts: for a CEO who needs speed, A is the right shape and the wrong content; it needs one more clause per bullet (the baseline, the blocker). For a comms team that needs polish, B is the right register and the wrong instinct; "keep a positive tone" is what turned a delay into "on track". The fix for B is to drop that instruction, not to shorten it.

## Repo paths

- `01-evaluation-strategy/eval_lab.ipynb`: the executed notebook with all cell outputs (generation, head-to-head verdict, per-row scores).
- `01-evaluation-strategy/fixtures/module1-starter-dataset.csv`: the 20 starter rows with first-pass labels.
- `01-evaluation-strategy/fixtures/module1-judge-results.csv`: the 22 scored rows with fidelity, brevity, score and reason.
- `01-evaluation-strategy/runs/`: verbatim outputs of the A/B generation, the cold-start call, and the three judge runs.
