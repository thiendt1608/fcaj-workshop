---
title: "Worklog Tuần 4"
date: 2026-10-05
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

Tuần 4: 05/10/2026 – 09/10/2026.
<!-- nháp: tự điền theo lịch học FCAJ, repo dự án chưa có bằng chứng cho tuần này (commit đầu tiên 18/10/2026); sửa lại cho đúng với việc đã làm trước khi nộp -->

### Mục tiêu tuần 4

* Lập nhóm và chọn đề tài dự án.
* Tìm hiểu các dịch vụ AI trên AWS cho phần việc AI: Bedrock, Guardrails, Textract.
* Chọn cách deploy và kênh chat cho bot.

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Lập nhóm 4 người (2 BE, 2 AI), brainstorm đề tài <br> - Chọn bot giúp người lớn tuổi hỏi trước khi làm theo tin nhắn đáng ngờ; tôi nhận vai trò AI1 | 05/10/2026 | 05/10/2026 | [research-concept.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/research-concept.md) |
| 3 | - Tìm hiểu các kiểu lừa đảo qua tin nhắn ở Việt Nam và nhu cầu của người lớn tuổi, ghi ý cho research concept | 06/10/2026 | 06/10/2026 | [research-concept.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/research-concept.md) |
| 4 | - Tìm hiểu Amazon Bedrock: danh sách model, Converse API, giá on-demand theo token | 07/10/2026 | 07/10/2026 | [Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/what-is-bedrock.html), [Converse API](https://docs.aws.amazon.com/bedrock/latest/userguide/conversation-inference.html) |
| 5 | - Tìm hiểu Bedrock Guardrails để chặn prompt injection <br> - So Amazon Textract với OCR chạy local cho ảnh chụp màn hình tiếng Việt | 08/10/2026 | 08/10/2026 | [Bedrock Guardrails](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails.html), [Amazon Textract](https://docs.aws.amazon.com/textract/latest/dg/what-is.html) |
| 6 | - Đọc Telegram Bot API (webhook, nhận ảnh) và AWS SAM để chọn kênh chat và cách deploy | 09/10/2026 | 09/10/2026 | [Telegram Bot API](https://core.telegram.org/bots/api), [AWS SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) |

### Kết quả đạt được

* Chốt đề tài Hỏi Trước (Ask First) và vai trò AI1.
* Danh sách dịch vụ AWS dự định dùng: Lambda, SQS, DynamoDB, S3, Bedrock, Guardrails; Telegram làm kênh chat.

### Bài học rút ra

* Chọn đề tài từ một người dùng cụ thể (người lớn tuổi nhận tin lừa) giúp cắt scope dễ hơn chọn từ công nghệ.
