---
title: "Tổng quan"
date: 2026-11-25
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Vấn đề

Người cao tuổi ở Việt Nam liên tục nhận tin nhắn lừa đảo: phạt nguội giả, thông báo giả của ngân hàng hay dịch vụ công, "con bác đang cấp cứu, chuyển tiền gấp", link phishing. Nhiều người không có ai bên cạnh để hỏi đúng lúc tin nhắn tới, và khi hỏi được thì tiền đã mất.

#### Giải pháp

**Ask First** đặt một "người thứ ba" vào giữa tin nhắn và đồng tiền. Người dùng chuyển tiếp tin, link hoặc ảnh chụp màn hình đáng ngờ cho Telegram bot. Bot sẽ:

1. Trả lời "⏳" ngay để người dùng biết bot đang xử lý.
2. Đọc nội dung (OCR với ảnh chụp màn hình), che các số cá nhân, chạy bộ luật cố định và một knowledge base nhỏ về các kịch bản lừa đảo đã biết, sau đó mới nhờ LLM đánh giá.
3. Sửa tin "⏳" thành câu trả lời ngắn bằng tiếng Việt dễ hiểu: mức rủi ro, kịch bản lừa đảo khớp với tin, lý do và việc cần làm ngay.
4. Nếu rủi ro cao và người dùng đã liên kết người thân (double opt-in), gửi cảnh báo cho người thân đó.

Bot **không** lưu nội dung gốc của tin: chỉ lưu kết luận, mã dấu hiệu và hash, có TTL.

#### Kiến trúc

![Kiến trúc Ask First trên AWS](/images/5-Workshop/architecture.png)

Sơ đồ đi theo một lượt kiểm tra từ đầu đến cuối. Số thứ tự bên dưới khớp với các mũi tên có đánh số.

| Bước | Diễn ra gì |
|---|---|
| 1–2 | Người cao tuổi gửi tin. Telegram POST update tới **Function URL của Lambda `webhook`** kèm header `X-Telegram-Bot-Api-Secret-Token`. Function từ chối mọi request không có đúng secret (HTTP 401). |
| 3 | `webhook` dedup theo `update_id`, kiểm tra người dùng đã đồng ý điều khoản chưa, rồi ghi tin vào buffer theo chat (`BUFFER#`) trong **DynamoDB**, để nhiều tin gửi liên tiếp được kiểm tra cùng nhau. |
| 4 | `webhook` gửi "⏳" cho người dùng, đẩy job vào **SQS `jobs`** với delay 3 giây, và trả HTTP 200 cho Telegram trong chưa đầy một giây. |
| 5–6 | **Lambda `worker`** (Java) poll queue (maximum concurrency 5), đọc buffer và cờ kill switch (`CONFIG#global/FLAGS`), rồi invoke đồng bộ **Lambda `ai`** với `op=analyze`. Chỉ metadata và `file_id` của Telegram được gửi đi, không bao giờ gửi bytes của ảnh. |
| 7 | `ai` tải ảnh từ Telegram thẳng vào RAM, dùng bot token đọc từ **SSM Parameter Store**. |
| 8 | Khi cold start, `ai` tải lười trọng số model (OCR, embedder) và index của knowledge base từ **S3** về `/tmp`, kiểm SHA-256 trước khi dùng. |
| 9 | `ai` chỉ đọc/ghi các item cache (`CACHE#`, `URL#`) trong DynamoDB. Cùng một nội dung được kiểm lại trong 24 giờ sẽ trả từ cache. |
| 10–12 | Pipeline chạy Normalize → Extract (OCR) → Mask PII → Signals (luật + URL intel) → Retrieve (RAG) → Guard → Judge → Fuse → Respond. **Bedrock Guardrails** kiểm tra prompt attack, sau đó **Bedrock** (Qwen3 32B ở Sydney, leo thang lên gpt-oss-120b khi chưa chắc) đánh giá văn bản **đã mask**. Nếu bộ luật đã đủ chắc thì bỏ qua LLM. |
| 13–15 | `worker` sửa tin "⏳" thành kết quả. Nếu kết luận nguy hiểm và có người thân đã liên kết, `worker` gửi cảnh báo cho người thân kèm nút "Tôi đã liên hệ". |
| 16–17 | `worker` ghi bản ghi `CHECK#` (chỉ metadata, TTL 30 ngày) và có thể đặt job nhắc lại sau 15 phút trên SQS. Job lỗi 5 lần sẽ vào **DLQ**; một Lambda nhỏ `worker-dlq` báo người dùng là bot đang bận, và **CloudWatch alarm** chuyển sang trạng thái ALARM. |

{{% notice note %}}
Sơ đồ vẽ đúng stack `dev` nhóm đang chạy (đối chiếu ngày 25/11/2026): 4 Lambda, Bedrock ở account riêng mà `ai` assume role sang, alarm DLQ gửi email qua SNS. Khi bạn tự deploy theo workshop: SnapStart **tắt** theo mặc định (`WorkerSnapStart=None`), VietOCR là tuỳ chọn, SNS topic cho alarm là tuỳ chọn (`AlarmTopicArn`, mục 5.7.5), Bedrock gọi ngay trong account của bạn nếu không đặt `BEDROCK_ROLE_ARN`, và GitHub Actions chỉ build và đẩy image `ai` qua OIDC; `sam deploy` chạy bằng tay.
{{% /notice %}}

#### Các dịch vụ được dùng và lý do

| Dịch vụ | Vai trò trong Ask First | Vì sao chọn |
|---|---|---|
| **AWS Lambda** | `webhook`, `worker`, `worker-dlq` (zip Java 21) và `ai` (container image Python) | Lượng tin nhỏ và không đều nên trả theo request. Function URL cho sẵn HTTPS endpoint mà không cần API Gateway. Container image cho phép `ai` mang theo thư viện ML vượt giới hạn 250 MB của gói zip. |
| **Amazon SQS + DLQ** | Hàng đợi giữa `webhook` và `worker` | `webhook` trả lời Telegram ngay; retry (tối đa 5 lần) và DLQ có sẵn; `DelaySeconds` dùng cho debounce 3 giây và nhắc lại sau 15 phút mà không cần scheduler. |
| **Amazon DynamoDB** | Một bảng `askfirst-<stage>`: user, consent, buffer, check, cache, flags | Billing on-demand, TTL tự xoá dữ liệu (tối thiểu hoá dữ liệu), conditional write cho idempotency, bật PITR. |
| **Amazon S3** | Trọng số model, index knowledge base, mẫu người dùng đóng góp (`donated/`, tự xoá sau 30 ngày) | Lưu trữ rẻ; đổi model không cần build lại image. Bật Block Public Access và SSE. |
| **Amazon Bedrock** | LLM judge (Qwen3 32B, dự phòng gpt-oss-120b) ở `ap-southeast-2` | Serverless, trả theo token, không phải vận hành GPU. Lúc team chọn, Sydney có model open-weight còn Singapore thì không. |
| **Bedrock Guardrails** | Phát hiện prompt attack trong input | Nội dung tin nhắn vốn là dữ liệu không tin cậy. |
| **Amazon ECR** | Chứa image `ai` (`askfirst-ai`, giữ 5 image mới nhất) | Bắt buộc với Lambda container image. |
| **SSM Parameter Store** | Bot token và webhook secret (`SecureString`), cùng cấu hình của stack | Secret không nằm trong code hay cấu hình Lambda; tier standard không tốn thêm phí. |
| **Amazon CloudWatch** | Log của mọi function, alarm `askfirst-<stage>-jobs-dlq-not-empty` | Có sẵn, không cần cài agent. |
| **AWS SAM / CloudFormation** | Toàn bộ stack dạng code (`infra/template.yaml`) | Một lệnh để build và deploy; cùng template tạo được `dev` hoặc `prod`. |
| **IAM + GitHub OIDC** | Role least-privilege; GitHub Actions đẩy image mà không cần key dài hạn | Không lưu access key trong GitHub. |

Các dịch vụ **cố ý không dùng**: API Gateway, Step Functions, EventBridge, OpenSearch, VPC/NAT Gateway (riêng NAT Gateway khoảng 32 USD/tháng), và GPU tự host trên EC2/SageMaker (nguồn: `docs/system-architecture.md` §3.2 trong repo dự án).

#### Đối chiếu với các trụ cột AWS Well-Architected

| Trụ cột | Ask First đáp ứng thế nào |
|---|---|
| **Operational excellence** | Infrastructure as code bằng SAM; CI trên GitHub Actions (Maven verify, pytest, contract test giữa Java và Python); log JSON dùng chung `trace_id` giữa `worker` và `ai`; alarm cho DLQ. |
| **Security** | Function URL chỉ nhận request có header secret của Telegram; secret nằm trong SSM `SecureString`, chỉ giải mã được qua SSM (`kms:ViaService`); IAM least-privilege (ví dụ `ai` chỉ được đụng các key DynamoDB bắt đầu bằng `CACHE#` hoặc `URL#`, và chỉ vài prefix S3); S3 Block Public Access và mã hoá; số cá nhân được mask trước khi văn bản đi tới Bedrock; dùng OIDC thay access key trong CI. |
| **Reliability** | SQS retry kèm `ReportBatchItemFailures`, vào DLQ sau 5 lần lỗi, dedup theo `update_id`, chế độ suy giảm "chỉ chạy luật" khi LLM lỗi, DynamoDB PITR. |
| **Performance efficiency** | Ba function được cấu hình theo đúng việc (`webhook` nhanh, `worker` tối đa 120 giây, `ai` 3008 MB và 90 giây); model CPU nhỏ, nạp lười và giữ trong RAM; cache kết luận cho nội dung lặp lại. |
| **Cost optimization** | Thuần trả theo lượt dùng, không VPC/NAT, DynamoDB on-demand, model open-weight giá thấp, bỏ qua LLM khi luật đã chắc. Chi phí Bedrock đo được khoảng 0,00076 USD cho mỗi lượt kiểm tra văn bản (nguồn: `docs/results/lambda-p95.md`, 100 lượt ngày 24/11/2026). |
| **Sustainability** | Không có server chạy không tải: mọi thành phần scale về 0 khi không ai gửi tin. |
