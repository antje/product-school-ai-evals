# AI Evals: Final Project

[![Ascend IQ: ship or hold? HOLD. 6 of 20 beta answers stated a detail the source does not support; 97% chance a VP meets one within ten questions; 0 unsupported claims in 300 is the bar to ship.](assets/readme-banner.jpg)](https://antje.github.io/product-school-ai-evals/06-culture/lab-2-final-pitch.html)

> My final project for Product School's **AI Evals** certification. One evaluation system for a real LLM feature, from strategy, through failure discovery and an automated eval suite, to the gates and governance that let it ship safely.

## Executive path

| | |
|---|---|
| **Problem** | Ascend IQ answers plain-language competitive questions for VP strategists at Fortune 500 accounts who pay $50k+ a year for verified data. Its promise: an answer they can put in front of their board without checking the source. |
| **The call** | **HOLD** the launch to the top 50 accounts until it passes a zero-fabrication audit. [Memo](06-culture/lab-1-ship-hold-memo.md) · [Deck](https://antje.github.io/product-school-ai-evals/06-culture/lab-2-final-pitch.html) |
| **Evidence today** | 6 of 20 beta answers stated a detail the source does not support, and none cited a source. At 30% per answer, a VP asking ten questions meets one 97% of the time (1 − 0.7¹⁰). [Taxonomy](02-failure-discovery/failure-taxonomy.md) |
| **What is at stake** | At least $2.5M of annual renewals (50 accounts × the $50k floor), with the error found in a client's board deck rather than in our logs. |
| **The bar to ship** | 0 unsupported claims in a 300-claim held-out audit. By the rule of three that bounds the per-claim rate under 1% at 95% confidence; zero in the 20-row beta would only bound it under 15%. [Spec](03-eval-suites/lab-2-eval-spec.md) |
| **What is in place** | A three-layer eval suite where the judge caught 9 of 11 confirmed failures and the code checks 1 [Suite](03-eval-suites/lab-1-eval-suite.md); a judge at κ 0.824 with human reviewers, on the traces it was tuned on; a per-dimension CI gate that blocked a regressing PR (faithfulness 87 against a floor of 95); $150K of continuous coverage on fabrication and attribution. |
| **Biggest unknown** | Whether the fixes bring fabrication down, measured on claims the judge was not tuned on. Behind it, the riskiest user assumption: that VPs open the citations that carry the residual risk, which wave 1 measures. |
| **Next test** | Fixes by Oct 23, judge recalibrated on a held-out set, audit from Oct 26, go/no-go Nov 9. Pass: the first 10 accounts go live behind a flag. Fail: fix and re-run on a fresh 300, with no partial launch. [Memo, next step](06-culture/lab-1-ship-hold-memo.md#next-step--decision-needed) |

**Data used.** Ascend IQ is the course scenario. The 20 beta answers, the latency log, PR #218 and the calibration traces come with it; the eval runs on them in this repo are real model calls. Dates from Oct 2 onward are a plan.

**Reviews.** Each module's cold review, run in a fresh context with the module's own critique prompt, is in that module's `runs/` folder, with what changed as a result at the top.

---

## Deliverables at a glance

| Module | Artifact | Status | File |
|---|---|---|---|
| M1 | **Evaluation Strategy Canvas** | ✅ | `01-evaluation-strategy/strategy-canvas.md` |
| M1 | **Eval harness proof** (links + screenshots) | ✅ | `01-evaluation-strategy/eval-harness-proof.md` |
| M2 | **Failure audit log** | ✅ | `02-failure-discovery/audit-log.md` |
| M2 | **Failure Taxonomy** | ✅ | `02-failure-discovery/failure-taxonomy.md` |
| M3 | **Runnable eval suite** (results) | ✅ | `03-eval-suites/lab-1-eval-suite.md` |
| M3 | **Trajectory eval** (scorecard) | ✅ | `03-eval-suites/lab-1b-trajectory.md` |
| M3 | **Judge calibration** (κ) | ✅ | `03-eval-suites/lab-judge-calibration.md` |
| M3 | **Eval Spec** (5-part spec + audience messages) | ✅ | `03-eval-suites/lab-2-eval-spec.md` |
| M4 | **Eval gate map** (severity × placement) | ✅ | `04-eval-gates/lab-1-gate-map.md` |
| M4 | **CI gate policy** (PR #218 replay) | ✅ | `04-eval-gates/lab-ci-gate-policy.md` |
| M4 | **Launch strategy** (release criteria + CI policy + mitigation) | ✅ | `04-eval-gates/lab-2-launch-strategy.md` |
| M5 | **Coverage matrix** | ✅ | `05-scale/lab-1-coverage-matrix.md` |
| M5 | **Eval budget** | ✅ | `05-scale/lab-2-budget-crisis.md` |
| M6 | **Ship / Hold memo** | ✅ | `06-culture/lab-1-ship-hold-memo.md` |
| M6 | **Final pitch deck** (generated HTML) | ✅ | `06-culture/lab-2-final-pitch.html` |

---

## Repo structure

```
ai-evals/
├── README.md                         ← this dashboard
├── 01-evaluation-strategy/           ← Module 1
│   ├── strategy-canvas.md            from the Strategy Canvas tool
│   ├── eval-harness-proof.md         from the first eval lab
│   └── runs/                         generation and judge runs
├── 02-failure-discovery/             ← Module 2
│   ├── audit-log.md                  scored audit of real outputs
│   ├── failure-taxonomy.md           prioritized failure modes
│   └── runs/                         Director cold review
├── 03-eval-suites/                   ← Module 3
│   ├── lab-1-eval-suite.md           runnable 3-layer suite results
│   ├── lab-1b-trajectory.md          trajectory scorecard
│   ├── lab-judge-calibration.md      judge calibration (κ)
│   └── lab-2-eval-spec.md            5-part eval spec + audience messages
├── 04-eval-gates/                    ← Module 4
│   ├── lab-1-gate-map.md             severity × pipeline-placement grid
│   ├── lab-ci-gate-policy.md         CI gate demo (PR #218 replay)
│   ├── lab-2-launch-strategy.md      release criteria + CI policy + mitigation
│   └── runs/                         Eng Lead pressure test
├── 05-scale/                         ← Module 5
│   ├── lab-1-coverage-matrix.md      coverage across product lines
│   ├── lab-2-budget-crisis.md        eval budget + trade-offs
│   └── runs/                         CPO and Principal PM reviews
└── 06-culture/                       ← Module 6
    ├── lab-1-ship-hold-memo.md       ship/hold recommendation
    ├── lab-2-final-pitch.html        generated pitch deck
    └── runs/                         VP critic review
```
