---
title: "Blog 1"
date: 2026-11-25
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---


# Test luồng Serverless & Event-Driven (SQS + DLQ + DynamoDB) không tốn $0 trên local với LocalStack + Java 21

**Link bài đăng:** [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj/posts/2349976352433990/)

**Tác giả:** Ngô Thị Kim Ngân · **Ngày đăng:** 27/11/2026

### Tóm tắt
Bài chia sẻ cách test luồng bất đồng bộ (sự kiện → SQS → Lambda Java 21 → DynamoDB / SSM Parameter Store, có DLQ) ngay trên máy bằng LocalStack trước khi deploy bằng AWS SAM. Mục tiêu là sửa các lỗi nhỏ ở local thay vì sửa trên AWS, vừa nhanh hơn vừa tránh phát sinh chi phí.

### Điểm chính
- Trong AWS SDK v2 cho Java, chỉ gọi `endpointOverride` trỏ về `http://localhost:4566` khi chạy chế độ `local`. Lên cloud thì dùng endpoint mặc định.
- Test cả hai luồng: payload hợp lệ phải ghi được vào DynamoDB và bị xoá khỏi queue chính; payload lỗi phải rơi vào DLQ sau đúng số lần retry đã cấu hình.
- Chỉ deploy bằng SAM khi logic và cấu hình đã chạy ổn ở local.
