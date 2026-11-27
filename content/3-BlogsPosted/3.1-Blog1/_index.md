---
title: "Blog 1"
date: 2026-11-25
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---


# Testing a serverless, event-driven flow (SQS + DLQ + DynamoDB) locally for $0 with LocalStack and Java 21

**Post link:** [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj/posts/2349976352433990/)

**Author(s):** Ngô Thị Kim Ngân · **Published:** November 27, 2026

### Summary
The post shows how to test an asynchronous flow (event → SQS → Java 21 Lambda → DynamoDB / SSM Parameter Store, with a DLQ) on a local machine with LocalStack before deploying with AWS SAM. The aim is to fix small bugs locally instead of on AWS, which is faster and avoids unexpected charges.

### Key points
- In the AWS SDK for Java v2, call `endpointOverride` with `http://localhost:4566` only in `local` mode. In the cloud, use the default endpoint.
- Test both paths: a valid payload must be written to DynamoDB and deleted from the main queue; a failing payload must land in the DLQ after the configured number of retries.
- Deploy with SAM only after the logic and configuration work locally.
