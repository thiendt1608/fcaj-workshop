---
title: "Worklog Tuần 7"
date: 2026-10-26
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

Tuần 7: 26/10/2026 – 30/10/2026 (tuần W2 của dự án). Một phần việc kéo sang thứ Bảy 31/10.

### Mục tiêu tuần 7

* Hoàn tất Gate G0 (test contract phía Java) và chốt Gate G2 (OCR).
* Dựng khung pipeline AI: Bedrock client (AI-02), eval harness (AI-01), judge prompt + hậu kiểm (AI-04), handler Lambda `ai` (AI-03b).
* Review các PR đầu tiên của AI2 (mask PII, KB v1).

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Chuẩn bị AI-02: đọc tài liệu Bedrock Converse API và cấu hình retry của botocore, phác `models.yaml` cho các role judge, backup, vlm <!-- nháp: tự điền, không có bằng chứng trên git; sửa lại cho đúng trước khi nộp --> | 26/10/2026 | 26/10/2026 | [Converse API](https://docs.aws.amazon.com/bedrock/latest/userguide/conversation-inference.html) |
| 3 | - Đọc trước PR contract phía Java của BE (#102), đối chiếu model Java với JSON Schema và fixtures của contract v1.0 <!-- nháp: tự điền, không có bằng chứng trên git; sửa lại cho đúng trước khi nộp --> | 27/10/2026 | 27/10/2026 | [#102](https://github.com/ask-first-fcaj/ask-first/pull/102) |
| 4 | - Review và approve PR của BE: `TelegramAdapter`, contract model và handler phía Java (SH-01) <br> - Đóng issue Gate G0 (#2) và SH-01 (#3) | 28/10/2026 | 28/10/2026 | [PR #102](https://github.com/ask-first-fcaj/ask-first/pull/102) |
| 5 | - Viết Bedrock Converse client và khung spike Qwen3 32B (AI-02); spike chưa chạy được vì chưa có credential <br> - Rà tiến độ các task của team AI | 29/10/2026 | 30/10/2026 (merge) | [PR #103](https://github.com/ask-first-fcaj/ask-first/pull/103) |
| 6 | - Rà lại phần OCR và eval: sửa cách chấm URL OCR theo ranh giới thay vì so chuỗi con, thêm workflow chạy lại benchmark trên CI (#105); viết LABELING bản 2 theo mã kịch bản (#106); gán lại bộ eval v0 theo mã kịch bản, validator bắt `scenario`/`provenance` (#107); duyệt mã kịch bản SH-02 (#108); soát lại nhãn 16 ảnh OCR độc lập (#109) <br> - Mở PR cổng test contract phía Java cho G0 (#104, merge 11/11) <br> - Review PR của AI2: mask PII (#110, request changes rồi approve), KB v1 (#111, request changes 30/10 và 31/10, approve 31/10) <br> - Tối 30/10 mở 5 PR, merge thứ Bảy 31/10: chốt Gate G2 trong ADR-003 (#112), nháp chính sách quyền riêng tư v0.1 (SH-07, #114), harness `run_eval.py` tính recall và báo nhầm (AI-01, #113), judge prompt v1 + parse JSON + hậu kiểm output LLM (AI-04, #115), handler Lambda `ai` + cache DynamoDB (AI-03b, #116) <br> - 31/10: mở PR thêm thẻ KB N2.2, N5.2 và sửa `advice` mạnh hơn nguồn (AI-25, #117, merge 03/11) | 30/10/2026 | 31/10/2026 | [#104](https://github.com/ask-first-fcaj/ask-first/pull/104), [#105](https://github.com/ask-first-fcaj/ask-first/pull/105), [#106](https://github.com/ask-first-fcaj/ask-first/pull/106), [#107](https://github.com/ask-first-fcaj/ask-first/pull/107), [#108](https://github.com/ask-first-fcaj/ask-first/pull/108), [#109](https://github.com/ask-first-fcaj/ask-first/pull/109), [#110](https://github.com/ask-first-fcaj/ask-first/pull/110), [#111](https://github.com/ask-first-fcaj/ask-first/pull/111), [#112](https://github.com/ask-first-fcaj/ask-first/pull/112), [#113](https://github.com/ask-first-fcaj/ask-first/pull/113), [#114](https://github.com/ask-first-fcaj/ask-first/pull/114), [#115](https://github.com/ask-first-fcaj/ask-first/pull/115), [#116](https://github.com/ask-first-fcaj/ask-first/pull/116), [#117](https://github.com/ask-first-fcaj/ask-first/pull/117) |

### Kết quả đạt được

* Gate G2 đạt về độ chính xác, chốt RapidOCR ngày 30/10 ([ADR-003](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-003-ocr-rapidocr.md)). Chạy lại benchmark trên CI cho cùng số URL và CER như trên máy dev.
* Khung pipeline AI đã có trên `main`: Bedrock client, eval harness, judge prompt v1 có hậu kiểm, handler Lambda `ai` có cache.
* 11 PR của tôi được merge trong 30–31/10; tôi review 3 PR (1 của BE, 2 của AI2).
* Chưa xong: Gate G1 (chọn LLM, hạn 30/10 theo plan) chưa chạy trong tuần này, ADR-002 chốt ngày 11/11. Lý do: chưa có credential AWS và Bedrock chưa bật ở `ap-southeast-2` (BE-05, [#11](https://github.com/ask-first-fcaj/ask-first/issues/11)), nên spike AI-02 chưa gọi được model thật ([#103](https://github.com/ask-first-fcaj/ask-first/pull/103)).

### Bài học rút ra

* Cách chấm sai làm số đẹp hơn thực tế: so URL bằng chuỗi con thì URL đọc thiếu vẫn tính là đúng, nên tôi đổi sang so theo ranh giới ([#105](https://github.com/ask-first-fcaj/ask-first/pull/105)).
* Branch protection có hai chỗ dễ vướng: merge `main` vào nhánh làm GitHub gỡ approve cũ nên phải xin approve lại, và file trong `/ai/` cần code owner team AI approve. Tôi merge `main` vào nhánh trước rồi mới nhờ review để khỏi xin approve hai lần.
