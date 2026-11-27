---
title: "Worklog Tuần 11"
date: 2026-11-23
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

Tuần 11: 23/11/2026 – 27/11/2026 (tuần W6 của dự án, mốc M6 demo).

### Mục tiêu tuần 11

* Chạy eval cuối với prompt `judge_v3` và quyết có bật VietOCR không.
* Đưa Lambda `ai` lên tài khoản AWS của team (BE-06) và đo độ trễ thật trên Lambda (văn bản, ảnh, cold start).
* Làm dự toán chi phí trên AWS Pricing Calculator (SH-09).
* Đóng các issue AI đã xong và chuẩn bị demo M6.
* Sửa các lỗi nút bấm của bot trước khi quay video demo, dựng site báo cáo thực tập.

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Sửa test luật chạy chập chờn do đo thời gian (#177, merge 24/11) <br> - Review và merge PR của AI2 và BE: tích hợp S3/DynamoDB/Telegram và preload (INT-01, #173), #174, kiểm tra mạng của URL (AI-24b, #175), sửa OCR dấu gạch nối (#176), VietOCR (#178) | 23/11/2026 | 24/11/2026 | [#177](https://github.com/ask-first-fcaj/ask-first/pull/177), [#173](https://github.com/ask-first-fcaj/ask-first/pull/173), [#174](https://github.com/ask-first-fcaj/ask-first/pull/174), [#175](https://github.com/ask-first-fcaj/ask-first/pull/175), [#176](https://github.com/ask-first-fcaj/ask-first/pull/176), [#178](https://github.com/ask-first-fcaj/ask-first/pull/178) |
| 3 | - Chạy eval cuối W6 với `judge_v3`, thử và bỏ `judge_v4`, quyết bật VietOCR (#179) <br> - Cache Maven trên CI (#180) <br> - Build image Lambda `ai` trên GitHub Actions và thêm function vào template SAM (BE-06, #181) <br> - Gọi Bedrock khác tài khoản qua assume role (#182) <br> - Đo độ trễ Lambda `ai` trên tài khoản team, cập nhật độ trễ OCR trong ADR-003 (#183) <br> - Làm dự toán chi phí trên AWS Pricing Calculator (SH-09, commit trong #169) <br> - Review: câu chữ tin bot (SH-08, #157, approve), #170, #171, #172; merge #171 #172 | 24/11/2026 | 25/11/2026 | [#179](https://github.com/ask-first-fcaj/ask-first/pull/179), [#180](https://github.com/ask-first-fcaj/ask-first/pull/180), [#181](https://github.com/ask-first-fcaj/ask-first/pull/181), [#182](https://github.com/ask-first-fcaj/ask-first/pull/182), [#183](https://github.com/ask-first-fcaj/ask-first/pull/183), [#169](https://github.com/ask-first-fcaj/ask-first/pull/169), [#157](https://github.com/ask-first-fcaj/ask-first/pull/157), [#170](https://github.com/ask-first-fcaj/ask-first/pull/170), [#171](https://github.com/ask-first-fcaj/ask-first/pull/171), [#172](https://github.com/ask-first-fcaj/ask-first/pull/172), [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) |
| 4 | - Đo p95 đầu-cuối cho tin ảnh qua bot trên Lambda (INT-02, #184) <br> - Mở #185 (script warm-up sau deploy, BE-10), #186 (hướng dẫn dọn tài nguyên, SH-10), #187 (security review lane A, nâng `jackson`, thêm `pip-audit`, BE-09b), #188 (security review lane B, BE-09a), #190, #191, #192 <br> - Dựng site báo cáo thực tập từ template Hugo của FCAJ, điền worklog, proposal, sự kiện và workshop <br> - Merge #167 (`judge_v3`), #169 (đo token Bedrock và dự toán chi phí), #179, #181, #184, #185; approve #170 <br> - Đóng các issue AI đã xong, trong đó có #56 (M1) và #165 | 25/11/2026 | 25/11/2026 | [#184](https://github.com/ask-first-fcaj/ask-first/pull/184), [#185](https://github.com/ask-first-fcaj/ask-first/pull/185), [#186](https://github.com/ask-first-fcaj/ask-first/pull/186), [#187](https://github.com/ask-first-fcaj/ask-first/pull/187), [#188](https://github.com/ask-first-fcaj/ask-first/pull/188), [lambda-p95.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md), [fcj-workshop-template](https://github.com/thienluhoan/fcj-workshop-template) |
| 5 | - Merge #186 (hướng dẫn dọn tài nguyên), #187 và #188 (rà bảo mật lane A, B), #192 (sửa LB-01, LB-02, LB-06 của lane B), #190 (báo người cao tuổi khi người thân bấm "Tôi đã liên hệ", FR13), #191 (file tóm tắt và sơ đồ kiến trúc nộp FCAJ Submission) <br> - Chặn ảnh lớn hơn 20 MB và trả lời "chưa hỗ trợ" cho tin thoại (BE-34, #193) | 26/11/2026 | 26/11/2026 | [#186](https://github.com/ask-first-fcaj/ask-first/pull/186), [#187](https://github.com/ask-first-fcaj/ask-first/pull/187), [#188](https://github.com/ask-first-fcaj/ask-first/pull/188), [#190](https://github.com/ask-first-fcaj/ask-first/pull/190), [#191](https://github.com/ask-first-fcaj/ask-first/pull/191), [#192](https://github.com/ask-first-fcaj/ask-first/pull/192), [#193](https://github.com/ask-first-fcaj/ask-first/pull/193) |
| 6 | - Sửa các nút trên bot: "Việc cần làm ngay" và nhãn `LOW_RISK` trong `/lichsu` (#194); "Báo người nhà", "Gọi người nhà", trả lời câu hỏi thêm (#195) <br> - Tìm lỗi nút "Báo người thân" báo chưa liên kết dù đã thêm người thân: webhook không lưu chat id của người thân khi kết nối. Sửa, thêm test (#196), deploy lên `dev` và chạy warm-up <br> - Đặt budget action cho tài khoản Bedrock: chi phí thực vượt 10 USD/tháng thì tự gắn policy chặn `bedrock:*` vào role mà Lambda `ai` dùng <br> - Chuẩn bị kịch bản quay video demo; điền 3 bài blog của nhóm vào site báo cáo | 27/11/2026 | 27/11/2026 | [#194](https://github.com/ask-first-fcaj/ask-first/pull/194), [#195](https://github.com/ask-first-fcaj/ask-first/pull/195), [#196](https://github.com/ask-first-fcaj/ask-first/pull/196), [Budget actions](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-controls.html) |

### Kết quả đạt được

* Eval cuối (`judge_v3`, 24/11, 3 lượt): recall 100%, báo nhầm 5% / 5% / 5%, đúng mức nhãn 99,3%. Vẫn chỉ có 134 mẫu và 0 mẫu thật ([eval-report-final.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-final.md)).
* Trên Lambda `ai` (tài khoản team): tin chữ khi container ấm p95 3,5 s (100 câu), RAG p95 45 ms, cold start init p50 6,2 s, tối đa 8,5 s ([lambda-p95.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md)).
* Tin ảnh đầu-cuối p95 17,0 s (21 ảnh), vượt mốc 15 s; tôi chấp nhận vì vẫn dưới ngưỡng khoảng 30 s cho ảnh. RapidOCR trên Lambda có p95 5,07 s, vượt mục tiêu 3 s của ADR-003; bật VietOCR thêm p95 3,42 s mỗi ảnh ([lambda-p95.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/lambda-p95.md), [ocr-vietocr.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/ocr-vietocr.md)).
* Dự toán chi phí cho 3.000 lượt kiểm tra mỗi tháng: khoảng 6,2 USD/tháng nếu không có Free Tier, khoảng 2,4 USD/tháng nếu còn Free Tier (chưa kiểm tài khoản còn Free Tier hay không) ([cost-estimate.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/cost-estimate.md)).
* Trong tuần: 20 PR của tôi được merge (#167, #169, #177, #179–#188, #190–#196); tôi review 9 PR (7 của AI2, 2 của BE).
* Bản sửa nút người thân (#196) đã deploy lên `dev`. Các nút "Báo người nhà", "Gọi người nhà" và cảnh báo tự động cho người thân chưa được test lại trên bot sau bản sửa; sẽ test trước khi quay video demo.
* Tài khoản Bedrock có chốt chặn chi phí: vượt 10 USD/tháng thì bot tự chuyển sang chế độ chỉ chạy luật thay vì tiếp tục gọi LLM.

### Bài học rút ra

* Thử prompt mới thì đặt tiêu chí trước khi đo: `judge_v4` viết dễ đọc hơn rõ nhưng báo nhầm tăng ở 2/3 lượt, đúng cùng một mẫu, nên tôi bỏ ([eval-report-final.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-final.md)).
* Lần init đầu sau deploy của Lambda `ai` chạm mốc 10 s và phải chạy lại, nên cần warm-up ngay sau deploy (#185).
* Docker 29 đẩy image dạng OCI index kèm attestation, và Lambda không nhận loại này. Phải trỏ function tới digest của manifest `linux/amd64` (hoặc `docker push --platform linux/amd64`).
* Test chỉ kiểm `status` của bản ghi người thân nên bỏ sót lỗi thiếu `guardian_chat_id`, trong khi hàm lọc người thân của nút bấm và worker lại cần đúng field đó. Test nên kiểm field mà phía dùng dữ liệu cần (#196).
* AWS Budgets không phải giới hạn cứng: số liệu chi phí chỉ cập nhật vài lần mỗi ngày nên có thể vượt mức một chút trước khi action chạy, và sau khi chạy phải gỡ tay.
