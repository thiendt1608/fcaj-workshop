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
| 2 | <!-- TODO: fill in --> | 02/11/2026 | <!-- TODO --> | <!-- TODO --> |
| 3 | - Wrote the fuse stage that combines rule and LLM scores into a `Verdict` (AI-05, #119) <br> - Wrote the respond stage that builds the 4-level reply and the family alert (AI-06, #120) <br> - Cascade from the judge to a backup model and a rules-only mode; respond shows a maintenance note when the LLM fails (AI-07, #122, #123) <br> - Wired `RealPipeline` for text from masking to respond (INT-01, #124) <br> - Local OCR with RapidOCR, URL repair, QR decoding (AI-26, #126); safe short-link expansion with SSRF protection (AI-24b, #127) <br> - Merged #117 (KB cards) <br> - Reviewed AI2's PRs: OTP masking fix (#118, requested changes then approved), Telegram media download + `S3ArtifactStore` (#121), media download time limit (#125), text normalization + rules engine v1 (#128), offline URL intel (#129) <br> - Closed issues AI-05 (#51), AI-06 (#55), AI-07 (#67) | 03/11/2026 | 03/11/2026 | [#119](https://github.com/ask-first-fcaj/ask-first/pull/119), [#120](https://github.com/ask-first-fcaj/ask-first/pull/120), [#122](https://github.com/ask-first-fcaj/ask-first/pull/122), [#123](https://github.com/ask-first-fcaj/ask-first/pull/123), [#124](https://github.com/ask-first-fcaj/ask-first/pull/124), [#126](https://github.com/ask-first-fcaj/ask-first/pull/126), [#127](https://github.com/ask-first-fcaj/ask-first/pull/127), [#118](https://github.com/ask-first-fcaj/ask-first/pull/118), [#121](https://github.com/ask-first-fcaj/ask-first/pull/121), [#125](https://github.com/ask-first-fcaj/ask-first/pull/125), [#128](https://github.com/ask-first-fcaj/ask-first/pull/128), [#129](https://github.com/ask-first-fcaj/ask-first/pull/129) |
| 4 | <!-- TODO: fill in --> | 04/11/2026 | <!-- TODO --> | <!-- TODO --> |
| 5 | <!-- TODO: fill in --> | 05/11/2026 | <!-- TODO --> | <!-- TODO --> |
| 6 | <!-- TODO: fill in --> | 06/11/2026 | <!-- TODO --> | <!-- TODO --> |

### Achievements

* The text pipeline is complete in code: normalize → mask PII → rules + URL intel → judge → fuse → respond, with a rules-only mode when the LLM fails.
* OCR is part of the pipeline code. On CI with the production model: exact URL 31/32 (97%) on 50 images, 17/18 (94%) on 16 independent images ([ADR-003](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-003-ocr-rapidocr.md), condition 3).
* 8 of my PRs were merged on 03/11; I reviewed 5 of AI2's PRs.
* <!-- TODO(not verified): whether milestone M1 (text E2E demo on the dev bot, due 06/11) was met on time; issue INT-01 (#56) was only closed on 25/11 -->

### Lessons learned

* <!-- TODO: fill in only if you have something real to say -->
