---
title: "Worklog"
date: 2026-09-14
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

This is my personal worklog for the 12-week internship. I am AI1 on the team project **Ask First (Hỏi Trước)**, a bot that helps people ask before acting on a suspicious message. My main work is the AI pipeline on AWS Lambda: rules, RAG, a judge on Amazon Bedrock, OCR for images, the eval set and the ADRs.

Each week is logged by day, with links to PRs, issues or docs in the [ask-first-fcaj/ask-first](https://github.com/ask-first-fcaj/ask-first) repo. Weeks 1–5 (before the project repo existed) have no PRs to link, so the references are AWS docs and project docs; week 12 is the plan.
<!-- TODO(confirm): whether to disclose use of an AI assistant (Claude Code) for code and docs (see bao-cao-claude-w2.md, check FCAJ rules) -->

**Week 1:** [Getting started with FCAJ, the AWS account and cloud basics](1.1-week1/)

**Week 2:** [IAM, VPC and EC2](1.2-week2/)

**Week 3:** [Storage, databases and serverless](1.3-week3/)

**Week 4:** [Forming the team, choosing Ask First and learning Bedrock](1.4-week4/)

**Week 5:** [Research concept, system architecture and the BE ↔ AI contract](1.5-week5/)

**Week 6:** [6-week plan, AI contract v1.0, eval set v0 and OCR spike](1.6-week6/)

**Week 7:** [Passed Gate G2, AI pipeline skeleton and URL scoring](1.7-week7/)

**Week 8:** [Text pipeline running in code, OCR re-measured](1.8-week8/)

**Week 9:** [Chose Bedrock Qwen3 32B (ADR-001), judge, Guardrails and red-team](1.9-week9/)

**Week 10:** [Fuse threshold tuning (ADR-005), Gate M4 eval report, token measurement for the cost estimate](1.10-week10/)

**Week 11:** [Final eval with `judge_v3`, `ai` Lambda on AWS, latency and cost estimate](1.11-week11/)

**Week 12:** [Resource cleanup and project wrap-up (planned)](1.12-week12/)
