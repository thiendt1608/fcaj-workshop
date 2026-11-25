---
title: "Worklog Tuần 10"
date: 2026-11-16
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

Tuần 10: 16/11/2026 – 20/11/2026 (tuần W5 của dự án, mốc M4 MVP freeze hạn 20/11).

### Mục tiêu tuần 10

* Hiệu chỉnh ngưỡng mức của fuse (AI-10) và viết báo cáo eval cho Gate M4 (AI-13).
* Giảm báo nhầm còn lại của judge (luật mới, prompt `judge_v3`).
* Thêm việc cho Workshop FCAJ vào plan: dự toán chi phí (SH-09), dọn tài nguyên (SH-10), mốc M7; bắt đầu đo token Bedrock cho SH-09.

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Merge #150: điểm chấm tay tiếng Việt cho Gate G1 và kiểm chéo bằng chấm mù, ghi vào ADR-002 (AI-03, đóng issue #38) <br> - Review PR đầu vào không hỗ trợ và tệp `.apk` của BE (BE-35) | 16/11/2026 | 16/11/2026 | [#150](https://github.com/ask-first-fcaj/ask-first/pull/150), [#154](https://github.com/ask-first-fcaj/ask-first/pull/154), [ADR-002](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-002-g1-judge-model.md) |
| 3 | - Thêm SH-09 (AWS Pricing Calculator), SH-10 (clean-up) và mốc M7 nộp Workshop FCAJ vào plan (#161) <br> - Hiệu chỉnh ngưỡng fuse thành 70/60/25 trên 134 mẫu, ghi ADR-005 (AI-10, #163) <br> - Viết báo cáo eval Gate M4, tách số theo nguồn mẫu (AI-13, #164) <br> - Thêm luật cứng `R_OVERPAY_REFUND` cho tin báo chuyển dư rồi xin chuyển lại (#166, issue #140) <br> - Mở #167 (prompt `judge_v3`, sửa báo nhầm giấy mời công an, issue #165) và #169 (đo token Bedrock mỗi lượt kiểm tra cho SH-09); cả hai merge 25/11 <br> - Review và merge PR của AI2: bộ đo mask PII 500 mẫu (AI-31, #158), đo luật OTP (AI-30, #159), xác minh lại 20 hotline (SH-05, #168); comment PR câu chữ tin bot (SH-08, #157) <br> - Review PR của BE: SQS worker (BE-07, #148), feedback "Bot báo sai" (BE-30, #155), rút đồng ý và xoá dữ liệu (BE-31, #156) | 17/11/2026 | 17/11/2026 | [#161](https://github.com/ask-first-fcaj/ask-first/pull/161), [#163](https://github.com/ask-first-fcaj/ask-first/pull/163), [#164](https://github.com/ask-first-fcaj/ask-first/pull/164), [#166](https://github.com/ask-first-fcaj/ask-first/pull/166), [#167](https://github.com/ask-first-fcaj/ask-first/pull/167), [#169](https://github.com/ask-first-fcaj/ask-first/pull/169), [#158](https://github.com/ask-first-fcaj/ask-first/pull/158), [#159](https://github.com/ask-first-fcaj/ask-first/pull/159), [#168](https://github.com/ask-first-fcaj/ask-first/pull/168), [#157](https://github.com/ask-first-fcaj/ask-first/pull/157), [#148](https://github.com/ask-first-fcaj/ask-first/pull/148), [#155](https://github.com/ask-first-fcaj/ask-first/pull/155), [#156](https://github.com/ask-first-fcaj/ask-first/pull/156), [ADR-005](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-005-nguong-muc-fuse.md), [eval-report-m4.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-m4.md) |
| 4 | - Lọc các mẫu còn báo nhầm sau báo cáo M4 để làm prompt `judge_v3`; thử prompt trên các mẫu đó và trên 10 tin held-out <!-- nháp: tự điền, không có bằng chứng trên git; sửa lại cho đúng trước khi nộp --> | 18/11/2026 | 18/11/2026 | [#167](https://github.com/ask-first-fcaj/ask-first/pull/167), [eval-report-m4.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-m4.md) |
| 5 | - Giao phần nối AWS (S3, DynamoDB, SSM, Telegram) của INT-01 cho AI2, vì tôi không có quyền AWS ngoài Bedrock; AI2 mở #173 cùng ngày <br> - Tìm hiểu AWS Pricing Calculator cho SH-09 <!-- nháp: tự điền, không có bằng chứng trên git; sửa lại cho đúng trước khi nộp --> | 19/11/2026 | 19/11/2026 | [#56](https://github.com/ask-first-fcaj/ask-first/issues/56), [#173](https://github.com/ask-first-fcaj/ask-first/pull/173) |
| 6 | - Mốc M4 (MVP freeze): rà các issue AI còn mở, ghi việc nào xong và việc nào dời sang tuần sau <br> - Soạn nháp bảng giả định tải (3.000 lượt/tháng) cho dự toán chi phí <!-- nháp: tự điền, không có bằng chứng trên git; sửa lại cho đúng trước khi nộp --> | 20/11/2026 | 20/11/2026 | [cost-estimate.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/cost-estimate.md) |

### Kết quả đạt được

* Báo cáo Gate M4 (17/11, `judge_v2`, 3 lượt): recall 100% (90/90 tin lừa đảo), báo nhầm 5% / 10% / 10% trên 20 tin lành tính dễ nhầm. Chỉ có 134 mẫu có nhãn thay vì 300 và chưa có mẫu thật nào, nên tiêu chí "trên 300 mẫu" của M4 **chưa đạt** ([eval-report-m4.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-m4.md)).
* Ngưỡng fuse 70/60/25 cho đường có LLM, giữ 75/50/25 cho đường chỉ luật ([ADR-005](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-005-nguong-muc-fuse.md)).
* Đo được chi phí token LLM khoảng 0,000753 USD mỗi lượt kiểm tra tin chữ (`judge_v2`, 133 lượt) để làm dự toán SH-09 ([cost-estimate.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/results/cost-estimate.md), mục 1).
* 5 PR của tôi được merge trong tuần; tôi review 8 PR (4 của AI2, 4 của BE).

### Bài học rút ra

* Chọn ngưỡng không chỉ nhìn số tổng: ngưỡng D = 60 cho đúng mức cao nhất ở 1 lượt nhưng làm 3 tin lành bị `DANGEROUS` và báo nhầm cho người thân. Script không chấm tiêu chí này nên tôi chọn tay 70/60/25 ([ADR-005](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-005-nguong-muc-fuse.md)).
* Đổi ngưỡng cho cả đường chỉ luật làm gate eval trên CI fail (recall chỉ luật tụt 22,2% → 17,8%), nên tôi tách ngưỡng theo đường xử lý.
