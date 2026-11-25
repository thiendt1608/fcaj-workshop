---
title: "Worklog Tuần 3"
date: 2026-09-28
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

Tuần 3: 28/09/2026 – 02/10/2026.
<!-- nháp: tự điền theo lịch học FCAJ, repo dự án chưa có bằng chứng cho tuần này (commit đầu tiên 18/10/2026); sửa lại cho đúng với việc đã làm trước khi nộp -->

### Mục tiêu tuần 3

* Nắm S3, RDS và DynamoDB, biết khi nào chọn loại nào.
* Làm quen các khối serverless: Lambda, SQS, CloudWatch.

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu S3: bucket, storage class, versioning, lifecycle <br> - Host một trang web tĩnh trên S3 | 28/09/2026 | 28/09/2026 | [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html), [S3 static website](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html) |
| 3 | - So sánh RDS và DynamoDB <br> - Tạo bảng DynamoDB on-demand, thử thiết kế khoá `PK`/`SK` theo câu truy vấn | 29/09/2026 | 29/09/2026 | [Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html), [DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html) |
| 4 | - Tìm hiểu Lambda: handler, timeout, memory, cold start <br> - Viết function Python nhỏ, xem log trên CloudWatch | 30/09/2026 | 30/09/2026 | [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html), [CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) |
| 5 | - Tìm hiểu SQS: visibility timeout, dead-letter queue <br> - Nối SQS vào Lambda làm event source | 01/10/2026 | 01/10/2026 | [Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html) |
| 6 | - Tìm hiểu CloudWatch metric, alarm và log group <br> - Đặt alarm khi DLQ có message, dọn tài nguyên của tuần | 02/10/2026 | 02/10/2026 | [CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) |

### Kết quả đạt được

* Dựng được luồng SQS → Lambda có DLQ và alarm, log xem được trên CloudWatch.
* Biết thiết kế khoá DynamoDB theo câu truy vấn.

### Bài học rút ra

* Với DynamoDB phải liệt kê câu truy vấn trước rồi mới chọn khoá; làm ngược lại thì phải thêm index hoặc scan cả bảng.
