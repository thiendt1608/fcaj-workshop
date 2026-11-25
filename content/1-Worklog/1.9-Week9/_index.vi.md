---
title: "Worklog Tuần 9"
date: 2026-11-09
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

Tuần 9: 09/11/2026 – 13/11/2026 (tuần W4 của dự án).

### Mục tiêu tuần 9

* Chạy Bedrock thật: spike Qwen3 32B (AI-02) và benchmark chọn model judge để chốt Gate G1 (AI-03).
* Thêm lớp chống prompt injection (AI-08), chấm lời giải thích (AI-11), bộ red-team (AI-14).
* Review các PR RAG và eval gate trong CI của AI2.

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Review và approve PR hạ tầng SAM dev và tích hợp Telegram của BE (BE-03, BE-14, BE-20, BE-21, BE-24, BE-25, BE-26) | 09/11/2026 | 09/11/2026 | [PR #131](https://github.com/ask-first-fcaj/ask-first/pull/131) |
| 3 | - Chuẩn bị benchmark Gate G1: viết script chạy 4 model trên 100 mẫu eval và tính recall, báo nhầm, tỉ lệ JSON hợp lệ <br> - Soạn nháp 34 mẫu red-team `hard` (AI-14) <!-- nháp: tự điền, không có bằng chứng trên git; sửa lại cho đúng trước khi nộp --> | 10/11/2026 | 11/11/2026 | [#133](https://github.com/ask-first-fcaj/ask-first/pull/133), [#137](https://github.com/ask-first-fcaj/ask-first/pull/137) |
| 4 | - Chạy spike Bedrock Qwen3 32B, chốt ADR-001 và điền `model_id` (AI-02, #132) <br> - Benchmark 4 model trên 100 mẫu eval, chốt Gate G1 trong ADR-002: giữ Qwen3 32B tắt thinking (AI-03, #133); thêm mốc tham chiếu Gemini (#141) và model dự phòng gpt-oss-120b (#142) <br> - Quyết chưa bật `outputConfig` JSON schema cho judge vì prompt đã cho JSON hợp lệ 99/100 (AI-04, #134) <br> - Phát hiện prompt injection 2 lớp: luật local + Amazon Bedrock Guardrails (AI-08, #135) <br> - Chấm lời giải thích bằng LLM-as-a-judge và phiếu chấm tay (AI-11, #136); thêm 34 mẫu red-team khó và phân tích lỗi pipeline thật (AI-14, #137) <br> - Sửa prompt judge: xưng hô (`judge_v2`, #144) và không bỏ cả câu trả lời khi `requested_actions` lạ (#145, issue #138) <br> - Sửa CI để test contract Java chạy `AiContractTest`, merge #104 (G0) <br> - Review và merge PR của AI2: điểm chấm tay AI2 cho AI-11 (#146), RAG local (AI-29, #147) <br> - Tối: mở #149 và #150 | 11/11/2026 | 11/11/2026 | [#132](https://github.com/ask-first-fcaj/ask-first/pull/132), [#133](https://github.com/ask-first-fcaj/ask-first/pull/133), [#134](https://github.com/ask-first-fcaj/ask-first/pull/134), [#135](https://github.com/ask-first-fcaj/ask-first/pull/135), [#136](https://github.com/ask-first-fcaj/ask-first/pull/136), [#137](https://github.com/ask-first-fcaj/ask-first/pull/137), [#141](https://github.com/ask-first-fcaj/ask-first/pull/141), [#142](https://github.com/ask-first-fcaj/ask-first/pull/142), [#144](https://github.com/ask-first-fcaj/ask-first/pull/144), [#145](https://github.com/ask-first-fcaj/ask-first/pull/145), [#104](https://github.com/ask-first-fcaj/ask-first/pull/104), [#146](https://github.com/ask-first-fcaj/ask-first/pull/146), [#147](https://github.com/ask-first-fcaj/ask-first/pull/147), [ADR-001](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-001-bedrock-qwen3.md), [ADR-002](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-002-g1-judge-model.md), [Bedrock structured outputs](https://docs.aws.amazon.com/bedrock/latest/userguide/structured-output.html), [Bedrock Guardrails languages](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-supported-languages.html) |
| 5 | - Merge #149: luật bắt tin xin OTP mà không gọi tên mã, và tin xin ảnh thẻ/CCCD (AI-14, issue #139) <br> - Review và merge PR của AI2: nối RAG vào `RealPipeline` (INT-03, #151), eval gate trên PR (AI-09, #153) | 12/11/2026 | 12/11/2026 | [#149](https://github.com/ask-first-fcaj/ask-first/pull/149), [#151](https://github.com/ask-first-fcaj/ask-first/pull/151), [#153](https://github.com/ask-first-fcaj/ask-first/pull/153) |
| 6 | - Review và approve PR liên kết người thân double opt-in của BE (BE-28) | 13/11/2026 | 13/11/2026 | [PR #152](https://github.com/ask-first-fcaj/ask-first/pull/152) |

### Kết quả đạt được

* Qwen3 32B trên Bedrock (tắt thinking) là model judge. Spike từ máy dev: p50 1,39 s, p95 1,58 s trên 30 lời gọi ([ADR-001](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-001-bedrock-qwen3.md)). Trên 100 mẫu: JSON hợp lệ 99%; lượt buổi sáng đạt cả recall ≥ 90% và báo nhầm ≤ 10%, 5 lượt buổi chiều báo nhầm 15% (3/20), recall 98,3% ([ADR-002](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-002-g1-judge-model.md)). Phần báo nhầm chuyển cho AI-13.
* Bộ red-team `hard` có 34 mẫu (30 lừa đảo, 4 lành tính khó); nhãn do AI soạn nháp, chưa có người thứ hai duyệt nên chưa tính vào Gate ([ai14-redteam.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ai14-redteam.md)).
* 12 PR của tôi được merge trong tuần; tôi review 6 PR (4 của AI2, 2 của BE).

### Bài học rút ra

* Cùng code, cùng prompt, số báo nhầm của Qwen3 lệch 1 mẫu giữa lượt sáng và lượt chiều. Với mẫu số 20, mỗi mẫu là 5 điểm %, nên tôi chạy nhiều lượt và ghi độ dao động thay vì tin một lượt ([ADR-002](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-002-g1-judge-model.md)).
* Bật thinking không đáng: recall chỉ tăng 1 mẫu, báo nhầm tăng từ 10% lên 45%, độ trễ gần gấp đôi (ADR-002).
