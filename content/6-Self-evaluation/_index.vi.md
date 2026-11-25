---
title: "Tự đánh giá"
date: 2026-11-25
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

Trong thời gian thực tập tại chương trình First Cloud AI Journey, tôi là thành viên phụ trách mảng AI (AI1) của dự án nhóm **Ask First**. Tôi làm phần pipeline kiểm tra tin nhắn lừa đảo: chọn và đo OCR, chọn model Bedrock làm judge, dựng bộ eval và các ngưỡng phân mức, đóng gói Lambda `ai` dạng container image và đo độ trễ, chi phí trên AWS. Bảng dưới là tự đánh giá của tôi, dẫn chứng lấy từ worklog và tài liệu trong repo dự án.

| STT | Tiêu chí | Tốt | Khá | Trung bình | Nhận xét / dẫn chứng |
|---|---|---|---|---|---|
| 1 | Kiến thức chuyên môn | ✅ | | | Nắm được Lambda container image, SQS, DynamoDB, Bedrock (Qwen3 32B, Guardrails), OCR và cách đánh giá model. Các quyết định kỹ thuật đều có ADR và số đo đi kèm (ADR-001, ADR-003, ADR-005). |
| 2 | Tự học | ✅ | | | Tự tìm hiểu OCR tiếng Việt, cách viết prompt cho LLM judge, cách đo recall và false alarm, cách đóng gói Lambda container; phần lớn là kiến thức mới với tôi trước chương trình. |
| 3 | Chủ động | ✅ | | | Chủ động chạy lại benchmark trên CI để kiểm số đo ở máy dev, dựng bộ red-team riêng (34 mẫu) và ghi rõ các giới hạn (134 mẫu thay vì 300, chưa có mẫu thật) thay vì để người khác phát hiện. |
| 4 | Kỷ luật | | ✅ | | Đa số việc xong theo mốc, nhưng gate G1 (chọn LLM) trễ từ 30/10 sang 11/11. PR thường dồn merge vào vài ngày và worklog không ghi đều từng ngày. |
| 5 | Giao tiếp | | ✅ | | Trao đổi qua issue, PR và ADR là chính, có contract BE ↔ AI v1.0 để hai bên làm song song. Còn hạn chế khi báo sớm các rủi ro trễ hạn cho cả nhóm. |
| 6 | Teamwork | ✅ | | | Review khoảng 26 PR của AI2 và BE trong tuần 6–10; contract có `StubPipeline` và fixtures để BE viết code Java mà không phải chờ pipeline AI. |
| 7 | Giải quyết vấn đề | ✅ | | | Sửa cách chấm URL từ so khớp chuỗi con sang so khớp có biên (#105); giữ ngưỡng riêng cho đường rules-only khi ngưỡng mới làm CI eval gate fail; xử lý lỗi INIT 10 s của Lambda `ai` bằng warm-up sau deploy (#185). |
| 8 | Đóng góp cho Workshop | ✅ | | | Phần AI pipeline, eval report, số đo độ trễ trên Lambda và ước tính chi phí (khoảng 0,000753 USD token cho mỗi lần kiểm tra text) dùng trực tiếp trong Proposal và Workshop. |

### Cần cải thiện
* **Đặt tiêu chí trước khi đo.** Ngưỡng CER ≤ 10% của OCR được đặt sau khi đã thấy kết quả. Lần sau tôi chốt ngưỡng và cách chấm trước khi chạy.
* **Giữ nhịp đều hơn.** Ghi worklog hằng ngày, chia PR nhỏ và merge đều thay vì dồn, báo sớm khi một gate có nguy cơ trễ.
* **Dữ liệu đánh giá còn mỏng.** Bộ eval mới có 134 mẫu và chưa có mẫu thật; ảnh end-to-end p95 17,0 s vẫn cao hơn mục tiêu 15 s. Đây là hai việc tôi muốn làm tiếp nếu dự án còn chạy.
