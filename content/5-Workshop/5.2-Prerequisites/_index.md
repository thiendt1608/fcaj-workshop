---
title: "Prerequisites"
date: 2026-11-25
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

Prepare everything on this page before you start 5.3. Commands are written for macOS/Linux (or Git Bash on Windows). PowerShell versions of the SSM commands are in `infra/README.md` of the project repository.

#### 1. AWS account and Regions

You need an AWS account where you can create IAM roles. Use a **sandbox or dev account**, not production.

| Region | Used for |
|---|---|
| `ap-southeast-1` (Singapore) | Everything you deploy: Lambda, SQS, DynamoDB, S3, SSM, ECR, CloudWatch. Fixed in `infra/samconfig.toml` and `.github/workflows/ai-image.yml`. |
| `ap-southeast-2` (Sydney) | Amazon Bedrock models and the Guardrail only (`bedrock_region` in `ai/config/models.yaml`). Nothing is deployed there by SAM. |

{{% notice tip %}}
Create an **AWS Budgets** cost budget before you start. The team used alerts at 10, 25 and 50 USD (Billing and Cost Management → Budgets → Create budget).
{{% /notice %}}

<!-- TODO screenshot: AWS Budgets page showing the monthly cost budget with the 10/25/50 USD alert thresholds -->

#### 2. Amazon Bedrock models in `ap-southeast-2`

The `ai` function calls two models in Sydney:

| Purpose | Model ID |
|---|---|
| Judge | `qwen.qwen3-32b-v1:0` |
| Backup (escalation) | `openai.gpt-oss-120b-1:0` |

Open the Amazon Bedrock console, switch the Region to **Asia Pacific (Sydney)**, and make sure both models can be used by your account. If your console still shows a **Model access** page, request access to both models there.

<!-- TODO screenshot: Bedrock console in ap-southeast-2 showing Qwen3 32B and gpt-oss-120b as available/access granted -->

You can check access from the CLI once your credentials are configured (step 5):

```bash
aws bedrock-runtime converse --region ap-southeast-2 \
  --model-id qwen.qwen3-32b-v1:0 \
  --messages '[{"role":"user","content":[{"text":"Xin chao"}]}]'
```

A JSON answer with an `output.message` field means the model is reachable. An `AccessDeniedException` means access is not granted yet.

#### 3. IAM permissions for the person who deploys

The project repository does not ship a ready-made deploy policy. `infra/README.md` (§8.2) lists the minimum permission groups for the SAM stack:

- **CloudFormation:** `cloudformation:*` on the stack `askfirst-dev`.
- **Lambda:** `lambda:CreateFunction`, `lambda:DeleteFunction`, `lambda:UpdateFunction*`, `lambda:AddPermission`, `lambda:CreateFunctionUrlConfig`, `lambda:GetFunctionUrlConfig`.
- **IAM:** `iam:CreateRole`, `iam:DeleteRole`, `iam:GetRole`, `iam:AttachRolePolicy`, `iam:DetachRolePolicy`, `iam:PutRolePolicy`, `iam:DeleteRolePolicy`, `iam:PassRole` (on the roles SAM creates for the functions).
- **DynamoDB:** `dynamodb:CreateTable`, `dynamodb:DeleteTable`, `dynamodb:DescribeTable`, `dynamodb:UpdateTimeToLive`, `dynamodb:UpdateContinuousBackups`.
- **SQS:** `sqs:CreateQueue`, `sqs:DeleteQueue`, `sqs:GetQueueAttributes`, `sqs:SetQueueAttributes`.
- **S3:** `s3:CreateBucket`, `s3:DeleteBucket`, `s3:PutBucketPublicAccessBlock`, `s3:PutEncryptionConfiguration`, `s3:PutLifecycleConfiguration`, `s3:PutObject`, `s3:DeleteObject`.
- **SSM:** `ssm:PutParameter`, `ssm:DeleteParameter`, `ssm:GetParameter*`, and `kms:Decrypt` (to read `SecureString` values with `--with-decryption`).

The current template and the AI steps also need the following. These are derived from `infra/template.yaml`, `infra/github-oidc.yaml` and `ai/scripts/create_guardrail.py`, not from a policy the team tested:

- **Lambda (versions, alias, SQS trigger):** `lambda:PublishVersion`, `lambda:CreateAlias`, `lambda:UpdateAlias`, `lambda:DeleteAlias`, `lambda:CreateEventSourceMapping`, `lambda:UpdateEventSourceMapping`, `lambda:DeleteEventSourceMapping`, `lambda:GetFunction*`.
- **CloudWatch:** `cloudwatch:PutMetricAlarm`, `cloudwatch:DeleteAlarms`, `cloudwatch:DescribeAlarms`.
- **ECR and OIDC (5.4):** `ecr:CreateRepository`, `ecr:PutLifecyclePolicy`, `ecr:DeleteRepository`, `ecr:DescribeImages`, `ecr:BatchGetImage`, `ecr:GetDownloadUrlForLayer`, `iam:CreateOpenIDConnectProvider`.
- **Bedrock (5.4):** `bedrock:CreateGuardrail`, `bedrock:CreateGuardrailVersion`, `bedrock:ListGuardrails`, `bedrock:InvokeModel`.
- **SAM artifact bucket:** `resolve_s3 = true` in `samconfig.toml` makes SAM create a managed stack (`aws-sam-cli-managed-default`) with an S3 bucket for build artifacts the first time you deploy.

<!-- TODO(not verified): the team has not published a tested least-privilege JSON policy covering all of the above; the extra list is derived from the templates and scripts. -->

{{% notice warning %}}
Do not use the root user. Prefer short-lived credentials (IAM Identity Center / `aws configure sso`) over long-lived access keys. Never commit credentials to Git.
{{% /notice %}}

#### 4. Tools to install

| Tool | Version used by the project | Check | Notes |
|---|---|---|---|
| Git | any recent | `git --version` | |
| GitHub account + GitHub CLI `gh` | any recent | `gh --version` | You need your **own fork** of the repository so GitHub Actions can build the AI image in your account (5.4). |
| Java JDK | **21** (CI uses Temurin 21) | `java -version` | Runtime of `webhook` and `worker` is `java21`. |
| Apache Maven | wrapper pins **3.10.0** | `./mvnw -v` | The repo ships `mvnw`/`mvnw.cmd`. |
| AWS CLI | **v2** | `aws --version` | |
| AWS SAM CLI | **1.100+** | `sam --version` | Windows: `winget install --id Amazon.SAM-CLI --exact` |
| Python | **3.12** (`requires-python = ">=3.12"`) | `python3 --version` | For the Telegram scripts and the AI scripts. |
| uv | any recent | `uv --version` | Python package manager used in `ai/` (CI installs Python 3.12 through uv). |
| OpenSSL | any | `openssl version` | Only to generate a random webhook secret. |
| Docker | any recent | `docker --version` | **Optional.** Only for building the AI image locally (option B in 5.4). The normal path builds it on GitHub Actions. |

<!-- TODO(not verified): whether `sam build` works with only the Maven wrapper or also needs `mvn` on PATH (SAM's Java Maven builder normally calls `mvn`). If `sam build` fails with "mvn not found", install Apache Maven and retry. -->

#### 5. Configure AWS credentials

```bash
aws configure            # or: aws configure sso
# Default region name: ap-southeast-1
# Default output format: json

aws sts get-caller-identity
```

The last command must print your account (`<ACCOUNT_ID>`) and the identity you will deploy with.

#### 6. Fork and clone the repository

Fork the project repository on GitHub into your own account or organisation, then clone your fork:

```bash
git clone https://github.com/<GITHUB_OWNER>/<REPO>.git ask-first
cd ask-first
```

All later commands start from this repository root unless a step says otherwise.

#### 7. Create the Telegram bot with BotFather

1. In Telegram, open a chat with **@BotFather** (check the blue verification tick).
2. Send `/newbot`.
3. Enter a display name, for example `Hoi Truoc Dev`.
4. Enter a username that ends with `bot`, for example `<BOT_USERNAME>` (it must be unique on Telegram).
5. BotFather replies with an HTTP API token of the form `<BOT_ID>:<SECRET_PART>`. This is `<TELEGRAM_BOT_TOKEN>` in the rest of this guide.

<!-- TODO screenshot: BotFather chat after /newbot with the token blurred out -->

{{% notice warning %}}
The bot token gives full control of the bot. Keep it only in a password manager and in SSM Parameter Store (5.5). Do not paste it into chats, screenshots, Git or `samconfig.toml`. If it leaks, send `/revoke` to BotFather and store the new token.
{{% /notice %}}

When everything above is ready, continue with [5.3 Deploy the infrastructure](../5.3-deploy-infrastructure/).
