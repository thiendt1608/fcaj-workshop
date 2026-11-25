---
title: "Week 9 Worklog"
date: 2026-11-09
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

Week 9: 09/11/2026 – 13/11/2026 (project week W4).

### Week 9 objectives

* Run real Bedrock calls: the Qwen3 32B spike (AI-02) and the judge-model benchmark to close Gate G1 (AI-03).
* Add a prompt-injection layer (AI-08), explanation grading (AI-11) and a red-team set (AI-14).
* Review AI2's RAG and CI eval-gate PRs.

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| 2 | - Reviewed and approved BE's PR for SAM dev infrastructure and Telegram integration (BE-03, BE-14, BE-20, BE-21, BE-24, BE-25, BE-26) | 09/11/2026 | 09/11/2026 | [PR #131](https://github.com/ask-first-fcaj/ask-first/pull/131) |
| 3 | - Prepared the Gate G1 benchmark: wrote the script that runs 4 models on the 100 eval samples and computes recall, false alarms and the valid-JSON rate <br> - Drafted the 34 `hard` red-team samples (AI-14) <!-- draft: filled in without git evidence; correct it before submitting --> | 10/11/2026 | 11/11/2026 | [#133](https://github.com/ask-first-fcaj/ask-first/pull/133), [#137](https://github.com/ask-first-fcaj/ask-first/pull/137) |
| 4 | - Ran the Bedrock Qwen3 32B spike, closed ADR-001 and set `model_id` (AI-02, #132) <br> - Benchmarked 4 models on 100 eval samples and closed Gate G1 in ADR-002: keep Qwen3 32B with thinking off (AI-03, #133); added a Gemini reference point (#141) and gpt-oss-120b as backup (#142) <br> - Decided not to enable `outputConfig` JSON schema for the judge, since the prompt alone gave valid JSON in 99/100 calls (AI-04, #134) <br> - Two-layer prompt-injection detection: local rules + Amazon Bedrock Guardrails (AI-08, #135) <br> - Explanation grading with LLM-as-a-judge and a manual grading sheet (AI-11, #136); added 34 hard red-team samples and an error analysis of the real pipeline (AI-14, #137) <br> - Judge prompt fixes: forms of address (`judge_v2`, #144) and no longer dropping the whole answer on unknown `requested_actions` (#145, issue #138) <br> - Fixed CI so the Java contract test runs `AiContractTest`, merged #104 (G0) <br> - Reviewed and merged AI2's PRs: AI2's manual scores for AI-11 (#146), local RAG (AI-29, #147) <br> - Evening: opened #149 and #150 | 11/11/2026 | 11/11/2026 | [#132](https://github.com/ask-first-fcaj/ask-first/pull/132), [#133](https://github.com/ask-first-fcaj/ask-first/pull/133), [#134](https://github.com/ask-first-fcaj/ask-first/pull/134), [#135](https://github.com/ask-first-fcaj/ask-first/pull/135), [#136](https://github.com/ask-first-fcaj/ask-first/pull/136), [#137](https://github.com/ask-first-fcaj/ask-first/pull/137), [#141](https://github.com/ask-first-fcaj/ask-first/pull/141), [#142](https://github.com/ask-first-fcaj/ask-first/pull/142), [#144](https://github.com/ask-first-fcaj/ask-first/pull/144), [#145](https://github.com/ask-first-fcaj/ask-first/pull/145), [#104](https://github.com/ask-first-fcaj/ask-first/pull/104), [#146](https://github.com/ask-first-fcaj/ask-first/pull/146), [#147](https://github.com/ask-first-fcaj/ask-first/pull/147), [ADR-001](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-001-bedrock-qwen3.md), [ADR-002](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-002-g1-judge-model.md), [Bedrock structured outputs](https://docs.aws.amazon.com/bedrock/latest/userguide/structured-output.html), [Bedrock Guardrails languages](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-supported-languages.html) |
| 5 | - Merged #149: rules now catch OTP requests that do not name the code, and requests for photos of ID cards (AI-14, issue #139) <br> - Reviewed and merged AI2's PRs: RAG wired into `RealPipeline` (INT-03, #151), eval gate on PRs (AI-09, #153) | 12/11/2026 | 12/11/2026 | [#149](https://github.com/ask-first-fcaj/ask-first/pull/149), [#151](https://github.com/ask-first-fcaj/ask-first/pull/151), [#153](https://github.com/ask-first-fcaj/ask-first/pull/153) |
| 6 | - Reviewed and approved BE's double opt-in family linking PR (BE-28) | 13/11/2026 | 13/11/2026 | [PR #152](https://github.com/ask-first-fcaj/ask-first/pull/152) |

### Achievements

* Qwen3 32B on Bedrock (thinking off) is the judge model. Spike from the dev machine: p50 1.39 s, p95 1.58 s over 30 calls ([ADR-001](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-001-bedrock-qwen3.md)). On 100 samples: 99% valid JSON; the morning run met both recall ≥ 90% and false alarms ≤ 10%, the 5 afternoon runs had 15% false alarms (3/20) and 98.3% recall ([ADR-002](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-002-g1-judge-model.md)). The false-alarm part moved to AI-13.
* The `hard` red-team set has 34 samples (30 scams, 4 tricky benign); labels were drafted by AI and not yet reviewed by a second person, so it is not counted in the gate ([ai14-redteam.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ai14-redteam.md)).
* 12 of my PRs were merged this week; I reviewed 6 PRs (4 from AI2, 2 from BE).

### Lessons learned

* Same code, same prompt, yet Qwen3's false alarms moved by 1 sample between the morning and afternoon runs. With a denominator of 20, one sample is 5 percentage points, so I run several times and record the spread instead of trusting one run ([ADR-002](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-002-g1-judge-model.md)).
* Thinking mode is not worth it: recall went up by 1 sample, false alarms went from 10% to 45%, and latency nearly doubled (ADR-002).
