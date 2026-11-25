---
title: "Week 11 Worklog"
date: 2026-11-23
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

Week 11: 23/11/2026 – 27/11/2026 (project week W6, milestone M6 demo). This week is in progress: entries go up to 25/11/2026.

### Week 11 objectives

* Run the final eval with the `judge_v3` prompt and decide whether to enable VietOCR.
* Deploy the `ai` Lambda to the team's AWS account (BE-06) and measure real latency on Lambda (text, image, cold start).
* Build the cost estimate in AWS Pricing Calculator (SH-09).
* Close the finished AI issues and prepare the M6 demo.

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| 2 | - Fixed a flaky timing-based rules test (#177, merged 24/11) <br> - Reviewed and merged AI2's and BE's PRs: S3/DynamoDB/Telegram integration and preload (INT-01, #173), #174, URL network checks (AI-24b, #175), OCR hyphen fix (#176), VietOCR (#178) | 23/11/2026 | 24/11/2026 | [#177](https://github.com/ask-first-fcaj/ask-first/pull/177), [#173](https://github.com/ask-first-fcaj/ask-first/pull/173), [#174](https://github.com/ask-first-fcaj/ask-first/pull/174), [#175](https://github.com/ask-first-fcaj/ask-first/pull/175), [#176](https://github.com/ask-first-fcaj/ask-first/pull/176), [#178](https://github.com/ask-first-fcaj/ask-first/pull/178) |
| 3 | - Ran the final W6 eval with `judge_v3`, tried and dropped `judge_v4`, decided to enable VietOCR (#179) <br> - Maven cache on CI (#180) <br> - Built the `ai` Lambda image on GitHub Actions and added the function to the SAM template (BE-06, #181) <br> - Cross-account Bedrock calls via assume role (#182) <br> - Measured `ai` Lambda latency on the team account, updated OCR latency in ADR-003 (#183) <br> - Built the cost estimate in AWS Pricing Calculator (SH-09, commit in #169) <br> - Reviews: bot copy (SH-08, #157, approved), #170, #171, #172; merged #171 #172 | 24/11/2026 | 25/11/2026 | [#179](https://github.com/ask-first-fcaj/ask-first/pull/179), [#180](https://github.com/ask-first-fcaj/ask-first/pull/180), [#181](https://github.com/ask-first-fcaj/ask-first/pull/181), [#182](https://github.com/ask-first-fcaj/ask-first/pull/182), [#183](https://github.com/ask-first-fcaj/ask-first/pull/183), [#169](https://github.com/ask-first-fcaj/ask-first/pull/169), [#157](https://github.com/ask-first-fcaj/ask-first/pull/157), [#170](https://github.com/ask-first-fcaj/ask-first/pull/170), [#171](https://github.com/ask-first-fcaj/ask-first/pull/171), [#172](https://github.com/ask-first-fcaj/ask-first/pull/172), [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) |
| 4 | - Measured end-to-end p95 for image messages through the bot on Lambda (INT-02, #184) <br> - Opened #185 (post-deploy warm-up script, BE-10), #186 (resource clean-up guide, SH-10), #187 (security review lane A, `jackson` upgrade, `pip-audit`, BE-09b); these three are still open <br> - Merged #167 (`judge_v3`), #169 (Bedrock token measurement and cost estimate), #179, #181, #184; approved #170 <br> - Closed finished AI issues, including #56 (M1) and #165 | 25/11/2026 | 25/11/2026 | [#184](https://github.com/ask-first-fcaj/ask-first/pull/184), [#185](https://github.com/ask-first-fcaj/ask-first/pull/185), [#186](https://github.com/ask-first-fcaj/ask-first/pull/186), [#187](https://github.com/ask-first-fcaj/ask-first/pull/187), [lambda-p95.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md) |
| 5 | <!-- TODO: fill in (not yet happened) --> | 26/11/2026 | <!-- TODO --> | <!-- TODO --> |
| 6 | <!-- TODO: fill in (not yet happened; plan says M6 demo) --> | 27/11/2026 | <!-- TODO --> | <!-- TODO --> |

### Achievements

* Final eval (`judge_v3`, 24/11, 3 runs): recall 100%, false alarms 5% / 5% / 5%, level accuracy 99.3%. Still only 134 samples and 0 real samples ([eval-report-final.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-final.md)).
* On the `ai` Lambda (team account): warm text p95 3.5 s (100 messages), RAG p95 45 ms, cold start init p50 6.2 s, max 8.5 s ([lambda-p95.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md)).
* Image messages end-to-end p95 17.0 s (21 images), above the 15 s target; I accepted it because it is still under the ~30 s limit for images. RapidOCR on Lambda has p95 5.07 s, above ADR-003's 3 s target; VietOCR adds p95 3.42 s per image ([lambda-p95.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md), [ocr-vietocr.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-vietocr.md)).
* Cost estimate for 3,000 checks per month: about 6.2 USD/month without Free Tier, about 2.4 USD/month with Free Tier (whether the account still has Free Tier is not checked) ([cost-estimate.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/cost-estimate.md)).
<!-- TODO(not verified): recount merged PRs and reviews for the week once it ends -->

### Lessons learned

* Set the criteria before measuring a new prompt: `judge_v4` read clearly better but false alarms went up in 2 of 3 runs, on the same sample, so I dropped it ([eval-report-final.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-final.md)).
* The first init after deploying the `ai` Lambda hit the 10 s limit and had to rerun, so a warm-up right after deploy is needed (#185).
<!-- TODO(not verified): lesson about Docker images (OCI index, attestation) when deploying a Lambda container; only in personal notes, not in repo docs -->
