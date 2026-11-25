---
title: "Week 10 Worklog"
date: 2026-11-16
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

Week 10: 16/11/2026 – 20/11/2026 (project week W5; milestone M4 MVP freeze due 20/11).

### Week 10 objectives

* Tune the fuse level thresholds (AI-10) and write the eval report for Gate M4 (AI-13).
* Cut the judge's remaining false alarms (new rule, `judge_v3` prompt).
* Add the FCAJ Workshop tasks to the plan: cost estimate (SH-09), resource clean-up (SH-10), milestone M7; start measuring Bedrock tokens for SH-09.

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| 2 | - Merged #150: Vietnamese manual grading for Gate G1 and a blind cross-check, recorded in ADR-002 (AI-03, closed issue #38) <br> - Reviewed BE's PR for unsupported input and `.apk` files (BE-35) | 16/11/2026 | 16/11/2026 | [#150](https://github.com/ask-first-fcaj/ask-first/pull/150), [#154](https://github.com/ask-first-fcaj/ask-first/pull/154), [ADR-002](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-002-g1-judge-model.md) |
| 3 | - Added SH-09 (AWS Pricing Calculator), SH-10 (clean-up) and milestone M7 (FCAJ Workshop submission) to the plan (#161) <br> - Tuned the fuse thresholds to 70/60/25 on 134 samples, recorded in ADR-005 (AI-10, #163) <br> - Wrote the Gate M4 eval report, with numbers split by sample source (AI-13, #164) <br> - Added the hard rule `R_OVERPAY_REFUND` for "I sent you too much, please send it back" messages (#166, issue #140) <br> - Opened #167 (`judge_v3` prompt, fixes a false alarm on a real police summons, issue #165) and #169 (Bedrock tokens per check for SH-09); both merged 25/11 <br> - Reviewed and merged AI2's PRs: 500-sample PII masking benchmark (AI-31, #158), OTP rule measurement (AI-30, #159), re-verified 20 hotlines (SH-05, #168); commented on the bot copy PR (SH-08, #157) <br> - Reviewed BE's PRs: SQS worker (BE-07, #148), "Bot was wrong" feedback (BE-30, #155), consent withdrawal and data deletion (BE-31, #156) | 17/11/2026 | 17/11/2026 | [#161](https://github.com/ask-first-fcaj/ask-first/pull/161), [#163](https://github.com/ask-first-fcaj/ask-first/pull/163), [#164](https://github.com/ask-first-fcaj/ask-first/pull/164), [#166](https://github.com/ask-first-fcaj/ask-first/pull/166), [#167](https://github.com/ask-first-fcaj/ask-first/pull/167), [#169](https://github.com/ask-first-fcaj/ask-first/pull/169), [#158](https://github.com/ask-first-fcaj/ask-first/pull/158), [#159](https://github.com/ask-first-fcaj/ask-first/pull/159), [#168](https://github.com/ask-first-fcaj/ask-first/pull/168), [#157](https://github.com/ask-first-fcaj/ask-first/pull/157), [#148](https://github.com/ask-first-fcaj/ask-first/pull/148), [#155](https://github.com/ask-first-fcaj/ask-first/pull/155), [#156](https://github.com/ask-first-fcaj/ask-first/pull/156), [ADR-005](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-005-nguong-muc-fuse.md), [eval-report-m4.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-m4.md) |
| 4 | <!-- TODO: fill in --> | 18/11/2026 | <!-- TODO --> | <!-- TODO --> |
| 5 | <!-- TODO: fill in --> | 19/11/2026 | <!-- TODO --> | <!-- TODO --> |
| 6 | <!-- TODO: fill in --> | 20/11/2026 | <!-- TODO --> | <!-- TODO --> |

### Achievements

* Gate M4 report (17/11, `judge_v2`, 3 runs): recall 100% (90/90 scam messages), false alarms 5% / 10% / 10% on 20 benign-but-suspicious messages. There are only 134 labeled samples instead of 300 and no real samples yet, so M4's "on 300 samples" criterion is **not met** ([eval-report-m4.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-m4.md)).
* Fuse thresholds 70/60/25 for the LLM path, 75/50/25 kept for the rules-only path ([ADR-005](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-005-nguong-muc-fuse.md)).
* Measured an LLM token cost of about 0.000753 USD per text check (`judge_v2`, 133 checks) for the SH-09 estimate ([cost-estimate.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/cost-estimate.md), section 1).
* 5 of my PRs were merged this week; I reviewed 8 PRs (4 from AI2, 4 from BE).

### Lessons learned

* Picking thresholds is not just the headline number: D = 60 had the best level accuracy in one run but marked 3 benign messages `DANGEROUS`, which would wrongly alert family members. The script does not score that, so I picked 70/60/25 by hand ([ADR-005](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-005-nguong-muc-fuse.md)).
* Applying the new thresholds to the rules-only path made the CI eval gate fail (rules-only recall dropped from 22.2% to 17.8%), so I kept separate thresholds per path.
