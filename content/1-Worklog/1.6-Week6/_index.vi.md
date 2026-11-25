---
title: "Worklog Tuần 6"
date: 2026-10-19
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

Tuần 6: 19/10/2026 – 23/10/2026 (tuần W1 của dự án nhóm Ask First / "Hỏi Trước", vai trò của tôi là AI1).

### Mục tiêu tuần 6

* Chốt kế hoạch 6 tuần và cách chia việc trong team (2 BE, 2 AI).
* Chốt hợp đồng BE ↔ AI v1.0 để BE và AI làm song song (SH-01, Gate G0).
* Bắt đầu spike OCR (AI-20) và bộ eval v0 100 mẫu (SH-03).

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Rút kế hoạch triển khai từ 12 xuống 6 tuần (19/10–29/11/2026) và cắt bớt scope cho vừa bootcamp <br> - Ghi luật duyệt PR (mỗi PR cần 1 BE + 1 AI, CODEOWNERS chỉ ép 1 owner), chia lại việc AI1/AI2 theo độ khó, chuyển hướng dẫn build backend sang Maven <br> - Viết `features.md`, dựng board Feature → Task và tạo các issue FR, task trên GitHub | 19/10/2026 | 19/10/2026 | [implementation-plan.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/implementation-plan.md), [features.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/features.md), commit [30d1e06](https://github.com/ask-first-fcaj/ask-first/commit/30d1e06), [c9101ca](https://github.com/ask-first-fcaj/ask-first/commit/c9101ca) |
| 3 | - Viết hợp đồng BE ↔ AI v1.0: domain model, JSON Schema xuất ra cho BE, `StubPipeline` và fixtures để BE test end-to-end khi AI chưa xong (SH-01, G0) <br> - Đổi identifier và giá trị contract sang tiếng Anh, thêm quy ước code và quy ước đặt tên PR <br> - Viết hướng dẫn 3 việc nghiên cứu phụ cho AI2 | 20/10/2026 | 23/10/2026 (merge) | [PR #89](https://github.com/ask-first-fcaj/ask-first/pull/89), [PR #93](https://github.com/ask-first-fcaj/ask-first/pull/93) |
| 4 | - Thêm bộ ảnh OCR 026–050 kèm nhãn cho spike OCR (AI-20); PR này đóng, không merge <!-- TODO(not verified): lý do đóng PR #95 --> <br> - Review và approve PR của AI2: hướng dẫn gán nhãn SH-02 (#94), bộ ảnh OCR 001–025 (#96) <br> - Đóng issue SH-04 phỏng vấn người dùng (#6) <!-- TODO(not verified): ghi chú W2 nói team đã bỏ phần phỏng vấn; kiểm lại trước khi ghi --> | 21/10/2026 | 21/10/2026 | [PR #95](https://github.com/ask-first-fcaj/ask-first/pull/95), [PR #94](https://github.com/ask-first-fcaj/ask-first/pull/94), [PR #96](https://github.com/ask-first-fcaj/ask-first/pull/96) |
| 5 | - Review và approve spike OCR của AI2 (so 3 stack OCR local trên 50 ảnh, #97) <br> - Viết ADR-003: chọn RapidOCR có điều kiện, dựa trên số đo của spike (AI-20) | 22/10/2026 | 23/10/2026 (merge) | [PR #97](https://github.com/ask-first-fcaj/ask-first/pull/97), [PR #98](https://github.com/ask-first-fcaj/ask-first/pull/98), [ADR-003](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-003-ocr-rapidocr.md) |
| 6 | - Viết validator cho bộ eval JSONL, soạn 25 mẫu eval của tôi, rồi cùng AI2 bổ sung cho đủ 100 mẫu eval v0 (SH-03) <br> - Review tập test OCR độc lập 16 ảnh của AI2 (#99); cập nhật ADR-003 với kết quả kiểm tra độc lập và hạ ngưỡng CER của G2 xuống ≤ 10% <br> - Merge #89, #93, #98; đóng issue SH-02 (#4), SH-03 (#5), AI-20 (#17) | 23/10/2026 | 23/10/2026 | [PR #100](https://github.com/ask-first-fcaj/ask-first/pull/100), [PR #99](https://github.com/ask-first-fcaj/ask-first/pull/99), [PR #101](https://github.com/ask-first-fcaj/ask-first/pull/101) |

### Kết quả đạt được

* Hợp đồng BE ↔ AI v1.0 cùng `StubPipeline` và fixtures đã merge vào `main` ([#89](https://github.com/ask-first-fcaj/ask-first/pull/89)), để BE viết code Java theo schema mà không phải chờ pipeline AI.
* Bộ eval v0 đủ 100 mẫu có validator ([#100](https://github.com/ask-first-fcaj/ask-first/pull/100)).
* Chọn RapidOCR cho bước đọc ảnh (ADR-003). Trên 16 ảnh độc lập: chép đúng URL 17/18 (94%) sau bước `repair_urls`, CER 7,2% ([ADR-003](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-003-ocr-rapidocr.md)).
* 5 PR của tôi được merge trong tuần; tôi review 4 PR của AI2.

### Bài học rút ra

* Ngưỡng CER ≤ 10% được đặt sau khi đã thấy số đo, và ADR-003 phải ghi rõ điều đó. Lần sau tôi chốt ngưỡng trước khi đo.
* Tách một tập ảnh độc lập chỉ chạy một lần (không chỉnh code theo nó) giúp biết số đo trên tập tuning có bị đẹp quá không: URL không có `repair_urls` chỉ còn 83% trên tập độc lập.
