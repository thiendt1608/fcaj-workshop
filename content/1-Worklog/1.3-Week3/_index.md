---
title: "Week 3 Worklog"
date: 2026-09-28
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

Week 3: 28/09/2026 – 02/10/2026.
<!-- draft: filled in from the usual FCAJ schedule; the project repo has no evidence for this week (first commit 18/10/2026); correct it to match what you actually did before submitting -->

### Week 3 objectives

* Learn S3, RDS and DynamoDB and when to pick each.
* Get familiar with the serverless building blocks: Lambda, SQS, CloudWatch.

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| 2 | - Learned S3: buckets, storage classes, versioning, lifecycle <br> - Hosted a static website on S3 | 28/09/2026 | 28/09/2026 | [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html), [S3 static website](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html) |
| 3 | - Compared RDS and DynamoDB <br> - Created an on-demand DynamoDB table and designed `PK`/`SK` keys from the queries | 29/09/2026 | 29/09/2026 | [Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html), [DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html) |
| 4 | - Learned Lambda: handler, timeout, memory, cold start <br> - Wrote a small Python function and read its logs in CloudWatch | 30/09/2026 | 30/09/2026 | [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html), [CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) |
| 5 | - Learned SQS: visibility timeout, dead-letter queues <br> - Connected SQS to Lambda as an event source | 01/10/2026 | 01/10/2026 | [Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html) |
| 6 | - Learned CloudWatch metrics, alarms and log groups <br> - Set an alarm for messages in the DLQ and cleaned up the week's resources | 02/10/2026 | 02/10/2026 | [CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) |

### Achievements

* Built an SQS → Lambda flow with a DLQ and an alarm, with logs in CloudWatch.
* Can design DynamoDB keys from the queries.

### Lessons learned

* With DynamoDB, list the queries first and pick the keys after; the other way round ends with extra indexes or full-table scans.
