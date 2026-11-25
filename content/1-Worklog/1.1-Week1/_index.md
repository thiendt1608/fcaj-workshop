---
title: "Week 1 Worklog"
date: 2026-09-14
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

Week 1: 14/09/2026 – 18/09/2026.
<!-- draft: filled in from the usual FCAJ schedule; the project repo has no evidence for this week (first commit 18/10/2026); correct it to match what you actually did before submitting -->

### Week 1 objectives

* Learn the FCAJ rules, schedule and how work is submitted.
* Create and secure a personal AWS account: MFA, an IAM user, AWS Budgets.
* Get an overview of AWS services and global infrastructure (Regions, Availability Zones).

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| 2 | - Attended the FCAJ onboarding, read the rules and grading criteria <br> - Met the mentors and the other interns | 14/09/2026 | 14/09/2026 | [Rules FCAJ](https://hcm-rules.awsfcaj.com/) |
| 3 | - Created an AWS account and enabled MFA on the root user <br> - Created an IAM user and group for daily work instead of using root | 15/09/2026 | 15/09/2026 | [IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html), [MFA](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html) |
| 4 | - Set up AWS Budgets for cost alerts <br> - Learned about Billing and the Free Tier | 16/09/2026 | 16/09/2026 | [AWS Budgets](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html) |
| 5 | - Read the AWS overview: Regions, Availability Zones, edge locations, the shared responsibility model | 17/09/2026 | 17/09/2026 | [Overview of AWS](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/introduction.html) |
| 6 | - Installed the AWS CLI and set up a named profile for the IAM user <br> - Tried read-only commands such as `aws sts get-caller-identity` and `aws s3 ls` | 18/09/2026 | 18/09/2026 | [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) |

### Achievements

* A personal AWS account with MFA, its own IAM user and a cost-alert budget.
* The AWS CLI works with a named profile, without root access keys.

### Lessons learned

* Setting a budget and enabling MFA on day one is far cheaper than dealing with a surprise bill or a leaked account.
