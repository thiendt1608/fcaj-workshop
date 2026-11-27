---
title: "Các bài blogs đã đăng"
date: 2026-11-25
weight: 3
chapter: false
pre: " <b> 3. </b> "
---


Ba bài blog chung của nhóm đã đăng trên [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj) (yêu cầu FCAJ: mỗi nhóm 3 bài, thiếu 1 bài trừ 0.1).

### [Blog 1 — Test luồng Serverless & Event-Driven (SQS + DLQ + DynamoDB) không tốn $0 trên local với LocalStack + Java 21](3.1-blog1/)
Giả lập SQS, DLQ, DynamoDB và SSM bằng LocalStack để test cả luồng chạy đúng lẫn luồng rơi vào DLQ của Lambda Java 21 ngay trên máy, rồi mới deploy bằng AWS SAM.

### [Blog 2 — Lambda + EventBridge: Cách mình dùng Event-driven Architecture để tự cập nhật data hằng ngày](3.2-blog2/)
Dựng trang tĩnh tự cập nhật bằng EventBridge Scheduler, Lambda collector, sự kiện S3 qua EventBridge và Lambda renderer. Mỗi phần chạy và lỗi độc lập, renderer idempotent và chỉ bắt sự kiện trong prefix `data/` để tránh vòng lặp.

### [Blog 3 — Thiết kế luồng dữ liệu IoT (MQTT → AWS IoT Core → Glue → S3) tối ưu chi phí cho đồ án quy mô nhỏ](3.3-blog3/)
Ghi dữ liệu cảm biến thẳng từ IoT Core xuống S3 theo phân vùng thời gian, gộp mỗi ngày thành Parquet bằng Glue và tự xoá file thô sau 7 ngày, để tránh hàng triệu file JSON nhỏ.
