---
title: "Worklog Tuần 8"
date: 2026-11-02
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

Tuần 8: 02/11/2026 – 06/11/2026 (tuần W3 của dự án, mốc M1 "Text E2E" hạn 06/11).

### Mục tiêu tuần 8

* Ghép đủ các stage của pipeline văn bản (fuse, respond, cascade) thành `RealPipeline` (INT-01).
* Đưa OCR vào code pipeline (AI-26) và mở link rút gọn an toàn (AI-24b).
* Review các PR của AI2 nằm trên đường găng của M1 (rules engine, URL intel, tải media).

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | <!-- TODO: fill in --> | 02/11/2026 | <!-- TODO --> | <!-- TODO --> |
| 3 | - Viết stage fuse gộp điểm luật và điểm LLM thành `Verdict` (AI-05, #119) <br> - Viết stage respond dựng tin trả lời 4 mức và cảnh báo người thân (AI-06, #120) <br> - Cascade judge sang model dự phòng và chế độ chỉ chạy luật; respond báo bảo trì khi LLM lỗi (AI-07, #122, #123) <br> - Nối `RealPipeline` cho văn bản từ mask tới respond (INT-01, #124) <br> - OCR local bằng RapidOCR, sửa URL, decode QR (AI-26, #126); mở link rút gọn an toàn, chống SSRF (AI-24b, #127) <br> - Merge #117 (thẻ KB) <br> - Review PR của AI2: sửa mask OTP (#118, request changes rồi approve), tải media Telegram + `S3ArtifactStore` (#121), giới hạn thời gian tải media (#125), chuẩn hoá văn bản + rules engine v1 (#128), URL intel không gọi mạng (#129) <br> - Đóng issue AI-05 (#51), AI-06 (#55), AI-07 (#67) | 03/11/2026 | 03/11/2026 | [#119](https://github.com/ask-first-fcaj/ask-first/pull/119), [#120](https://github.com/ask-first-fcaj/ask-first/pull/120), [#122](https://github.com/ask-first-fcaj/ask-first/pull/122), [#123](https://github.com/ask-first-fcaj/ask-first/pull/123), [#124](https://github.com/ask-first-fcaj/ask-first/pull/124), [#126](https://github.com/ask-first-fcaj/ask-first/pull/126), [#127](https://github.com/ask-first-fcaj/ask-first/pull/127), [#118](https://github.com/ask-first-fcaj/ask-first/pull/118), [#121](https://github.com/ask-first-fcaj/ask-first/pull/121), [#125](https://github.com/ask-first-fcaj/ask-first/pull/125), [#128](https://github.com/ask-first-fcaj/ask-first/pull/128), [#129](https://github.com/ask-first-fcaj/ask-first/pull/129) |
| 4 | <!-- TODO: fill in --> | 04/11/2026 | <!-- TODO --> | <!-- TODO --> |
| 5 | <!-- TODO: fill in --> | 05/11/2026 | <!-- TODO --> | <!-- TODO --> |
| 6 | <!-- TODO: fill in --> | 06/11/2026 | <!-- TODO --> | <!-- TODO --> |

### Kết quả đạt được

* Pipeline văn bản đã ghép đủ trong code: chuẩn hoá → mask PII → luật + URL intel → judge → fuse → respond, có chế độ chỉ chạy luật khi LLM lỗi.
* OCR nằm trong code pipeline. Chạy trên CI với model production: chép đúng URL 31/32 (97%) trên 50 ảnh, 17/18 (94%) trên 16 ảnh độc lập ([ADR-003](https://github.com/ask-first-fcaj/ask-first/blob/main/docs/decisions/ADR-003-ocr-rapidocr.md), điều kiện 3).
* 8 PR của tôi được merge trong ngày 03/11; tôi review 5 PR của AI2.
* <!-- TODO(not verified): mốc M1 (demo text E2E trên bot dev, hạn 06/11) có đạt đúng hạn không; issue INT-01 (#56) chỉ đóng ngày 25/11 -->

### Bài học rút ra

* <!-- TODO: chỉ điền khi có bài học thật -->
