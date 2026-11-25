---
title: "Chuẩn bị"
date: 2026-11-25
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

Chuẩn bị đủ mọi thứ trong trang này trước khi sang 5.3. Lệnh được viết cho macOS/Linux (hoặc Git Bash trên Windows). Bản PowerShell của các lệnh SSM có trong `infra/README.md` của repo dự án.

#### 1. Tài khoản AWS và Region

Bạn cần một tài khoản AWS có quyền tạo IAM role. Nên dùng **tài khoản sandbox hoặc dev**, không dùng production.

| Region | Dùng cho |
|---|---|
| `ap-southeast-1` (Singapore) | Mọi tài nguyên bạn deploy: Lambda, SQS, DynamoDB, S3, SSM, ECR, CloudWatch. Đã cố định trong `infra/samconfig.toml` và `.github/workflows/ai-image.yml`. |
| `ap-southeast-2` (Sydney) | Chỉ model Amazon Bedrock và Guardrail (`bedrock_region` trong `ai/config/models.yaml`). SAM không deploy gì ở đây. |

{{% notice tip %}}
Tạo **AWS Budgets** trước khi bắt đầu. Team đặt cảnh báo ở các mức 10, 25 và 50 USD (Billing and Cost Management → Budgets → Create budget).
{{% /notice %}}

<!-- TODO screenshot: trang AWS Budgets có budget chi phí hằng tháng với các ngưỡng cảnh báo 10/25/50 USD -->

#### 2. Model Amazon Bedrock ở `ap-southeast-2`

Function `ai` gọi hai model ở Sydney:

| Mục đích | Model ID |
|---|---|
| Judge | `qwen.qwen3-32b-v1:0` |
| Dự phòng (leo thang) | `openai.gpt-oss-120b-1:0` |

Mở Amazon Bedrock console, đổi Region sang **Asia Pacific (Sydney)** và đảm bảo tài khoản dùng được cả hai model. Nếu console của bạn vẫn còn trang **Model access**, hãy request access cho cả hai model ở đó.

<!-- TODO screenshot: Bedrock console ở ap-southeast-2 cho thấy Qwen3 32B và gpt-oss-120b đã dùng được/đã được cấp quyền -->

Sau khi cấu hình credentials (bước 5), có thể kiểm tra bằng CLI:

```bash
aws bedrock-runtime converse --region ap-southeast-2 \
  --model-id qwen.qwen3-32b-v1:0 \
  --messages '[{"role":"user","content":[{"text":"Xin chao"}]}]'
```

Nhận về JSON có trường `output.message` nghĩa là gọi được model. Lỗi `AccessDeniedException` nghĩa là tài khoản chưa được cấp quyền.

#### 3. Quyền IAM cho người deploy

Repo dự án chưa có sẵn một policy deploy hoàn chỉnh. `infra/README.md` (§8.2) liệt kê các nhóm quyền tối thiểu cho stack SAM:

- **CloudFormation:** `cloudformation:*` trên stack `askfirst-dev`.
- **Lambda:** `lambda:CreateFunction`, `lambda:DeleteFunction`, `lambda:UpdateFunction*`, `lambda:AddPermission`, `lambda:CreateFunctionUrlConfig`, `lambda:GetFunctionUrlConfig`.
- **IAM:** `iam:CreateRole`, `iam:DeleteRole`, `iam:GetRole`, `iam:AttachRolePolicy`, `iam:DetachRolePolicy`, `iam:PutRolePolicy`, `iam:DeleteRolePolicy`, `iam:PassRole` (trên các role SAM tạo cho function).
- **DynamoDB:** `dynamodb:CreateTable`, `dynamodb:DeleteTable`, `dynamodb:DescribeTable`, `dynamodb:UpdateTimeToLive`, `dynamodb:UpdateContinuousBackups`.
- **SQS:** `sqs:CreateQueue`, `sqs:DeleteQueue`, `sqs:GetQueueAttributes`, `sqs:SetQueueAttributes`.
- **S3:** `s3:CreateBucket`, `s3:DeleteBucket`, `s3:PutBucketPublicAccessBlock`, `s3:PutEncryptionConfiguration`, `s3:PutLifecycleConfiguration`, `s3:PutObject`, `s3:DeleteObject`.
- **SSM:** `ssm:PutParameter`, `ssm:DeleteParameter`, `ssm:GetParameter*`, và `kms:Decrypt` (để đọc `SecureString` với `--with-decryption`).

Template hiện tại và các bước AI còn cần thêm những quyền dưới đây. Danh sách này suy ra từ `infra/template.yaml`, `infra/github-oidc.yaml` và `ai/scripts/create_guardrail.py`, không phải policy team đã chạy thử:

- **Lambda (version, alias, trigger SQS):** `lambda:PublishVersion`, `lambda:CreateAlias`, `lambda:UpdateAlias`, `lambda:DeleteAlias`, `lambda:CreateEventSourceMapping`, `lambda:UpdateEventSourceMapping`, `lambda:DeleteEventSourceMapping`, `lambda:GetFunction*`.
- **CloudWatch:** `cloudwatch:PutMetricAlarm`, `cloudwatch:DeleteAlarms`, `cloudwatch:DescribeAlarms`.
- **ECR và OIDC (5.4):** `ecr:CreateRepository`, `ecr:PutLifecyclePolicy`, `ecr:DeleteRepository`, `ecr:DescribeImages`, `ecr:BatchGetImage`, `ecr:GetDownloadUrlForLayer`, `iam:CreateOpenIDConnectProvider`.
- **Bedrock (5.4):** `bedrock:CreateGuardrail`, `bedrock:CreateGuardrailVersion`, `bedrock:ListGuardrails`, `bedrock:InvokeModel`.
- **Bucket artifact của SAM:** `resolve_s3 = true` trong `samconfig.toml` khiến SAM tự tạo một managed stack (`aws-sam-cli-managed-default`) có S3 bucket chứa artifact build ở lần deploy đầu tiên.

<!-- TODO(not verified): team chưa công bố policy JSON least-privilege đã kiểm thử bao trọn các quyền trên; danh sách bổ sung được suy ra từ template và script. -->

{{% notice warning %}}
Không dùng root user. Ưu tiên credentials ngắn hạn (IAM Identity Center / `aws configure sso`) thay vì access key dài hạn. Tuyệt đối không commit credentials vào Git.
{{% /notice %}}

#### 4. Cài đặt công cụ

| Công cụ | Phiên bản dự án dùng | Kiểm tra | Ghi chú |
|---|---|---|---|
| Git | bản mới | `git --version` | |
| Tài khoản GitHub + GitHub CLI `gh` | bản mới | `gh --version` | Cần **fork riêng** của repo để GitHub Actions build AI image vào tài khoản của bạn (5.4). |
| Java JDK | **21** (CI dùng Temurin 21) | `java -version` | Runtime của `webhook` và `worker` là `java21`. |
| Apache Maven | wrapper ghim **3.10.0** | `./mvnw -v` | Repo có sẵn `mvnw`/`mvnw.cmd`. |
| AWS CLI | **v2** | `aws --version` | |
| AWS SAM CLI | **1.100+** | `sam --version` | Windows: `winget install --id Amazon.SAM-CLI --exact` |
| Python | **3.12** (`requires-python = ">=3.12"`) | `python3 --version` | Cho các script Telegram và script AI. |
| uv | bản mới | `uv --version` | Trình quản lý package Python dùng trong `ai/` (CI cài Python 3.12 qua uv). |
| OpenSSL | bản bất kỳ | `openssl version` | Chỉ để sinh webhook secret ngẫu nhiên. |
| Docker | bản mới | `docker --version` | **Tuỳ chọn.** Chỉ cần khi build AI image trên máy (cách B ở 5.4). Cách chính là build trên GitHub Actions. |

<!-- TODO(not verified): `sam build` chạy được chỉ với Maven wrapper hay cần thêm `mvn` trên PATH (Java Maven builder của SAM thường gọi `mvn`). Nếu `sam build` báo không tìm thấy `mvn`, hãy cài Apache Maven rồi chạy lại. -->

#### 5. Cấu hình AWS credentials

```bash
aws configure            # hoặc: aws configure sso
# Default region name: ap-southeast-1
# Default output format: json

aws sts get-caller-identity
```

Lệnh cuối phải in ra đúng tài khoản (`<ACCOUNT_ID>`) và danh tính bạn sẽ dùng để deploy.

#### 6. Fork và clone repo

Fork repo dự án trên GitHub về tài khoản hoặc organisation của bạn, rồi clone bản fork:

```bash
git clone https://github.com/<GITHUB_OWNER>/<REPO>.git ask-first
cd ask-first
```

Mọi lệnh phía sau đều chạy từ thư mục gốc repo này, trừ khi bước đó ghi khác.

#### 7. Tạo Telegram bot bằng BotFather

1. Trong Telegram, mở chat với **@BotFather** (kiểm tra có dấu tích xanh).
2. Gửi `/newbot`.
3. Nhập tên hiển thị, ví dụ `Hoi Truoc Dev`.
4. Nhập username kết thúc bằng `bot`, ví dụ `<BOT_USERNAME>` (phải là duy nhất trên Telegram).
5. BotFather trả về HTTP API token dạng `<BOT_ID>:<SECRET_PART>`. Trong phần còn lại của hướng dẫn, đây là `<TELEGRAM_BOT_TOKEN>`.

<!-- TODO screenshot: chat với BotFather sau lệnh /newbot, token đã được làm mờ -->

{{% notice warning %}}
Bot token cho phép điều khiển toàn bộ bot. Chỉ giữ token trong password manager và SSM Parameter Store (5.5). Không dán token vào chat, ảnh chụp màn hình, Git hay `samconfig.toml`. Nếu lộ, gửi `/revoke` cho BotFather và lưu token mới.
{{% /notice %}}

Chuẩn bị xong thì sang [5.3 Deploy hạ tầng](../5.3-deploy-infrastructure/).
