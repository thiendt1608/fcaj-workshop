---
title: "Worklog Tuần 2"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

Tuần 2: 21/09/2026 – 25/09/2026.
<!-- nháp: tự điền theo lịch học FCAJ, repo dự án chưa có bằng chứng cho tuần này (commit đầu tiên 18/10/2026); sửa lại cho đúng với việc đã làm trước khi nộp -->

### Mục tiêu tuần 2

* Hiểu IAM policy, role và nguyên tắc quyền tối thiểu (least privilege).
* Tự dựng VPC có subnet public/private và chạy EC2 trong đó.
* Dọn sạch tài nguyên sau mỗi bài lab.

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Đọc cấu trúc IAM policy JSON, phân biệt user, group, role <br> - Viết thử policy chỉ cho đọc một bucket S3 | 21/09/2026 | 21/09/2026 | [IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) |
| 3 | - Dựng VPC trên 2 AZ: subnet public/private, route table, Internet Gateway | 22/09/2026 | 22/09/2026 | [Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html) |
| 4 | - So sánh Security Group và Network ACL <br> - Chạy EC2 trong subnet public, SSH bằng key pair | 23/09/2026 | 23/09/2026 | [Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html), [Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) |
| 5 | - Tìm hiểu instance type, EBS, AMI, user data <br> - Gắn IAM role cho EC2 thay vì để access key trên máy | 24/09/2026 | 24/09/2026 | [Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) |
| 6 | - Ôn tập tuần, xoá EC2, NAT Gateway và VPC đã tạo <br> - Ghi chú các khoản tính tiền theo giờ dù không dùng (NAT Gateway, Elastic IP không gắn) | 25/09/2026 | 25/09/2026 | [Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html), [AWS Budgets](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html) |

### Kết quả đạt được

* Dựng được VPC nhiều AZ và chạy EC2 trong đó, rồi dọn sạch sau lab.
* Gắn quyền cho EC2 bằng IAM role, không để access key trên máy.

### Bài học rút ra

* NAT Gateway tính tiền theo giờ kể cả khi không có traffic. Điều này về sau là một lý do dự án chọn không dùng VPC cho Lambda ([infra/README.md](https://github.com/ask-first-fcaj/ask-first/blob/main/infra/README.md), quy tắc D1).
