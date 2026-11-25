---
title: "Week 7 Worklog"
date: 2026-10-26
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

Week 7: 26/10/2026 – 30/10/2026 (project week W2). Some work carried over to Saturday 31/10.

### Week 7 objectives

* Finish Gate G0 (Java-side contract test) and close Gate G2 (OCR).
* Build the AI pipeline skeleton: Bedrock client (AI-02), eval harness (AI-01), judge prompt + post-check (AI-04), Lambda `ai` handler (AI-03b).
* Review AI2's first PRs (PII masking, KB v1).

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| 2 | - Prepared AI-02: read the Bedrock Converse API docs and botocore retry settings, sketched `models.yaml` for the judge, backup and vlm roles <!-- draft: filled in without git evidence; correct it before submitting --> | 26/10/2026 | 26/10/2026 | [Converse API](https://docs.aws.amazon.com/bedrock/latest/userguide/conversation-inference.html) |
| 3 | - Read BE's Java contract PR (#102) ahead of review, checking the Java models against the contract v1.0 JSON Schema and fixtures <!-- draft: filled in without git evidence; correct it before submitting --> | 27/10/2026 | 27/10/2026 | [#102](https://github.com/ask-first-fcaj/ask-first/pull/102) |
| 4 | - Reviewed and approved a BE PR: `TelegramAdapter`, Java contract models and handlers (SH-01) <br> - Closed issues Gate G0 (#2) and SH-01 (#3) | 28/10/2026 | 28/10/2026 | [PR #102](https://github.com/ask-first-fcaj/ask-first/pull/102) |
| 5 | - Wrote the Bedrock Converse client and the Qwen3 32B spike skeleton (AI-02); the spike could not run yet because there were no credentials <br> - Reviewed the AI team's task progress | 29/10/2026 | 30/10/2026 (merged) | [PR #103](https://github.com/ask-first-fcaj/ask-first/pull/103) |
| 6 | - Reworked OCR and eval: score OCR URLs on boundaries instead of substring match, added a workflow to rerun the benchmark on CI (#105); LABELING v2 by scenario code (#106); relabeled eval v0 by scenario code, validator now checks `scenario`/`provenance` (#107); approved the SH-02 scenario codes (#108); re-checked the labels of the 16 independent OCR images (#109) <br> - Opened the Java-side contract test gate for G0 (#104, merged 11/11) <br> - Reviewed AI2's PRs: PII masking (#110, requested changes then approved), KB v1 (#111, requested changes on 30/10 and 31/10, approved 31/10) <br> - On the evening of 30/10 opened 5 PRs, merged Saturday 31/10: closed Gate G2 in ADR-003 (#112), privacy policy draft v0.1 (SH-07, #114), `run_eval.py` harness computing recall and false alarms (AI-01, #113), judge prompt v1 + JSON parsing + LLM output post-check (AI-04, #115), Lambda `ai` handler + DynamoDB cache (AI-03b, #116) <br> - 31/10: opened a PR adding KB cards N2.2, N5.2 and fixing `advice` that was stronger than its source (AI-25, #117, merged 03/11) | 30/10/2026 | 31/10/2026 | [#104](https://github.com/ask-first-fcaj/ask-first/pull/104), [#105](https://github.com/ask-first-fcaj/ask-first/pull/105), [#106](https://github.com/ask-first-fcaj/ask-first/pull/106), [#107](https://github.com/ask-first-fcaj/ask-first/pull/107), [#108](https://github.com/ask-first-fcaj/ask-first/pull/108), [#109](https://github.com/ask-first-fcaj/ask-first/pull/109), [#110](https://github.com/ask-first-fcaj/ask-first/pull/110), [#111](https://github.com/ask-first-fcaj/ask-first/pull/111), [#112](https://github.com/ask-first-fcaj/ask-first/pull/112), [#113](https://github.com/ask-first-fcaj/ask-first/pull/113), [#114](https://github.com/ask-first-fcaj/ask-first/pull/114), [#115](https://github.com/ask-first-fcaj/ask-first/pull/115), [#116](https://github.com/ask-first-fcaj/ask-first/pull/116), [#117](https://github.com/ask-first-fcaj/ask-first/pull/117) |

### Achievements

* Gate G2 passed on accuracy; RapidOCR confirmed on 30/10 ([ADR-003](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-003-ocr-rapidocr.md)). Rerunning the benchmark on CI gave the same URL and CER numbers as on the dev machine.
* The AI pipeline skeleton is on `main`: Bedrock client, eval harness, judge prompt v1 with post-check, Lambda `ai` handler with cache.
* 11 of my PRs were merged on 30–31/10; I reviewed 3 PRs (1 from BE, 2 from AI2).
* Not done: Gate G1 (choose the LLM, due 30/10 in the plan) did not run this week; ADR-002 closed it on 11/11. Reason: there were no AWS credentials yet and Bedrock was not enabled in `ap-southeast-2` (BE-05, [#11](https://github.com/ask-first-fcaj/ask-first/issues/11)), so the AI-02 spike could not call a real model ([#103](https://github.com/ask-first-fcaj/ask-first/pull/103)).

### Lessons learned

* A wrong scoring rule makes numbers look better than they are: matching URLs as substrings counted truncated URLs as correct, so I switched to boundary matching ([#105](https://github.com/ask-first-fcaj/ask-first/pull/105)).
* Branch protection has two traps: merging `main` into a branch makes GitHub dismiss earlier approvals, so the PR needs approving again, and files under `/ai/` need an approval from the AI code-owner team. I now merge `main` into the branch first and ask for review after, so nobody has to approve twice.
