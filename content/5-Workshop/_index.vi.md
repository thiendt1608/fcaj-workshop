---
title: "Workshop"
date: 2026-11-25
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Ask First ("Hỏi Trước"): bot kiểm tra tin lừa đảo chạy serverless trên AWS

#### Tổng quan

**Ask First** là một Telegram bot giúp người cao tuổi kiểm tra tin nhắn đáng ngờ trước khi làm theo. Người dùng chuyển tiếp tin nhắn, đường link hoặc ảnh chụp màn hình cho bot. Sau vài giây, bot trả lời bằng tiếng Việt dễ hiểu: tin này rủi ro tới mức nào, giống kịch bản lừa đảo nào và nên làm gì tiếp theo. Nếu rủi ro cao, bot có thể báo cho người thân đã đồng ý nhận cảnh báo.

Toàn bộ hệ thống chạy serverless trên AWS:

+ **AWS Lambda** (Java 21) nhận update từ Telegram qua **Function URL** (`webhook`) và xử lý job từ **Amazon SQS** (`worker`), có **dead-letter queue** cho job lỗi.
+ Lambda thứ ba là **`ai`**, đóng gói dạng **container image** (Python 3.12): chạy model OCR và embedding trên CPU, áp dụng luật và RAG, rồi gọi **Amazon Bedrock** (Qwen3 32B, dự phòng gpt-oss-120b) có **Bedrock Guardrails** đứng trước.
+ **Amazon DynamoDB** lưu metadata có TTL, **Amazon S3** lưu trọng số model và index của knowledge base, **SSM Parameter Store** giữ bot token và webhook secret, **Amazon CloudWatch** thu log và alarm cho DLQ.
+ Mọi tài nguyên được viết dưới dạng code bằng **AWS SAM** và deploy ở `ap-southeast-1` (Singapore); Bedrock được gọi ở `ap-southeast-2` (Sydney).

Trong workshop này, bạn sẽ dựng lại đúng hệ thống đó trên tài khoản AWS của mình theo từng bước, và kết thúc với một bot chạy thật để nhắn tin trên Telegram.

#### Nội dung

1. [Tổng quan](5.1-overview/)
2. [Chuẩn bị](5.2-prerequisites/)
3. [Deploy hạ tầng (SAM)](5.3-deploy-infrastructure/)
4. [Deploy AI function](5.4-deploy-ai-function/)
5. [Kết nối Telegram](5.5-connect-telegram/)
6. [Kiểm thử và nghiệm thu](5.6-testing-validation/)
7. [Giám sát](5.7-monitoring/)
8. [Chi phí](5.8-cost/)
9. [Dọn dẹp tài nguyên](5.9-cleanup/)
10. [Demo và bài học kinh nghiệm](5.10-demo-lessons/)
