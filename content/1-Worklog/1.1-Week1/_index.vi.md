---
title: "Worklog Tuần 1"
date: 2026-09-14
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

Tuần 1: 14/09/2026 – 18/09/2026.
<!-- nháp: tự điền theo lịch học FCAJ, repo dự án chưa có bằng chứng cho tuần này (commit đầu tiên 18/10/2026); sửa lại cho đúng với việc đã làm trước khi nộp -->

### Mục tiêu tuần 1

* Nắm nội quy, lịch học và cách nộp bài của chương trình FCAJ.
* Tạo và bảo vệ tài khoản AWS cá nhân: MFA, IAM user, AWS Budgets.
* Nắm tổng quan dịch vụ AWS và hạ tầng toàn cầu (Region, Availability Zone).

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Dự buổi onboarding FCAJ, đọc nội quy và cách chấm điểm <br> - Làm quen mentor và các bạn cùng đợt | 14/09/2026 | 14/09/2026 | [Rules FCAJ](https://hcm-rules.awsfcaj.com/) |
| 3 | - Tạo tài khoản AWS, bật MFA cho root <br> - Tạo IAM user và group cho việc hằng ngày, không dùng root | 15/09/2026 | 15/09/2026 | [IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html), [MFA](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html) |
| 4 | - Đặt AWS Budgets để nhận cảnh báo chi phí <br> - Tìm hiểu Billing và Free Tier | 16/09/2026 | 16/09/2026 | [AWS Budgets](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html) |
| 5 | - Đọc tổng quan AWS: Region, Availability Zone, edge location, mô hình trách nhiệm chung (shared responsibility) | 17/09/2026 | 17/09/2026 | [Overview of AWS](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/introduction.html) |
| 6 | - Cài AWS CLI, cấu hình named profile cho IAM user <br> - Thử các lệnh chỉ đọc như `aws sts get-caller-identity`, `aws s3 ls` | 18/09/2026 | 18/09/2026 | [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) |

### Kết quả đạt được

* Tài khoản AWS cá nhân có MFA, IAM user riêng và budget cảnh báo chi phí.
* Gọi được AWS CLI bằng profile riêng, không dùng access key của root.

### Bài học rút ra

* Đặt budget và bật MFA ngay ngày đầu rẻ hơn nhiều so với xử lý một hoá đơn bất ngờ hay một tài khoản bị lộ.
