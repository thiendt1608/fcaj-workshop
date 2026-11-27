---
title: "Blog 2"
date: 2026-11-25
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---


# Lambda + EventBridge: using event-driven architecture to refresh data every day

**Post link:** [AWS Study Group](https://www.facebook.com/groups/660548818043427/posts/2345717436193215/)

**Author(s):** Võ Đức Trí · **Published:** November 27, 2026

### Summary
The post builds a static page that refreshes fuel prices, gold prices, esports scores and the weather on its own, instead of opening several tabs every morning. EventBridge Scheduler invokes a collector Lambda at a separate rate for each source; the collector writes JSON to S3; the S3 event goes through EventBridge to a renderer Lambda that rebuilds `index.html` and invalidates CloudFront. The parts never call each other directly, so one failing source does not break the page.

### Key points
- One shared collector; each schedule sends its own source name as the payload. Scheduler supports time zones, a retry policy and a DLQ.
- The collector normalizes data into `data/<source>.json`. A failing source raises an error and does not overwrite the old file, so the page keeps the last good data.
- The EventBridge rule must filter on the `data/` prefix. Otherwise the `index.html` written by the renderer triggers the renderer again, forever.
- S3 delivers events at least once, so the renderer must be idempotent: it always rebuilds the whole page from current data and overwrites it.
- Each section shows its `updatedAt` so readers can tell which numbers are stale.
