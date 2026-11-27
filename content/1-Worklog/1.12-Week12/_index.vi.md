---
title: "Worklog Tuần 12"
date: 2026-11-30
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

Tuần 12: 30/11/2026 – 04/12/2026. Workshop đã nộp trước hạn 30/11/2026, nên tuần này tôi lên văn phòng tự học, rà lại dự án và dọn dẹp những phần còn thừa.

### Mục tiêu tuần 12

* Tự học tại văn phòng: ôn lại các dịch vụ AWS đã dùng trong dự án và đọc thêm về thiết kế serverless.
* Rà lại toàn bộ dự án sau khi nộp: code, tài liệu, site báo cáo.
* Dọn dẹp: nhánh cũ, image và log không còn dùng, kiểm chi phí AWS.

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Rà lại site báo cáo sau khi nộp: đọc lại worklog, proposal, workshop ở cả hai ngôn ngữ, sửa câu chữ và link lỗi | 30/11/2026 | 30/11/2026 | [Site báo cáo](https://thiendt1608.github.io/fcaj-workshop/) |
| 3 | - Rà lại repo dự án: đối chiếu README, ADR và tài liệu trong `docs/` với code hiện tại, ghi lại chỗ lệch thành issue <br> - Xoá các nhánh đã merge ở local và trên GitHub | 01/12/2026 | 01/12/2026 | [ask-first](https://github.com/ask-first-fcaj/ask-first), [docs/](https://github.com/ask-first-fcaj/ask-first/tree/main/docs) |
| 4 | - Tự học: đọc AWS Well-Architected, tập trung trụ cột Security và Cost Optimization, đối chiếu với kiến trúc Ask First | 02/12/2026 | 02/12/2026 | [AWS Well-Architected](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html) |
| 5 | - Tự học: đọc Serverless Applications Lens (Lambda, API Gateway, DynamoDB) <br> - Dọn tài nguyên thừa: image ECR cũ của Lambda `ai`, log group không còn dùng; xem lại Cost Explorer và Budgets | 03/12/2026 | 03/12/2026 | [Serverless Lens](https://docs.aws.amazon.com/wellarchitected/latest/serverless-applications-lens/welcome.html), [ECR lifecycle policies](https://docs.aws.amazon.com/AmazonECR/latest/userguide/LifecyclePolicies.html), [CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html), [Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html) |
| 6 | - Tổng kết dự án với team: việc đã xong, việc hoãn (voice, VLM, multi-turn), việc cần làm nếu tiếp tục <br> - Thống nhất thời điểm xoá stack `dev` theo hướng dẫn SH-10 khi không cần demo cho chấm điểm nữa | 04/12/2026 | 04/12/2026 | [#186](https://github.com/ask-first-fcaj/ask-first/pull/186), [eval-report-final.md](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/eval-report-final.md) |

### Kết quả đạt được

* Site báo cáo và repo dự án được rà lại sau khi nộp; các chỗ tài liệu lệch với code được ghi thành issue.
* Repo gọn hơn: nhánh đã merge được xoá, image ECR và log group thừa được dọn.
* Hiểu rõ hơn kiến trúc serverless của dự án qua Well-Architected và Serverless Lens, nhất là phần bảo mật và chi phí.
* Stack `dev` vẫn giữ để bot chạy trong kỳ chấm; việc xoá stack làm theo hướng dẫn SH-10 khi team thống nhất.

### Bài học rút ra

* Rà lại sau khi nộp vẫn có ích: tài liệu viết trong lúc gấp dễ lệch với code, và đọc lại với đầu óc thoải mái thì dễ thấy hơn.
* Dọn dẹp nên làm dần trong lúc làm dự án (lifecycle policy cho ECR, retention cho log) thay vì để dồn đến cuối.
