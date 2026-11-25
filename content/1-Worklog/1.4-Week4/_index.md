---
title: "Week 4 Worklog"
date: 2026-10-05
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

Week 4: 05/10/2026 – 09/10/2026.
<!-- draft: filled in from the usual FCAJ schedule; the project repo has no evidence for this week (first commit 18/10/2026); correct it to match what you actually did before submitting -->

### Week 4 objectives

* Form the team and choose the project topic.
* Learn the AWS AI services for the AI work: Bedrock, Guardrails, Textract.
* Choose how to deploy and which chat channel the bot uses.

| Day | Task | Start date | Completion date | Reference |
| --- | --- | --- | --- | --- |
| 2 | - Formed a team of 4 (2 BE, 2 AI) and brainstormed topics <br> - Chose a bot that helps older people ask before acting on a suspicious message; I took the AI1 role | 05/10/2026 | 05/10/2026 | [research-concept.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/research-concept.md) |
| 3 | - Looked into message scams in Vietnam and what older users need; collected notes for the research concept | 06/10/2026 | 06/10/2026 | [research-concept.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/research-concept.md) |
| 4 | - Learned Amazon Bedrock: model catalog, Converse API, on-demand token pricing | 07/10/2026 | 07/10/2026 | [Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/what-is-bedrock.html), [Converse API](https://docs.aws.amazon.com/bedrock/latest/userguide/conversation-inference.html) |
| 5 | - Learned Bedrock Guardrails for blocking prompt injection <br> - Compared Amazon Textract with local OCR for Vietnamese screenshots | 08/10/2026 | 08/10/2026 | [Bedrock Guardrails](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails.html), [Amazon Textract](https://docs.aws.amazon.com/textract/latest/dg/what-is.html) |
| 6 | - Read the Telegram Bot API (webhooks, receiving images) and AWS SAM to choose the chat channel and deployment | 09/10/2026 | 09/10/2026 | [Telegram Bot API](https://core.telegram.org/bots/api), [AWS SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) |

### Achievements

* Settled on Ask First (Hỏi Trước) and the AI1 role.
* List of AWS services to use: Lambda, SQS, DynamoDB, S3, Bedrock, Guardrails; Telegram as the chat channel.

### Lessons learned

* Picking the topic from a concrete user (older people receiving scam messages) made it easier to cut scope than picking from a technology.
