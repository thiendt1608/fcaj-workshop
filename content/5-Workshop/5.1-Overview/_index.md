---
title: "Overview"
date: 2026-11-25
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### The problem

Elderly people in Vietnam receive a steady stream of scam messages: fake traffic fines, fake bank or public-service notices, "your child is in hospital, transfer money now", phishing links. Many of them do not have someone nearby to ask at the moment the message arrives, and by the time they ask, the money is gone.

#### The solution

**Ask First** puts a "third person" between the message and the money. The user forwards the suspicious text, link or screenshot to a Telegram bot. The bot:

1. Replies with "⏳" right away, so the user knows it is working.
2. Reads the content (OCR for screenshots), masks personal numbers, runs fixed rules and a small knowledge base of known scam scenarios, and only then asks an LLM to judge.
3. Edits the "⏳" message into a short answer in plain Vietnamese: a risk level, the scam pattern it matches, why, and what to do now.
4. If the risk is high and the user has linked a relative (double opt-in), sends that relative an alert.

The bot does **not** store the original message: only the verdict, signal codes and hashes, with a TTL.

#### Architecture

![Ask First architecture on AWS](/images/5-Workshop/architecture.png)

The diagram follows one check from start to end. The numbers below match the numbered arrows.

| Step | What happens |
|---|---|
| 1–2 | The elderly user sends a message. Telegram POSTs the update to the **`webhook` Lambda Function URL** with the header `X-Telegram-Bot-Api-Secret-Token`. The function rejects any request without the right secret (HTTP 401). |
| 3 | `webhook` de-duplicates the `update_id`, checks that the user has given consent, and appends the message to a per-chat buffer (`BUFFER#`) in **DynamoDB**, so several messages sent in a row are checked together. |
| 4 | `webhook` sends "⏳" to the user, puts a job on the **SQS `jobs` queue** with a 3-second delay, and returns HTTP 200 to Telegram in under a second. |
| 5–6 | The **`worker` Lambda** (Java) polls the queue (maximum concurrency 5), reads the buffer and the kill-switch flags (`CONFIG#global/FLAGS`), then invokes the **`ai` Lambda** synchronously with `op=analyze`. Only metadata and Telegram `file_id`s are sent, never image bytes. |
| 7 | `ai` downloads the image from Telegram straight into memory, using the bot token it reads from **SSM Parameter Store**. |
| 8 | On a cold start, `ai` lazily downloads model weights (OCR, embedder) and the knowledge-base index from **S3** into `/tmp` and checks their SHA-256. |
| 9 | `ai` reads and writes only cache items (`CACHE#`, `URL#`) in DynamoDB. The same content checked twice in 24 hours is answered from cache. |
| 10–12 | The pipeline runs Normalize → Extract (OCR) → Mask PII → Signals (rules + URL intel) → Retrieve (RAG) → Guard → Judge → Fuse → Respond. **Bedrock Guardrails** checks for prompt attacks, then **Bedrock** (Qwen3 32B in Sydney, escalating to gpt-oss-120b when unsure) judges the **masked** text. If the rules are already certain, the LLM is skipped. |
| 13–15 | `worker` edits the "⏳" message into the result. If the verdict is dangerous and a relative is linked, it sends the relative an alert with an "I have contacted them" button. |
| 16–17 | `worker` stores a `CHECK#` record (metadata only, TTL 30 days) and may schedule a 15-minute follow-up job on SQS. A job that fails 5 times moves to the **DLQ**; a small `worker-dlq` Lambda tells the user the bot is busy, and a **CloudWatch alarm** fires. |

{{% notice note %}}
The diagram shows the team's running `dev` stack (checked on 25/11/2026): 4 Lambda functions, Bedrock in a separate account that `ai` assumes a role into, and the DLQ alarm emailing through SNS. When you deploy it yourself with this workshop: SnapStart is **off** by default (`WorkerSnapStart=None`), VietOCR is optional, the SNS topic for the alarm is optional (`AlarmTopicArn`, section 5.7.5), Bedrock is called in your own account unless you set `BEDROCK_ROLE_ARN`, and GitHub Actions only builds and pushes the `ai` image through OIDC; `sam deploy` is run by hand.
{{% /notice %}}

#### Services used and why

| Service | Role in Ask First | Why this choice |
|---|---|---|
| **AWS Lambda** | `webhook`, `worker`, `worker-dlq` (Java 21 zip) and `ai` (Python container image) | Traffic is small and bursty, so pay per request. A Function URL gives an HTTPS endpoint without API Gateway. A container image lets `ai` ship ML libraries that exceed the 250 MB zip limit. |
| **Amazon SQS + DLQ** | Queue between `webhook` and `worker` | `webhook` can answer Telegram immediately; retries (max 5) and a DLQ come for free; `DelaySeconds` implements the 3-second debounce and the 15-minute follow-up without a scheduler. |
| **Amazon DynamoDB** | Single table `askfirst-<stage>`: users, consent, buffers, checks, cache, flags | On-demand billing, TTL deletes data automatically (data minimisation), conditional writes for idempotency, PITR enabled. |
| **Amazon S3** | Model weights, knowledge-base index, donated samples (`donated/`, expires after 30 days) | Cheap storage; models can change without rebuilding the image. Block Public Access and SSE are on. |
| **Amazon Bedrock** | LLM judge (Qwen3 32B, backup gpt-oss-120b) in `ap-southeast-2` | Serverless, pay per token, no GPU to run. Open-weight models were available in Sydney but not in Singapore when the team chose them. |
| **Bedrock Guardrails** | Detects prompt attacks in the input | The message content is untrusted by nature. |
| **Amazon ECR** | Stores the `ai` image (`askfirst-ai`, keeps the 5 newest images) | Required for Lambda container images. |
| **SSM Parameter Store** | Bot token and webhook secret (`SecureString`), plus stack config | Secrets stay out of code and out of Lambda configuration; the standard tier has no extra cost. |
| **Amazon CloudWatch** | Logs of every function, alarm `askfirst-<stage>-jobs-dlq-not-empty` | Built in, no extra agent. |
| **AWS SAM / CloudFormation** | Whole stack as code (`infra/template.yaml`) | One command builds and deploys; the same template creates `dev` or `prod`. |
| **IAM + GitHub OIDC** | Least-privilege roles; GitHub Actions pushes the image without long-lived keys | No access keys stored in GitHub. |

Services deliberately **not** used: API Gateway, Step Functions, EventBridge, OpenSearch, VPC/NAT Gateway (a NAT Gateway alone costs about 32 USD/month), and self-hosted GPUs on EC2/SageMaker (source: `docs/system-architecture.md` §3.2 in the project repository).

#### Mapping to the AWS Well-Architected pillars

| Pillar | How Ask First addresses it |
|---|---|
| **Operational excellence** | Infrastructure as code with SAM; CI on GitHub Actions (Maven verify, pytest, contract tests between Java and Python); JSON logs share a `trace_id` across `worker` and `ai`; DLQ alarm. |
| **Security** | Function URL accepts only requests with the Telegram secret header; secrets in SSM `SecureString`, decryptable only through SSM (`kms:ViaService`); least-privilege IAM (for example `ai` may only touch DynamoDB keys starting with `CACHE#` or `URL#`, and only some S3 prefixes); S3 Block Public Access and encryption; personal numbers are masked before any text leaves for Bedrock; OIDC instead of access keys in CI. |
| **Reliability** | SQS retries with `ReportBatchItemFailures`, DLQ after 5 failures, `update_id` de-duplication, degraded "rules only" mode when the LLM fails, DynamoDB PITR. |
| **Performance efficiency** | Three functions sized for their job (fast `webhook`, `worker` up to 120 s, `ai` with 3008 MB and 90 s); small CPU models loaded lazily and cached in memory; verdict cache for repeated content. |
| **Cost optimization** | Pure pay-per-use, no VPC/NAT, on-demand DynamoDB, low-cost open-weight models, the LLM is skipped when rules are certain. Measured Bedrock cost was about 0.00076 USD per text check (source: `docs/results/lambda-p95.md`, 100 checks on 2026-11-24). |
| **Sustainability** | No idle servers: every component scales to zero when nobody sends messages. |
