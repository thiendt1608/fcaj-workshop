---
title: "Dọn dẹp tài nguyên"
date: 2026-11-25
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

Làm xong workshop thì xoá hết tài nguyên theo đúng thứ tự dưới đây để không bị tính tiền tiếp. Bước 1–6 theo hướng dẫn dọn dẹp trong repo dự án (`infra/README.md`, mục 7, viết lại trong pull request #186 của task SH-10). Bước 7 và các mục bổ sung ở bước 5, 6 dọn những tài nguyên tạo ở các phần khác của workshop này.

<!-- TODO(not verified): PR #186 vẫn đang mở. Các lệnh chỉ đọc đã chạy thử trên stack dev, nhưng chưa chạy trọn bộ trên một stack tạm. -->

Lệnh viết cho stage `dev`, Region `ap-southeast-1`, chạy ở gốc repo bằng Bash (Windows dùng Git Bash hoặc WSL). Đổi `STAGE` nếu bạn deploy stage khác.

```bash
STAGE=dev
export AWS_REGION=ap-southeast-1
```

{{% notice warning %}}
**Bước 3, 5 và 6 không hoàn tác được.** Bước 3 xoá mọi file trong bucket (model và ảnh người dùng hiến tặng). Bước 5 xoá bảng DynamoDB `askfirst-$STAGE` cùng toàn bộ dữ liệu người dùng, bản backup PITR cũng mất theo. Bước 6 xoá parameter chứa token bot và toàn bộ log. Cần giữ gì thì sao lưu trước.
{{% /notice %}}

#### Bước 1: Gỡ webhook Telegram

Để Telegram ngừng gửi update vào Function URL trước khi xoá function:

```bash
python scripts/set_webhook.py --delete   # đọc TELEGRAM_BOT_TOKEN từ biến môi trường hoặc .env
```

Kiểm tra: script in ra kết quả `deleteWebhook` thành công, và `getWebhookInfo` của bot trả về `url` rỗng.

#### Bước 2: Tắt provisioned concurrency (chỉ khi đã bật)

Template **không** bật provisioned concurrency. Nếu bạn tự bật thì tắt:

```bash
for f in webhook worker ai; do
  aws lambda list-provisioned-concurrency-configs --function-name askfirst-$STAGE-$f \
    --query 'ProvisionedConcurrencyConfigs[].FunctionArn' --output text 2>/dev/null
done
# Có dòng nào in ra thì xoá cấu hình trên alias đó, ví dụ:
aws lambda delete-provisioned-concurrency-config --function-name askfirst-$STAGE-ai --qualifier live
```

Kiểm tra: chạy lại vòng `for` ở trên không in ra gì.

#### Bước 3: Làm rỗng S3 bucket

CloudFormation không xoá được bucket còn object. Bucket của stack không bật versioning, nhưng nếu bạn tự bật thì phải xoá cả các version cũ:

```bash
BUCKET=$(aws ssm get-parameter --name "/askfirst/$STAGE/s3/bucket_name" --query Parameter.Value --output text)
aws s3 rm "s3://$BUCKET" --recursive
# Chỉ khi đã bật versioning: xoá mọi version và delete marker
aws s3api delete-objects --bucket "$BUCKET" --delete "$(aws s3api list-object-versions --bucket "$BUCKET" \
  --query '{Objects: [Versions, DeleteMarkers][][].{Key: Key, VersionId: VersionId}}' --output json)"
```

Kiểm tra: `aws s3api list-object-versions --bucket "$BUCKET" --max-items 1` không còn `Versions` hay `DeleteMarkers`. Bản thân bucket bị xoá ở bước 5.

#### Bước 4: Xoá image trong ECR

Repo `askfirst-ai` thuộc stack `askfirst-github-oidc` và đặt `EmptyOnDelete: true`, nên xoá stack là xoá luôn image:

```bash
aws cloudformation delete-stack --stack-name askfirst-github-oidc
aws cloudformation wait stack-delete-complete --stack-name askfirst-github-oidc
```

Kiểm tra: `aws ecr describe-repositories --repository-names askfirst-ai` báo `RepositoryNotFoundException`. Nếu tài khoản có stack khác dùng chung GitHub OIDC provider thì xem kỹ trước khi xoá stack này.

#### Bước 5: Xoá stack SAM

Nếu bạn đã cho function `ai` gọi Bedrock ở tài khoản khác (`infra/README.md`, mục 8.4), xoá trước inline policy đã gắn tay vào role của nó. Policy này không do CloudFormation tạo:

```bash
aws iam delete-role-policy --role-name <AI_LAMBDA_ROLE> --policy-name assume-bedrock
```

Rồi xoá stack:

```bash
sam delete --stack-name askfirst-$STAGE
```

Gõ `y` khi SAM hỏi xác nhận xoá stack và artifact. Lệnh này xoá các Lambda `webhook`, `worker`, `worker-dlq`, `ai`, bảng DynamoDB, hai hàng đợi SQS, bucket, alarm, các SSM parameter do stack tạo, và artifact deploy của stack trong bucket `aws-sam-cli-managed-default`.

Kiểm tra: `aws cloudformation describe-stacks --stack-name askfirst-$STAGE` báo `does not exist`.

<!-- TODO screenshot: trang CloudFormation Stacks có askfirst-dev và askfirst-github-oidc ở trạng thái DELETE_COMPLETE (lọc "Deleted") -->

#### Bước 6: Xoá những thứ stack không quản lý

```bash
# Log group do Lambda tự tạo
for g in $(aws logs describe-log-groups --log-group-name-prefix "/aws/lambda/askfirst-$STAGE-" \
           --query 'logGroups[].logGroupName' --output text); do
  aws logs delete-log-group --log-group-name "$g"
done
# SSM parameter tạo tay (token bot, webhook secret, policy_sha256...)
for n in $(aws ssm get-parameters-by-path --path "/askfirst/$STAGE" --recursive \
           --query 'Parameters[].Name' --output text); do
  aws ssm delete-parameter --name "$n"
done
```

Kiểm tra thêm:

- **Tài nguyên tạo tay ngoài stack**, ví dụ function `ai` deploy tay khi chưa truyền `AiImageUri`, hay repo ECR khác `askfirst-ai`. `sam delete` không xoá những thứ này. Tìm bằng các lệnh ở phần kiểm tra cuối rồi xoá tay (`aws lambda delete-function --function-name <tên>`, `aws ecr delete-repository --repository-name <tên> --force`). Role tạo tay cho function đó cũng phải xoá: gỡ hết policy rồi `aws iam delete-role --role-name <tên>`.
- **SNS topic cho alarm** (chỉ khi bạn đã tạo ở 5.7.5):

  ```bash
  TOPIC_ARN=$(aws sns list-topics --query "Topics[?ends_with(TopicArn, ':askfirst-$STAGE-alarms')].TopicArn" --output text)
  aws sns delete-topic --topic-arn "$TOPIC_ARN"
  ```

- **AWS Budgets** (task BE-01) không tốn tiền nên có thể giữ. Muốn xoá: `aws budgets describe-budgets --account-id <ACCOUNT_ID>` để lấy tên, rồi `aws budgets delete-budget --account-id <ACCOUNT_ID> --budget-name <tên>`.
- **Bucket `aws-sam-cli-managed-default`** do `resolve_s3 = true` tạo, dùng chung cho mọi stack SAM trong Region. Không còn stack SAM nào thì làm rỗng bucket đó rồi xoá stack `aws-sam-cli-managed-default`.
- **Bedrock Guardrail** (`ap-southeast-2`): chỉ khi bạn đã tạo bằng `ai/scripts/create_guardrail.py` thì xoá bằng `aws bedrock delete-guardrail --region ap-southeast-2 --guardrail-identifier <id>`. Chạy với profile của tài khoản sở hữu Guardrail.

#### Bước 7: Dọn ngoài tài khoản team

PR #186 không có các mục này.

- **Role Bedrock cross-account** (chỉ khi bạn làm theo `infra/README.md`, mục 8.4). Chạy bằng profile của tài khoản có Bedrock, không phải tài khoản team:

  ```bash
  aws iam delete-role-policy --role-name askfirst-bedrock-from-team --policy-name bedrock --profile <BEDROCK_PROFILE>
  aws iam delete-role --role-name askfirst-bedrock-from-team --profile <BEDROCK_PROFILE>
  ```

- **Biến repo GitHub** trỏ tới role OIDC vừa xoá:

  ```bash
  gh variable delete AWS_AI_IMAGE_ROLE_ARN
  ```

- **Token bot Telegram** (tuỳ chọn). Nếu không dùng bot nữa, thu hồi token trong BotFather (`/revoke`). Tự làm trong Telegram; không bao giờ dán token vào log terminal, chat hay báo cáo này.

#### Kiểm tra cuối: không còn tài nguyên `askfirst-$STAGE`

```bash
aws cloudformation describe-stacks --stack-name askfirst-$STAGE 2>&1 | grep -q 'does not exist' && echo "stack: đã xoá"
aws lambda list-functions --query "Functions[?starts_with(FunctionName, 'askfirst-$STAGE')].FunctionName" --output text
aws dynamodb list-tables --query "TableNames[?starts_with(@, 'askfirst-$STAGE')]" --output text
aws sqs list-queues --queue-name-prefix "askfirst-$STAGE" --query QueueUrls --output text
aws s3 ls | grep "askfirst-$STAGE"
aws logs describe-log-groups --log-group-name-prefix "/aws/lambda/askfirst-$STAGE-" --query 'logGroups[].logGroupName' --output text
aws ssm get-parameters-by-path --path "/askfirst/$STAGE" --recursive --query 'Parameters[].Name' --output text
```

Lệnh đầu in ra `stack: đã xoá`. Sáu lệnh còn lại phải in ra rỗng (hoặc `None`).

<!-- TODO screenshot: output terminal của phần kiểm tra cuối (stack: đã xoá, các lệnh còn lại rỗng) -->

{{% notice note %}}
Một hai ngày sau, xem **Billing and Cost Management → Bills**. Mức dùng của mấy ngày cuối trước khi dọn vẫn có thể hiện ra, nhưng không được phát sinh khoản mới cho `askfirst`.
{{% /notice %}}
