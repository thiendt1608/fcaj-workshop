---
title: "Blog 2"
date: 2026-11-25
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---


# Lambda + EventBridge: Cách mình dùng Event-driven Architecture để tự cập nhật data hằng ngày

**Link bài đăng:** [AWS Study Group](https://www.facebook.com/groups/660548818043427/posts/2345717436193215/)

**Tác giả:** Võ Đức Trí · **Ngày đăng:** 27/11/2026

### Tóm tắt
Bài dựng một trang tĩnh tự cập nhật giá xăng, giá vàng, tỉ số esport và thời tiết, thay cho việc mở từng tab mỗi sáng. EventBridge Scheduler gọi một Lambda collector theo nhịp riêng của từng nguồn; collector ghi JSON vào S3; sự kiện S3 qua EventBridge gọi Lambda renderer dựng lại `index.html` rồi invalidate CloudFront. Các phần không gọi trực tiếp nhau nên một nguồn lỗi không làm hỏng cả trang.

### Điểm chính
- Một collector dùng chung, mỗi schedule gửi payload tên nguồn riêng. Scheduler cho chọn múi giờ, có retry policy và DLQ.
- Collector chuẩn hoá dữ liệu thành `data/<nguồn>.json`. Nguồn lỗi thì báo lỗi và không ghi đè file cũ, nên trang vẫn giữ dữ liệu lần trước.
- Rule EventBridge phải lọc prefix `data/`. Không lọc thì `index.html` do renderer ghi sẽ sinh sự kiện và gọi lại renderer mãi.
- S3 giao sự kiện at-least-once, nên renderer phải idempotent: luôn dựng lại cả trang từ dữ liệu hiện có rồi ghi đè.
- Mỗi mục hiện `updatedAt` để người xem biết số liệu nào đã cũ.
