---
title: "Nhật ký công việc"
date: 2026-09-14
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

Đây là nhật ký công việc cá nhân của tôi trong 12 tuần thực tập. Tôi giữ vai trò AI1 trong dự án nhóm **Hỏi Trước (Ask First)**, một bot giúp người dùng hỏi trước khi làm theo tin nhắn đáng ngờ. Phần việc chính của tôi là pipeline AI trên AWS Lambda: luật, RAG, judge dùng Amazon Bedrock, OCR cho ảnh, bộ eval và các ADR.

Mỗi tuần ghi theo ngày, kèm link PR, issue hoặc tài liệu trong repo [ask-first-fcaj/ask-first](https://github.com/ask-first-fcaj/ask-first). Tuần 1–5 (trước khi có repo dự án) chưa có PR để link, nên nguồn là tài liệu AWS và tài liệu dự án; tuần 12 là kế hoạch.
<!-- TODO(confirm): có cần ghi rõ việc dùng trợ lý AI (Claude Code) khi viết code và tài liệu không (xem bao-cao-claude-w2.md, kiểm quy định của FCAJ) -->

**Tuần 1:** [Làm quen FCAJ, tài khoản AWS và tổng quan cloud](1.1-week1/)

**Tuần 2:** [IAM, VPC và EC2](1.2-week2/)

**Tuần 3:** [Lưu trữ, cơ sở dữ liệu và serverless](1.3-week3/)

**Tuần 4:** [Lập nhóm, chọn đề tài Hỏi Trước và tìm hiểu Bedrock](1.4-week4/)

**Tuần 5:** [Research concept, kiến trúc hệ thống và hợp đồng BE ↔ AI](1.5-week5/)

**Tuần 6:** [Chốt plan 6 tuần, contract AI v1.0, bộ eval v0 và spike OCR](1.6-week6/)

**Tuần 7:** [Qua Gate G2, dựng khung pipeline AI và chấm URL](1.7-week7/)

**Tuần 8:** [Pipeline xử lý tin chữ chạy trong code, đo lại OCR](1.8-week8/)

**Tuần 9:** [Chọn Bedrock Qwen3 32B (ADR-001), judge, Guardrails và red-team](1.9-week9/)

**Tuần 10:** [Hiệu chỉnh ngưỡng fuse (ADR-005), báo cáo eval Gate M4, đo token cho dự toán chi phí](1.10-week10/)

**Tuần 11:** [Eval cuối với `judge_v3`, đưa Lambda `ai` lên AWS, đo độ trễ và dự toán chi phí](1.11-week11/)

**Tuần 12:** [Dọn tài nguyên và tổng kết dự án (kế hoạch)](1.12-week12/)
