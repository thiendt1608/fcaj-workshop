---
title: "Proposal"
date: 2026-11-25
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Ask First ("Hỏi Trước")
## A serverless Telegram assistant that helps elderly people check suspected scam messages before they act

### 1. Overview

**Ask First** is a Telegram bot for elderly people in Vietnam. When they receive a suspicious message, link or screenshot, they forward it to the bot. The bot answers in plain Vietnamese with:

- one of **4 risk levels**: `DANGEROUS`, `SUSPICIOUS`, `UNSURE`, `LOW_RISK` (there is deliberately no "safe" level),
- the scam scenario it resembles, the warning signs quoted from the message itself, what to do right now, and a verified official hotline,
- an optional **alert to a family member** who was linked with the elderly person's explicit consent, when the risk is high.

The bot plays the role of the "third person" who steps in before the money leaves the account. It runs fully serverless on AWS: Java 21 Lambdas handle Telegram and orchestration, a Python 3.12 Lambda runs the AI pipeline (local OCR, rules, local RAG), and only the final "judge" step calls an open-weight LLM on Amazon Bedrock with **text that has already been masked**.

| Item | Value |
|---|---|
| Team | 4 people: 2 backend (Java 21), 2 AI (Python 3.12) |
| Timeframe | 6 weeks, 19/10/2026 – 29/11/2026 (FCAJ bootcamp) |
| Regions | Compute in `ap-southeast-1` (Singapore); Bedrock in `ap-southeast-2` (Sydney) |
| Channel | Telegram Bot API (webhook) |
| Estimated cost | ≈ 6.2 USD/month for 3,000 checks without Free Tier, ≈ 2.4 USD/month with always-free tiers (section 6) |

### 2. Problem statement

**Online scams are large-scale in Vietnam, and elderly people are a main target.**

- The Ministry of Public Security estimated losses from online fraud in 2025 at more than 8,000 billion VND ([VOV](https://vov.vn/phap-luat/thiet-hai-tu-lua-dao-truc-tuyen-len-toi-8000-ty-dong-trong-nam-2025-post1286479.vov)).
- In the 2025 survey of the National Cybersecurity Association (NCA), impersonating the police was the most common scam type, and only 32.12% of victims reported to the authorities ([Báo Hải Phòng](https://baohaiphong.vn/cac-hinh-thuc-lua-dao-truc-tuyen-nao-pho-bien-nhat-nam-2025-532711.html), [Công Lý](https://doanhnhan.congly.vn/lua-dao-truc-tuyen-nam-2025-gay-thiet-hai-tren-6-000-ty-dong.html)).
- Thai Nguyen police reported that victims of the "fake police officer" scenario are "mainly elderly people and people in remote areas" ([Bộ Công an](https://www.bocongan.gov.vn/bai-viet/kip-thoi-ngan-chan-vu-viec-gia-danh-luc-luong-cong-an-lua-dao-chiem-doat-tai-san-1782987242)).

**The gap.** Banks already warn at transfer time, but the decisive manipulation happens earlier: scammers isolate the victim ("do not talk to anyone"). In the cases where a scam was stopped, a third person (bank teller, post office staff, local police) stepped in before the transfer. Elderly people have no simple tool to **forward a screenshot or link and get an explanation they understand**, and their children are often not there.

**Target users**

| User | Need |
|---|---|
| Elderly person (primary) | Forward / screenshot / paste a suspicious message, get a short, clear answer within seconds, no app to install |
| Family member (secondary) | Be alerted when a linked elderly relative receives a high-risk message, with one "I have contacted them" button |

### 3. Goals and scope

**Success criteria** (set before building; source: architecture doc §1.3, plan §4):

| Criterion | Target |
|---|---|
| Scam recall | ≥ 90% |
| False positives on "benign but suspicious-looking" messages | ≤ 10% |
| End-to-end latency (warm) | p95 ≤ 15 s for text and images |
| Cost | ≤ 0.005 USD per check; ≤ 50 USD/month at demo scale |
| Privacy | Images stay in the `ai` Lambda's memory; the LLM only sees masked text; logs contain no message content; all user data has a TTL |

**In scope (MVP, 6 weeks)**

- Text, links (including links hidden behind text), screenshots and albums, forwarded messages; several messages sent in a row are grouped into one answer.
- 4-level verdict with scenario, signs, actions, official hotline and a citation from official warnings (RAG).
- Onboarding with explicit consent, withdraw consent, delete data.
- Family linking (double opt-in), family alerts, 15-minute reminder, "I have contacted them" acknowledgement.
- In-chat management: `/nguoithan`, `/lichsu`, `/rutdongy`, `/xoadulieu`, `/caidat`; "first 30 minutes" checklist after being scammed (`/bilua`).
- "Bot was wrong" feedback button; kill switch to fall back to rules-only without redeploying; unsupported input handling (video, sticker, `.apk`...).

**Cut from the original plan to fit 6 weeks** (plan v3, 19/10/2026 – the original plan was 12 weeks):

| Cut / postponed | Why |
|---|---|
| Voice notes and local speech recognition (ASR) | Time; the bot replies "voice is not supported yet" |
| VLM fallback for hard images | Time; local OCR passed its gate (section 4) |
| Multi-turn follow-up questions | Time |
| Telegram Mini App (web UI) | Everything is done in chat with commands and inline buttons instead |
| Phase 2: live knowledge base, "trending scams" page | Out of the 6-week window |
| Pilot with 10–20 households | Replaced by internal testing with the team and their families; **no real-user metrics outside the team** |

**Out of scope by design:** blocking calls or transfers, a public blacklist of phone numbers or accounts, real-time deepfake video detection, sending SMS, recording calls.

### 4. Solution architecture

![Ask First architecture on AWS (MVP)](/images/5-Workshop/architecture.png)

**Design principles**

1. **Serverless, event-driven, no VPC and no NAT** – small, uneven load; pay per request; avoids a fixed NAT cost of about 32 USD/month.
2. **Hybrid local-first AI** – OCR, PII masking, rules and embeddings run on CPU inside Lambda. Only the judge step calls Bedrock, and only with masked text.
3. **Controlled workflow, not an agent** – `Normalize → Extract → Mask PII → Signals (rules + URL) → Retrieve (RAG) → Guard → Judge → Fuse → Respond`. The rules layer works without the LLM, so the bot always answers.
4. **Store no original content** – only verdict, signal codes and hashes, with a 30-day TTL.

**AWS services and why each was chosen**

| Service | Role | Why this service |
|---|---|---|
| **AWS Lambda** – `webhook`, `worker`, `worker-dlq` (Java 21, zip) | Receive Telegram updates via **Function URL**, orchestrate checks, send replies and family alerts | Pay per invocation; Function URL gives an HTTPS endpoint without API Gateway |
| **AWS Lambda** – `ai` (Python 3.12, container image, 3008 MB) | Whole AI pipeline with local models (OCR, embeddings) | Container image fits ML libraries beyond the 250 MB zip limit; images are processed in memory only |
| **Amazon SQS** + DLQ | Queue between `webhook` and `worker` | Webhook returns 200 immediately; caps concurrency to protect Bedrock quota; free retries; `DelaySeconds` implements message grouping (3 s) and the 15-minute reminder without a scheduler |
| **Amazon DynamoDB** (on-demand, single table) | Users, consent, family links, check metadata, dedup, cache, rate limit, feature flags | Key-value access, **TTL** for data minimisation, conditional writes for idempotency, PITR enabled |
| **Amazon Bedrock** – Qwen3 32B (judge), gpt-oss-120b (backup) | Structured classification of masked text | Serverless, pay per token, no GPU to run. Open-weight models are available in-region in Sydney, not in Singapore |
| **Amazon Bedrock Guardrails** | `PROMPT_ATTACK` filter on input | Detects prompt injection inside the forwarded message |
| **Amazon S3** | Local model weights, RAG index, eval sets, voluntarily donated samples (`donated/` expires after 30 days) | Cheap; swap models without rebuilding the image; Block Public Access + SSE |
| **Amazon ECR** | Stores the `ai` container image | Required for image-based Lambda |
| **SSM Parameter Store** (SecureString) | Bot token, webhook secret, stack config | No secrets in code or Lambda environment variables |
| **CloudWatch** + **SNS** | Logs (JSON, shared `trace_id`), DLQ alarm → email to the team | Alarm needs a target; SNS is used only for internal alarms |
| **IAM** + GitHub Actions **OIDC** | Least-privilege roles (the `ai` role may only touch `CACHE#` / `URL#` keys); CI/CD without stored access keys | Security by default |

**Deliberately not used:** API Gateway (Function URL is enough), Step Functions (linear pipeline under 30 s), OpenSearch / vector DB (small corpus, cosine in memory, RAG p95 45 ms on Lambda), EC2/SageMaker GPU (far more expensive than per-token Bedrock at this scale), Amazon Transcribe (voice postponed), Textract, Bedrock Agents, VPC/NAT.

**Main data flow** (numbers match the diagram)

1. The elderly person forwards a message; Telegram POSTs the update to the `webhook` Function URL with a `secret_token` (checked in constant time).
2. `webhook` deduplicates the `update_id`, checks consent, buffers the message (`BUFFER#`), sends "⏳ checking…", and puts a job on SQS with a 3 s delay. It answers Telegram in under 1 s.
3. `worker` reads the buffer and the kill-switch flags, then invokes `ai` synchronously with `op=analyze` (metadata and `file_id` only, never image bytes).
4. `ai` downloads the image straight into memory, runs OCR and local models, masks phone numbers, account numbers, ID numbers, OTPs and emails, applies rules and URL checks, and retrieves scenario cards (RAG).
5. `ai` calls Bedrock Guardrails, then the judge (Qwen3 32B; gpt-oss-120b on low confidence or error), fuses rule and LLM scores into a level, and renders the Vietnamese answer from a template.
6. If the level is `DANGEROUS` with a risky action, `worker` alerts all linked family members in parallel; the "already alerted" state is recorded only after Telegram returns a `message_id`.
7. `worker` edits the "⏳" message into the final answer and writes `CHECK#` metadata (TTL 30 days); a 15-minute reminder is scheduled if no family member acknowledged.
8. A job that fails 5 times goes to the DLQ → CloudWatch alarm → SNS email.

{{% notice note %}}
Configuration details of the deployed `dev` stack (`infra/template.yaml`): SQS maximum concurrency is **5** for `worker` and **2** for `worker-dlq`; SnapStart is a parameter that is **off by default** until cold starts are measured (ADR-008); the OCR model is RapidOCR PP-OCRv6 small. VietOCR was enabled next to RapidOCR on 24/11/2026.
{{% /notice %}}

### 5. Plan and timeline

Six one-week sprints, three parallel tracks (backend, AI, shared) joined by a JSON Schema contract between Java and Python.

| Week | Milestone / gate | Content | Status (from repo docs) |
|---|---|---|---|
| W1 (19–25/10) | **G0** Contract | BE↔AI JSON Schema, fixtures, contract tests in CI | Done <!-- TODO(not verified): exact merge date of G0 --> |
| W2 (26/10–01/11) | **G1** LLM choice · **G2** OCR choice | Benchmark judge models; compare OCR stacks on 50 real screenshots | G2 passed 30/10 (RapidOCR, URL 94%). G1 closed 11/11, later than planned (Qwen3 32B, ADR-002) |
| W3 (02–08/11) | **M1** Text end-to-end | Forwarded text → real verdict (rules + Qwen3) | <!-- TODO(not verified): M1 completion date --> |
| W4 (09–15/11) | **M2** Images + family | Screenshot → local OCR → verdict; family alert | Image path measured on Lambda 25/11 (see section 7) |
| W5 (16–22/11) | **M3** RAG · **M4** MVP freeze | Citations from official warnings; eval gate | M4 eval report 17/11; RAG p95 45 ms on Lambda |
| W6 (23–29/11) | **M5** internal test · **M6** presentation · **M7** workshop submission | Team and family testing, cost estimate, final eval, clean-up guide | Final eval 24/11, cost estimate 24/11 |

After M4, only bug fixes, prompt/threshold tuning (through the eval gate) and infrastructure work were allowed.

### 6. Budget estimation

Load assumption: **3,000 checks/month**, text and images, no voice. Non-Bedrock services were priced with the **[AWS Pricing Calculator](https://calculator.aws/#/estimate?id=073f413f6ac370de48bf3bff0ba10a7b870ae807)** (created 24/11/2026, `ap-southeast-1`, "Without Free Tier"). Bedrock was calculated by hand because the Calculator does not offer Qwen3 in Sydney; token counts were measured on the real pipeline.

| Service | Basis | Without Free Tier (USD/month) | With always-free tiers (USD/month) |
|---|---|---|---|
| Bedrock LLM (Qwen3 32B + gpt-oss-120b backup) | 2,250 LLM calls × 0.000792 USD (measured, includes escalation) | 1.78 | 1.78 |
| Bedrock Guardrails | 2,250 × 0.15 USD / 1,000 text units | 0.34 | 0.34 |
| Lambda `ai` (3008 MB) | Calculator | 0.73 | 0 |
| Lambda `worker` (512 MB) | Calculator | 0.13 | 0 |
| Lambda `webhook` (512 MB) | Calculator | 0.03 | 0 |
| SQS (jobs + DLQ) | Calculator | 0.28 | 0 |
| DynamoDB on-demand | Calculator | 0.29 | < 0.05 |
| S3 | Calculator | 0.03 | 0.03 |
| ECR (`ai` image) | Calculator | 0.20 | 0.20 |
| CloudWatch (logs, metrics, alarms, dashboard) | Calculator | 2.35 | 0 |
| SNS (alarm email), SSM Parameter Store (standard) | Not entered | ≈ 0 | 0 |
| **Total** | | **≈ 6.16** | **≈ 2.4** |

- Calculator total without Bedrock: 4.04 USD/month (48.48 USD per 12 months, 0 USD upfront).
- Per check, LLM tokens only: ≈ 0.000792 USD (≈ 20 VND), well under the 0.005 USD target.
- The 2,250 LLM calls assume 25% of checks are answered by the rules fast path or cache. This ratio is **not verified**: the eval set is almost all scams and hard cases. If every check calls the LLM, Bedrock is ≈ 2.7 USD/month.

<!-- TODO(not verified): whether the team AWS account still qualifies for the always-free tiers (check Billing > Free Tier) -->
<!-- TODO(not verified): Lambda durations for worker/webhook are assumptions; ai duration is from dev-machine measurements rounded up -->
<!-- TODO(not verified): which Guardrails price line AWS actually bills (0.15 vs 0.08 USD per 1,000 units); 0.15 used to be safe -->
<!-- TODO(not verified): actual spend from Cost Explorer after the internal test week -->

**Cost optimisation**

1. **No VPC/NAT, no API Gateway, no OpenSearch, no GPU** – avoids every fixed monthly cost; the bill scales with use.
2. **Local-first AI** – OCR, embeddings and rules run inside Lambda at no per-token cost; only the judge calls Bedrock with a cheap open-weight model.
3. **Fast path and cache** – each check that skips the LLM saves ≈ 0.00094 USD (tokens + Guardrails).
4. **Kill switch** (`CONFIG#global/FLAGS`) – turns off the LLM judge or image intake without a redeploy if a cost alarm fires or the bot is spammed; per-user rate limit caps abuse.
5. **Free Tier** – Lambda, SQS and CloudWatch fit in the always-free tiers at this load (6.2 → 2.4 USD).
6. **Retention and lifecycle** – S3 `donated/` already expires after 30 days. Recommended next: log retention of 7 days (the template does not set `RetentionInDays` yet) and an ECR lifecycle policy keeping the 2 latest images.

### 7. Risks and mitigations

| Risk | Impact | Mitigation | Current status |
|---|---|---|---|
| Missed scam (false negative) | Elderly person loses money | Rules floor independent of the LLM; no "safe" level; recall gate ≥ 90% before any prompt/model change | Final eval: recall 100% on 90 scam samples (3 runs) |
| False alarm on legitimate messages | Users stop trusting the bot | Calibrated fuse thresholds (70/60/25, ADR-005); false-positive gate ≤ 10% | 5% (1 of 20) on benign-but-suspicious samples |
| Small, synthetic eval set | Metrics may be optimistic | Report limits openly; collect anonymised donated samples with consent | 134 labelled samples, **0 real anonymised samples**; target was 300 |
| LLM error, throttling or quota | No answer | Backup model, then rules-only mode; the bot always replies | Implemented in the cascade |
| Prompt injection inside forwarded text | Manipulated verdict | Bedrock Guardrails `PROMPT_ATTACK`; detected injection raises the minimum level to `UNSURE`; LLM output is rendered through templates only | Implemented |
| Latency and cold start | Answer later than the 15 s target | Grouping on SQS; models preloaded during Lambda INIT; warm-up before the demo | Text p95 3.5 s (warm). **Image end-to-end p95 17.0 s, above 15 s**; accepted up to ~30 s by the AI lead. `ai` cold-start init p50 6.2 s |
| Cost spike from spam | Budget overrun | Per-user rate limit, kill switch, SQS concurrency cap, DLQ alarm | <!-- TODO(not verified): AWS Budgets alerts (10/25/50 USD) shown in the diagram are actually created in the account --> |
| Personal data and consent | Legal non-compliance | Explicit, recorded consent; withdraw and delete commands; PII masked before any external call; no original content stored; 30-day TTL. Designed against Law 91/2025/QH15 on Personal Data Protection (National Assembly) and Decree 356/2025/NĐ-CP (Government) – a design mapping, not legal advice | <!-- TODO(not verified): add official vbpl.vn / chinhphu.vn links for Law 91/2025/QH15 and Decree 356/2025/NĐ-CP --> |
| Bedrock model availability by region | Judge unavailable | Sydney has the models in-region; fallback to Jakarta (`ap-southeast-3`) or switching model only changes `ai/config/models.yaml` | Not needed so far |
| Team capacity (exams, 4 people) | Missed milestones | Pairing, early scope cut (voice, VLM, multi-turn, Mini App), predefined cut order if late | Scope cut applied in v3 of the plan |
