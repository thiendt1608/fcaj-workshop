---
title: "Week 8 Worklog"
date: 2026-11-02
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

Week 8: 02/11/2026 – 06/11/2026 (project week W3; milestone M1 "Text E2E" due 06/11).

### Week 8 objectives

* Put all stages of the text pipeline (fuse, respond, cascade) together into `RealPipeline` (INT-01).
* Move OCR into the pipeline code (AI-26) and expand short links safely (AI-24b).
* Review AI2's PRs on the M1 critical path (rules engine, URL intel, media download).

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| 2 | - Reviewed AI2's OTP masking fix (mask every repeat, drop the O(n²) cost, skip `code` inside URLs) and commented on issue AI-22; AI2 opened PR #118 the same day <br> - Wrote the fuse, respond and cascade stages on separate branches and opened the PRs on the morning of 03/11 | 02/11/2026 | 03/11/2026 | [#41](https://github.com/ask-first-fcaj/ask-first/issues/41), [#118](https://github.com/ask-first-fcaj/ask-first/pull/118) |
| 3 | - Wrote the fuse stage that combines rule and LLM scores into a `Verdict` (AI-05, #119) <br> - Wrote the respond stage that builds the 4-level reply and the family alert (AI-06, #120) <br> - Cascade from the judge to a backup model and a rules-only mode; respond shows a maintenance note when the LLM fails (AI-07, #122, #123) <br> - Wired `RealPipeline` for text from masking to respond (INT-01, #124) <br> - Local OCR with RapidOCR, URL repair, QR decoding (AI-26, #126); safe short-link expansion with SSRF protection (AI-24b, #127) <br> - Merged #117 (KB cards) <br> - Reviewed AI2's PRs: OTP masking fix (#118, requested changes then approved), Telegram media download + `S3ArtifactStore` (#121), media download time limit (#125), text normalization + rules engine v1 (#128), offline URL intel (#129) <br> - Closed issues AI-05 (#51), AI-06 (#55), AI-07 (#67) | 03/11/2026 | 03/11/2026 | [#119](https://github.com/ask-first-fcaj/ask-first/pull/119), [#120](https://github.com/ask-first-fcaj/ask-first/pull/120), [#122](https://github.com/ask-first-fcaj/ask-first/pull/122), [#123](https://github.com/ask-first-fcaj/ask-first/pull/123), [#124](https://github.com/ask-first-fcaj/ask-first/pull/124), [#126](https://github.com/ask-first-fcaj/ask-first/pull/126), [#127](https://github.com/ask-first-fcaj/ask-first/pull/127), [#118](https://github.com/ask-first-fcaj/ask-first/pull/118), [#121](https://github.com/ask-first-fcaj/ask-first/pull/121), [#125](https://github.com/ask-first-fcaj/ask-first/pull/125), [#128](https://github.com/ask-first-fcaj/ask-first/pull/128), [#129](https://github.com/ask-first-fcaj/ask-first/pull/129) |
| 4 | - Rechecked `main` after 13 PRs merged on 03/11: ran the full `ai/` test suite and the rules-only eval on my dev machine <br> - Wrote the M1 demo script (a "traffic fine" message with a fake link) <!-- draft: filled in without git evidence; correct it before submitting --> | 04/11/2026 | 04/11/2026 | [implementation-plan.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/implementation-plan.md) |
| 5 | - While waiting for Bedrock access (BE-05): read the Bedrock Guardrails docs to prepare AI-08 and listed the prompt-injection patterns to block <!-- draft: filled in without git evidence; correct it before submitting --> | 05/11/2026 | 05/11/2026 | [Bedrock Guardrails](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails.html), [#11](https://github.com/ask-first-fcaj/ask-first/issues/11) |
| 6 | - Reviewed BE's SAM dev infrastructure and Telegram integration PR (#131) in depth: CI was green, but I pointed out several places that would fail when run for real on `dev`; the next day I checked the fix commits for the DynamoDB `PK`/`SK` keys, GSI1 and the testcontainers-based tests | 06/11/2026 | 07/11/2026 | [#131](https://github.com/ask-first-fcaj/ask-first/pull/131) |

### Achievements

* The text pipeline is complete in code: normalize → mask PII → rules + URL intel → judge → fuse → respond, with a rules-only mode when the LLM fails.
* OCR is part of the pipeline code. On CI with the production model: exact URL 31/32 (97%) on 50 images, 17/18 (94%) on 16 independent images ([ADR-003](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-003-ocr-rapidocr.md), condition 3).
* 8 of my PRs were merged on 03/11; I reviewed 5 of AI2's PRs.
* Milestone M1 (end-to-end text demo on the `dev` bot, due 06/11) was **not met on time**: the text pipeline code was done on 03/11, but Bedrock was not reachable yet (the spike ran on 11/11, [#132](https://github.com/ask-first-fcaj/ask-first/pull/132)) and the `ai` Lambda was only wired to S3, DynamoDB and Telegram in [#173](https://github.com/ask-first-fcaj/ask-first/pull/173) (23/11). Issue INT-01 ([#56](https://github.com/ask-first-fcaj/ask-first/issues/56)) was closed on 25/11.

### Lessons learned

* Merging 8 stacked PRs on the same day kept causing `CHANGELOG.md` conflicts ([#121](https://github.com/ask-first-fcaj/ask-first/pull/121), [#126](https://github.com/ask-first-fcaj/ask-first/pull/126), [#127](https://github.com/ask-first-fcaj/ask-first/pull/127)): after each merge I had to merge `main` back into the remaining branches. Next time I merge in dependency order and put the changelog entries in the last PR.
* Finished code is not a finished milestone: M1 needed to run on the `dev` bot with real Bedrock, and Bedrock access was someone else's task (BE-05). I should have pushed for that access back in week 6.
