---
title: "Week 12 Worklog"
date: 2026-11-30
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

Week 12: 30/11/2026 – 04/12/2026. The Workshop was submitted before the 30/11/2026 deadline, so this week I worked from the office on self-study, reviewed the project and cleaned up what was left over.

### Week 12 objectives

* Self-study at the office: revisit the AWS services used in the project and read more about serverless design.
* Review the whole project after submission: code, documentation, report site.
* Clean up: old branches, unused images and logs, and check AWS costs.

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| 2 | - Reviewed the report site after submission: reread the worklog, proposal and workshop in both languages, fixed wording and broken links | 30/11/2026 | 30/11/2026 | [Report site](https://thiendt1608.github.io/fcaj-workshop/) |
| 3 | - Reviewed the project repo: compared the README, ADRs and `docs/` against the current code, filed issues for mismatches <br> - Deleted merged branches locally and on GitHub | 01/12/2026 | 01/12/2026 | [ask-first](https://github.com/ask-first-fcaj/ask-first), [docs/](https://github.com/ask-first-fcaj/ask-first/tree/main/docs) |
| 4 | - Self-study: read AWS Well-Architected, focusing on the Security and Cost Optimization pillars, and compared them with the Ask First architecture | 02/12/2026 | 02/12/2026 | [AWS Well-Architected](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html) |
| 5 | - Self-study: read the Serverless Applications Lens (Lambda, API Gateway, DynamoDB) <br> - Cleaned up leftovers: old ECR images of the `ai` Lambda, unused log groups; reviewed Cost Explorer and Budgets | 03/12/2026 | 03/12/2026 | [Serverless Lens](https://docs.aws.amazon.com/wellarchitected/latest/serverless-applications-lens/welcome.html), [ECR lifecycle policies](https://docs.aws.amazon.com/AmazonECR/latest/userguide/LifecyclePolicies.html), [CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html), [Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html) |
| 6 | - Wrapped up the project with the team: what was done, what was postponed (voice, VLM, multi-turn), what to do if the project continues <br> - Agreed on when to delete the `dev` stack using the SH-10 guide, once the demo is no longer needed for grading | 04/12/2026 | 04/12/2026 | [#186](https://github.com/ask-first-fcaj/ask-first/pull/186), [eval-report-final.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-final.md) |

### Achievements

* The report site and the project repo were reviewed after submission; places where the docs did not match the code were filed as issues.
* The repo is tidier: merged branches deleted, extra ECR images and log groups cleaned up.
* A clearer view of the project's serverless architecture through Well-Architected and the Serverless Lens, especially on security and cost.
* The `dev` stack stays up so the bot keeps running during grading; it will be deleted with the SH-10 guide when the team agrees.

### Lessons learned

* Reviewing after submission still pays off: docs written in a rush drift from the code, and a calm reread catches it more easily.
* Clean-up is better done gradually during the project (ECR lifecycle policies, log retention) instead of all at the end.
