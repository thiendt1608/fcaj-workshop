---
title: "Blog 3"
date: 2026-11-25
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---


# Thiết kế luồng dữ liệu IoT (MQTT → AWS IoT Core → Glue → S3) tối ưu chi phí cho đồ án quy mô nhỏ

**Link bài đăng:** [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2351133745651584/)

**Tác giả:** Ngô Thị Kim Ngân · **Ngày đăng:** 27/11/2026

### Tóm tắt
Bài nói về chuyện thiết bị IoT gửi JSON nhỏ liên tục khiến S3 dồn hàng triệu file, làm phí request cao và query bằng Athena chậm. Giải pháp serverless chia hai giai đoạn: IoT Core Rule Engine ghi thẳng dữ liệu thô vào S3 theo phân vùng thời gian, rồi mỗi ngày một Glue job gộp file và chuyển sang Parquet. File thô tự xoá sau 7 ngày bằng S3 Lifecycle.

### Điểm chính
- Dùng IoT Core Rule Engine (lọc bằng SQL) ghi thẳng xuống S3 theo đường dẫn `raw-data/year=YYYY/month=MM/day=DD/hour=HH/...`, không cần EC2 hay Lambda trung gian.
- EventBridge Scheduler chạy Glue job cuối ngày: đọc JSON hôm trước, làm sạch kiểu dữ liệu, gộp lại và xuất Parquet nén Snappy sang bucket curated.
- S3 Lifecycle xoá file JSON thô sau 7 ngày, vẫn đủ để debug.
- Theo ước tính trong bài (10 triệu bản tin/tháng): dung lượng giảm khoảng 85%, số file từ 10 triệu còn khoảng 30, dữ liệu Athena phải quét khi đọc 1 cột giảm khoảng 98%.
- Dữ liệu nhỏ thì dùng Glue Python Shell với `awswrangler` thay vì Spark. Dùng Spark thì chọn worker G.1X, tối đa 2 worker.
