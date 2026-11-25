---
title: "Workshop"
date: 2026-11-25
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Ask First ("Hỏi Trước"): a serverless scam-check bot on AWS

#### Overview

**Ask First** is a Telegram bot that helps elderly people check a suspicious message before they act on it. The user forwards a text, a link or a screenshot to the bot. Within seconds the bot answers in plain Vietnamese: how risky the message is, which scam pattern it matches, and what to do next. If the risk is high, the bot can also alert a relative who has agreed to receive alerts.

The whole system is serverless and runs on AWS:

+ **AWS Lambda** (Java 21) receives Telegram updates through a **Function URL** (`webhook`) and processes jobs from **Amazon SQS** (`worker`), with a **dead-letter queue** for failed jobs.
+ A third Lambda, **`ai`**, is a **container image** (Python 3.12) that runs OCR and embedding models on CPU, applies rules and RAG, and calls **Amazon Bedrock** (Qwen3 32B, with gpt-oss-120b as backup) behind **Bedrock Guardrails**.
+ **Amazon DynamoDB** stores metadata with TTL, **Amazon S3** stores model weights and the knowledge-base index, **SSM Parameter Store** holds the bot token and webhook secret, and **Amazon CloudWatch** collects logs and the DLQ alarm.
+ Everything is defined as code with **AWS SAM** and deployed in `ap-southeast-1` (Singapore); Bedrock is called in `ap-southeast-2` (Sydney).

In this workshop you will rebuild the same system in your own AWS account, step by step, and finish with a working bot you can chat with on Telegram.

#### Content

1. [Overview](5.1-overview/)
2. [Prerequisites](5.2-prerequisites/)
3. [Deploy the infrastructure (SAM)](5.3-deploy-infrastructure/)
4. [Deploy the AI function](5.4-deploy-ai-function/)
5. [Connect Telegram](5.5-connect-telegram/)
6. [Testing and validation](5.6-testing-validation/)
7. [Monitoring](5.7-monitoring/)
8. [Cost](5.8-cost/)
9. [Clean up](5.9-cleanup/)
10. [Demo and lessons learned](5.10-demo-lessons/)
