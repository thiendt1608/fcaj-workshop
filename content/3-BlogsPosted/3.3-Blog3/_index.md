---
title: "Blog 3"
date: 2026-11-25
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---


# Designing a cost-optimized IoT data pipeline (MQTT → AWS IoT Core → Glue → S3) for small projects

**Post link:** [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2351133745651584/)

**Author(s):** Ngô Thị Kim Ngân · **Published:** November 27, 2026

### Summary
The post covers how IoT devices that keep sending small JSON messages pile millions of files into S3, which drives up request costs and slows Athena queries. The serverless fix has two stages: the IoT Core Rule Engine writes raw data straight to S3 in time partitions, and a daily Glue job merges the files and converts them to Parquet. Raw files are deleted after 7 days by an S3 Lifecycle rule.

### Key points
- Use the IoT Core Rule Engine (filtering with SQL) to write directly to S3 under `raw-data/year=YYYY/month=MM/day=DD/hour=HH/...`, with no EC2 or Lambda in between.
- EventBridge Scheduler runs a Glue job at the end of each day: it reads the previous day's JSON, cleans the data types, merges it and writes Snappy-compressed Parquet to a curated bucket.
- An S3 Lifecycle rule deletes raw JSON after 7 days, which still leaves enough for debugging.
- By the post's own estimate (10 million messages a month): storage drops by about 85%, file count goes from 10 million to about 30, and the data Athena scans to read one column drops by about 98%.
- For small data, use Glue Python Shell with `awswrangler` instead of Spark. With Spark, choose G.1X workers and cap them at 2.
