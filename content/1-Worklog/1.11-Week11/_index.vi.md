---
title: "Worklog Tuần 11"
date: 2026-11-23
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

Tuần 11: 23/11/2026 – 27/11/2026 (tuần W6 của dự án, mốc M6 demo). Tuần này đang diễn ra: nhật ký ghi đến 25/11/2026.

### Mục tiêu tuần 11

* Chạy eval cuối với prompt `judge_v3` và quyết có bật VietOCR không.
* Đưa Lambda `ai` lên tài khoản AWS của team (BE-06) và đo độ trễ thật trên Lambda (văn bản, ảnh, cold start).
* Làm dự toán chi phí trên AWS Pricing Calculator (SH-09).
* Đóng các issue AI đã xong và chuẩn bị demo M6.

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Sửa test luật chạy chập chờn do đo thời gian (#177, merge 24/11) <br> - Review và merge PR của AI2 và BE: tích hợp S3/DynamoDB/Telegram và preload (INT-01, #173), #174, kiểm tra mạng của URL (AI-24b, #175), sửa OCR dấu gạch nối (#176), VietOCR (#178) | 23/11/2026 | 24/11/2026 | [#177](https://github.com/ask-first-fcaj/ask-first/pull/177), [#173](https://github.com/ask-first-fcaj/ask-first/pull/173), [#174](https://github.com/ask-first-fcaj/ask-first/pull/174), [#175](https://github.com/ask-first-fcaj/ask-first/pull/175), [#176](https://github.com/ask-first-fcaj/ask-first/pull/176), [#178](https://github.com/ask-first-fcaj/ask-first/pull/178) |
| 3 | - Chạy eval cuối W6 với `judge_v3`, thử và bỏ `judge_v4`, quyết bật VietOCR (#179) <br> - Cache Maven trên CI (#180) <br> - Build image Lambda `ai` trên GitHub Actions và thêm function vào template SAM (BE-06, #181) <br> - Gọi Bedrock khác tài khoản qua assume role (#182) <br> - Đo độ trễ Lambda `ai` trên tài khoản team, cập nhật độ trễ OCR trong ADR-003 (#183) <br> - Làm dự toán chi phí trên AWS Pricing Calculator (SH-09, commit trong #169) <br> - Review: câu chữ tin bot (SH-08, #157, approve), #170, #171, #172; merge #171 #172 | 24/11/2026 | 25/11/2026 | [#179](https://github.com/ask-first-fcaj/ask-first/pull/179), [#180](https://github.com/ask-first-fcaj/ask-first/pull/180), [#181](https://github.com/ask-first-fcaj/ask-first/pull/181), [#182](https://github.com/ask-first-fcaj/ask-first/pull/182), [#183](https://github.com/ask-first-fcaj/ask-first/pull/183), [#169](https://github.com/ask-first-fcaj/ask-first/pull/169), [#157](https://github.com/ask-first-fcaj/ask-first/pull/157), [#170](https://github.com/ask-first-fcaj/ask-first/pull/170), [#171](https://github.com/ask-first-fcaj/ask-first/pull/171), [#172](https://github.com/ask-first-fcaj/ask-first/pull/172), [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) |
| 4 | - Đo p95 đầu-cuối cho tin ảnh qua bot trên Lambda (INT-02, #184) <br> - Mở #185 (script warm-up sau deploy, BE-10), #186 (hướng dẫn dọn tài nguyên, SH-10), #187 (security review lane A, nâng `jackson`, thêm `pip-audit`, BE-09b); ba PR này còn mở <br> - Merge #167 (`judge_v3`), #169 (đo token Bedrock và dự toán chi phí), #179, #181, #184; approve #170 <br> - Đóng các issue AI đã xong, trong đó có #56 (M1) và #165 | 25/11/2026 | 25/11/2026 | [#184](https://github.com/ask-first-fcaj/ask-first/pull/184), [#185](https://github.com/ask-first-fcaj/ask-first/pull/185), [#186](https://github.com/ask-first-fcaj/ask-first/pull/186), [#187](https://github.com/ask-first-fcaj/ask-first/pull/187), [lambda-p95.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md) |
| 5 | <!-- TODO: fill in (chưa tới ngày) --> | 26/11/2026 | <!-- TODO --> | <!-- TODO --> |
| 6 | <!-- TODO: fill in (chưa tới ngày; theo plan là demo M6) --> | 27/11/2026 | <!-- TODO --> | <!-- TODO --> |

### Kết quả đạt được

* Eval cuối (`judge_v3`, 24/11, 3 lượt): recall 100%, báo nhầm 5% / 5% / 5%, đúng mức nhãn 99,3%. Vẫn chỉ có 134 mẫu và 0 mẫu thật ([eval-report-final.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-final.md)).
* Trên Lambda `ai` (tài khoản team): tin chữ khi container ấm p95 3,5 s (100 câu), RAG p95 45 ms, cold start init p50 6,2 s, tối đa 8,5 s ([lambda-p95.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md)).
* Tin ảnh đầu-cuối p95 17,0 s (21 ảnh), vượt mốc 15 s; tôi chấp nhận vì vẫn dưới ngưỡng khoảng 30 s cho ảnh. RapidOCR trên Lambda có p95 5,07 s, vượt mục tiêu 3 s của ADR-003; bật VietOCR thêm p95 3,42 s mỗi ảnh ([lambda-p95.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md), [ocr-vietocr.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-vietocr.md)).
* Dự toán chi phí cho 3.000 lượt kiểm tra mỗi tháng: khoảng 6,2 USD/tháng nếu không có Free Tier, khoảng 2,4 USD/tháng nếu còn Free Tier (chưa kiểm tài khoản còn Free Tier hay không) ([cost-estimate.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/cost-estimate.md)).
<!-- TODO(not verified): đếm lại số PR merge và review của tuần sau khi tuần kết thúc -->

### Bài học rút ra

* Thử prompt mới thì đặt tiêu chí trước khi đo: `judge_v4` viết dễ đọc hơn rõ nhưng báo nhầm tăng ở 2/3 lượt, đúng cùng một mẫu, nên tôi bỏ ([eval-report-final.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-final.md)).
* Lần init đầu sau deploy của Lambda `ai` chạm mốc 10 s và phải chạy lại, nên cần warm-up ngay sau deploy (#185).
<!-- TODO(not verified): bài học về image Docker (OCI index, attestation) khi deploy Lambda container; chỉ có trong ghi chú cá nhân, chưa có trong docs của repo -->
