---
title: "Week 11 Worklog"
date: 2026-11-23
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

Week 11: 23/11/2026 – 27/11/2026 (project week W6, milestone M6 demo).

### Week 11 objectives

* Run the final eval with the `judge_v3` prompt and decide whether to enable VietOCR.
* Deploy the `ai` Lambda to the team's AWS account (BE-06) and measure real latency on Lambda (text, image, cold start).
* Build the cost estimate in AWS Pricing Calculator (SH-09).
* Close the finished AI issues and prepare the M6 demo.
* Fix the bot's button bugs before filming the demo video, and build the internship report site.

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| 2 | - Fixed a flaky timing-based rules test (#177, merged 24/11) <br> - Reviewed and merged AI2's and BE's PRs: S3/DynamoDB/Telegram integration and preload (INT-01, #173), #174, URL network checks (AI-24b, #175), OCR hyphen fix (#176), VietOCR (#178) | 23/11/2026 | 24/11/2026 | [#177](https://github.com/ask-first-fcaj/ask-first/pull/177), [#173](https://github.com/ask-first-fcaj/ask-first/pull/173), [#174](https://github.com/ask-first-fcaj/ask-first/pull/174), [#175](https://github.com/ask-first-fcaj/ask-first/pull/175), [#176](https://github.com/ask-first-fcaj/ask-first/pull/176), [#178](https://github.com/ask-first-fcaj/ask-first/pull/178) |
| 3 | - Ran the final W6 eval with `judge_v3`, tried and dropped `judge_v4`, decided to enable VietOCR (#179) <br> - Maven cache on CI (#180) <br> - Built the `ai` Lambda image on GitHub Actions and added the function to the SAM template (BE-06, #181) <br> - Cross-account Bedrock calls via assume role (#182) <br> - Measured `ai` Lambda latency on the team account, updated OCR latency in ADR-003 (#183) <br> - Built the cost estimate in AWS Pricing Calculator (SH-09, commit in #169) <br> - Reviews: bot copy (SH-08, #157, approved), #170, #171, #172; merged #171 #172 | 24/11/2026 | 25/11/2026 | [#179](https://github.com/ask-first-fcaj/ask-first/pull/179), [#180](https://github.com/ask-first-fcaj/ask-first/pull/180), [#181](https://github.com/ask-first-fcaj/ask-first/pull/181), [#182](https://github.com/ask-first-fcaj/ask-first/pull/182), [#183](https://github.com/ask-first-fcaj/ask-first/pull/183), [#169](https://github.com/ask-first-fcaj/ask-first/pull/169), [#157](https://github.com/ask-first-fcaj/ask-first/pull/157), [#170](https://github.com/ask-first-fcaj/ask-first/pull/170), [#171](https://github.com/ask-first-fcaj/ask-first/pull/171), [#172](https://github.com/ask-first-fcaj/ask-first/pull/172), [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) |
| 4 | - Measured end-to-end p95 for image messages through the bot on Lambda (INT-02, #184) <br> - Opened #185 (post-deploy warm-up script, BE-10), #186 (resource clean-up guide, SH-10), #187 (security review lane A, `jackson` upgrade, `pip-audit`, BE-09b), #188 (security review lane B, BE-09a), #190, #191, #192 <br> - Built the internship report site from FCAJ's Hugo template and filled in the worklog, proposal, events and workshop <br> - Merged #167 (`judge_v3`), #169 (Bedrock token measurement and cost estimate), #179, #181, #184, #185; approved #170 <br> - Closed finished AI issues, including #56 (M1) and #165 | 25/11/2026 | 25/11/2026 | [#184](https://github.com/ask-first-fcaj/ask-first/pull/184), [#185](https://github.com/ask-first-fcaj/ask-first/pull/185), [#186](https://github.com/ask-first-fcaj/ask-first/pull/186), [#187](https://github.com/ask-first-fcaj/ask-first/pull/187), [#188](https://github.com/ask-first-fcaj/ask-first/pull/188), [lambda-p95.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md), [fcj-workshop-template](https://github.com/thienluhoan/fcj-workshop-template) |
| 5 | - Merged #186 (clean-up guide), #187 and #188 (security reviews lane A and B), #192 (fixes LB-01, LB-02, LB-06 from lane B), #190 (tell the elder when a relative taps "I've made contact", FR13), #191 (summary file and architecture diagram for FCAJ Submission) <br> - Rejected images over 20 MB and replied "not supported yet" to voice messages (BE-34, #193) | 26/11/2026 | 26/11/2026 | [#186](https://github.com/ask-first-fcaj/ask-first/pull/186), [#187](https://github.com/ask-first-fcaj/ask-first/pull/187), [#188](https://github.com/ask-first-fcaj/ask-first/pull/188), [#190](https://github.com/ask-first-fcaj/ask-first/pull/190), [#191](https://github.com/ask-first-fcaj/ask-first/pull/191), [#192](https://github.com/ask-first-fcaj/ask-first/pull/192), [#193](https://github.com/ask-first-fcaj/ask-first/pull/193) |
| 6 | - Fixed bot buttons: "What to do now" and the `LOW_RISK` label in `/lichsu` (#194); "Alert family", "Call family", answering follow-up questions (#195) <br> - Found why the "Alert family" button said no relative was linked even after one was added: the webhook did not store the relative's chat id when linking. Fixed it, added a test (#196), deployed to `dev` and ran the warm-up <br> - Set up a budget action on the Bedrock account: when actual spend passes 10 USD/month, a policy denying `bedrock:*` is attached to the role the `ai` Lambda uses <br> - Prepared the demo video script; added the team's 3 blog posts to the report site | 27/11/2026 | 27/11/2026 | [#194](https://github.com/ask-first-fcaj/ask-first/pull/194), [#195](https://github.com/ask-first-fcaj/ask-first/pull/195), [#196](https://github.com/ask-first-fcaj/ask-first/pull/196), [Budget actions](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-controls.html) |

### Achievements

* Final eval (`judge_v3`, 24/11, 3 runs): recall 100%, false alarms 5% / 5% / 5%, level accuracy 99.3%. Still only 134 samples and 0 real samples ([eval-report-final.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-final.md)).
* On the `ai` Lambda (team account): warm text p95 3.5 s (100 messages), RAG p95 45 ms, cold start init p50 6.2 s, max 8.5 s ([lambda-p95.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md)).
* Image messages end-to-end p95 17.0 s (21 images), above the 15 s target; I accepted it because it is still under the ~30 s limit for images. RapidOCR on Lambda has p95 5.07 s, above ADR-003's 3 s target; VietOCR adds p95 3.42 s per image ([lambda-p95.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md), [ocr-vietocr.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-vietocr.md)).
* Cost estimate for 3,000 checks per month: about 6.2 USD/month without Free Tier, about 2.4 USD/month with Free Tier (whether the account still has Free Tier is not checked) ([cost-estimate.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/cost-estimate.md)).
* This week: 20 of my PRs were merged (#167, #169, #177, #179–#188, #190–#196); I reviewed 9 PRs (7 from AI2, 2 from BE).
* The relative-button fix (#196) is deployed to `dev`. The "Alert family" and "Call family" buttons and the automatic alert to relatives have not been retested on the bot since the fix; they will be tested before filming the demo video.
* The Bedrock account now has a cost stop: above 10 USD/month the bot falls back to rules-only mode instead of calling the LLM.

### Lessons learned

* Set the criteria before measuring a new prompt: `judge_v4` read clearly better but false alarms went up in 2 of 3 runs, on the same sample, so I dropped it ([eval-report-final.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-final.md)).
* The first init after deploying the `ai` Lambda hit the 10 s limit and had to rerun, so a warm-up right after deploy is needed (#185).
* Docker 29 pushes the image as an OCI index with attestations, which Lambda does not accept. Point the function at the digest of the `linux/amd64` manifest (or use `docker push --platform linux/amd64`).
* The test only checked the relative record's `status`, so it missed the missing `guardian_chat_id`, which is exactly the field the button handler and the worker filter on. Tests should check the fields that consumers of the data rely on (#196).
* AWS Budgets is not a hard cap: cost data updates only a few times a day, so spend can overshoot a little before the action runs, and the action has to be reversed by hand.
