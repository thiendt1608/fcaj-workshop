---
title: "Blogs Posted"
date: 2026-11-25
weight: 3
chapter: false
pre: " <b> 3. </b> "
---


The team's three shared blog posts on [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj) (FCAJ rule: 3 posts per team, −0.1 per missing post).

### [Blog 1 — Testing a serverless, event-driven flow (SQS + DLQ + DynamoDB) locally for $0 with LocalStack and Java 21](3.1-blog1/)
Emulate SQS, the DLQ, DynamoDB and SSM with LocalStack to test both the happy path and the DLQ path of a Java 21 Lambda on a local machine, then deploy with AWS SAM.

### [Blog 2 — Lambda + EventBridge: using event-driven architecture to refresh data every day](3.2-blog2/)
A self-updating static page built from EventBridge Scheduler, a collector Lambda, S3 events routed through EventBridge and a renderer Lambda. Each part runs and fails on its own; the renderer is idempotent and only reacts to the `data/` prefix to avoid a loop.

### [Blog 3 — Designing a cost-optimized IoT data pipeline (MQTT → AWS IoT Core → Glue → S3) for small projects](3.3-blog3/)
Write sensor data straight from IoT Core to time-partitioned S3, merge it into Parquet daily with Glue, and delete raw files after 7 days to avoid millions of small JSON files.
