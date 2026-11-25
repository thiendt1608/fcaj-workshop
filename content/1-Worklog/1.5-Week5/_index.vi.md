---
title: "Worklog Tuần 5"
date: 2026-10-12
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

Tuần 5: 12/10/2026 – 16/10/2026. Dòng CN là Chủ nhật 18/10/2026, ngày repo dự án có commit đầu tiên.
<!-- nháp: tự điền theo lịch học FCAJ, repo dự án chưa có bằng chứng cho tuần này (commit đầu tiên 18/10/2026); sửa lại cho đúng với việc đã làm trước khi nộp -->

### Mục tiêu tuần 5

* Viết research concept, kiến trúc và kế hoạch triển khai bản đầu.
* Viết và chạy thử hợp đồng dữ liệu BE ↔ AI để hai bên làm song song.
* Dựng repo dự án và tạo các task đầu tiên.

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Viết research concept: vấn đề, người dùng, các kịch bản lừa đảo, tiêu chí thành công | 12/10/2026 | 12/10/2026 | [research-concept.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/research-concept.md) |
| 3 | - Vẽ kiến trúc: Telegram → Lambda `webhook` → SQS → `worker` → Lambda `ai` → Bedrock; chọn không dùng VPC | 13/10/2026 | 13/10/2026 | [system-architecture.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/system-architecture.md) |
| 4 | - Viết kế hoạch triển khai bản đầu (12 tuần, các gate G0–G2, mốc M1–M6) | 14/10/2026 | 14/10/2026 | [implementation-plan.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/implementation-plan.md) |
| 5 | - Viết code Python mẫu cho hợp đồng BE ↔ AI (pydantic), chạy thử 20 test và `ruff` <br> - Ghi chú nghiên cứu AI local (OCR, LLM), sau này thành 3 việc nghiên cứu phụ cho AI2 | 15/10/2026 | 15/10/2026 | [implementation-plan.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/implementation-plan.md) (§3), [ai-viec-nghien-cuu-phu.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/ai-viec-nghien-cuu-phu.md) |
| 6 | - Viết hướng dẫn cho team BE và team AI | 16/10/2026 | 16/10/2026 | [huong-dan-backend.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/huong-dan-backend.md), [huong-dan-ai.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/huong-dan-ai.md) |
| CN | - Tạo các commit đầu tiên của repo: khung repo, CI, CODEOWNERS, tài liệu kiến trúc, hướng dẫn BE/AI, ảnh kiến trúc <br> - Mở issue #1–#18 (task BE-xx, SH-xx, AI-xx) | 18/10/2026 | 18/10/2026 | commit [4842bd3](https://github.com/ask-first-fcaj/ask-first/commit/4842bd3), [c1ef4b6](https://github.com/ask-first-fcaj/ask-first/commit/c1ef4b6), [issues](https://github.com/ask-first-fcaj/ask-first/issues?q=is%3Aissue) |

### Kết quả đạt được

* Bộ tài liệu đầu tiên vào repo ngày 18/10: research concept, kiến trúc, kế hoạch, hai bản hướng dẫn (commit [4842bd3](https://github.com/ask-first-fcaj/ask-first/commit/4842bd3), 4.569 dòng).
* Code Python của hợp đồng BE ↔ AI đã chạy thử ngày 15/10 (20 test pass) trước khi giao cho BE.

### Bài học rút ra

* Code mẫu trong tài liệu phải chạy thử trước khi giao, và ghi rõ phần nào chưa chạy: kế hoạch ghi rõ phần codegen Java chưa chạy để BE kiểm ở tuần đầu.
