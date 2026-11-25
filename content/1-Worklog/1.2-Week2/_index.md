---
title: "Week 2 Worklog"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

Week 2: 21/09/2026 – 25/09/2026.
<!-- draft: filled in from the usual FCAJ schedule; the project repo has no evidence for this week (first commit 18/10/2026); correct it to match what you actually did before submitting -->

### Week 2 objectives

* Understand IAM policies, roles and least privilege.
* Build a VPC with public/private subnets and run EC2 in it.
* Clean up resources after every lab.

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| 2 | - Read the structure of IAM policy JSON; users vs groups vs roles <br> - Wrote a policy that only allows reading one S3 bucket | 21/09/2026 | 21/09/2026 | [IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) |
| 3 | - Built a VPC across 2 AZs: public/private subnets, route tables, Internet Gateway | 22/09/2026 | 22/09/2026 | [Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html) |
| 4 | - Compared Security Groups and Network ACLs <br> - Ran EC2 in a public subnet and connected over SSH with a key pair | 23/09/2026 | 23/09/2026 | [Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html), [Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) |
| 5 | - Learned instance types, EBS, AMIs, user data <br> - Attached an IAM role to EC2 instead of storing access keys on the instance | 24/09/2026 | 24/09/2026 | [Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) |
| 6 | - Reviewed the week; deleted the EC2 instance, NAT Gateway and VPC <br> - Noted the items billed by the hour even when idle (NAT Gateway, unattached Elastic IP) | 25/09/2026 | 25/09/2026 | [Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html), [AWS Budgets](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html) |

### Achievements

* Built a multi-AZ VPC, ran EC2 in it, and cleaned everything up after the lab.
* Gave EC2 permissions through an IAM role, with no access keys on the instance.

### Lessons learned

* A NAT Gateway is billed by the hour even with no traffic. This later became one reason the project runs Lambda without a VPC ([infra/README.md](https://github.com/ask-first-fcaj/ask-first/blob/main/infra/README.md), rule D1).
