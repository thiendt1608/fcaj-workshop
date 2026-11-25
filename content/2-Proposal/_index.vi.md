---
title: "Bản đề xuất"
date: 2026-11-25
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Ask First ("Hỏi Trước")
## Trợ lý Telegram serverless giúp người cao tuổi kiểm tra tin nhắn nghi lừa đảo trước khi làm theo

### 1. Tổng quan

**Hỏi Trước** là bot Telegram dành cho người cao tuổi ở Việt Nam. Khi nhận được tin nhắn, link hay ảnh chụp màn hình đáng ngờ, người dùng chuyển tiếp cho bot. Bot trả lời bằng tiếng Việt dễ hiểu, gồm:

- một trong **4 mức rủi ro**: `DANGEROUS`, `SUSPICIOUS`, `UNSURE`, `LOW_RISK` (cố ý không có mức "an toàn"),
- kịch bản lừa đảo mà tin giống, các dấu hiệu trích từ chính tin, việc cần làm ngay và số tổng đài chính thức đã xác minh,
- **cảnh báo người thân** (nếu đã liên kết với sự đồng ý của người cao tuổi) khi rủi ro cao.

Bot đóng vai "người thứ ba" xen vào trước khi tiền rời tài khoản. Toàn bộ hệ thống chạy serverless trên AWS: các Lambda Java 21 lo phần Telegram và điều phối, một Lambda Python 3.12 chạy pipeline AI (OCR local, luật, RAG local), và chỉ bước "judge" cuối cùng mới gọi LLM open-weight trên Amazon Bedrock với **văn bản đã che thông tin cá nhân**.

| Mục | Giá trị |
|---|---|
| Nhóm | 4 người: 2 backend (Java 21), 2 AI (Python 3.12) |
| Thời gian | 6 tuần, 19/10/2026 – 29/11/2026 (bootcamp FCAJ) |
| Region | Compute ở `ap-southeast-1` (Singapore); Bedrock ở `ap-southeast-2` (Sydney) |
| Kênh | Telegram Bot API (webhook) |
| Chi phí ước tính | ≈ 6,2 USD/tháng cho 3.000 lượt kiểm tra nếu không có Free Tier, ≈ 2,4 USD/tháng nếu hưởng các mức always-free (mục 6) |

### 2. Vấn đề cần giải quyết

**Lừa đảo trực tuyến ở Việt Nam có quy mô lớn, và người cao tuổi là đối tượng bị nhắm nhiều.**

- Bộ Công an ước tính thiệt hại do lừa đảo trực tuyến năm 2025 là hơn 8.000 tỷ đồng ([VOV](https://vov.vn/phap-luat/thiet-hai-tu-lua-dao-truc-tuyen-len-toi-8000-ty-dong-trong-nam-2025-post1286479.vov)).
- Theo khảo sát năm 2025 của Hiệp hội An ninh mạng Quốc gia (NCA), mạo danh công an là hình thức phổ biến nhất, và chỉ 32,12% nạn nhân trình báo ([Báo Hải Phòng](https://baohaiphong.vn/cac-hinh-thuc-lua-dao-truc-tuyen-nao-pho-bien-nhat-nam-2025-532711.html), [Công Lý](https://doanhnhan.congly.vn/lua-dao-truc-tuyen-nam-2025-gay-thiet-hai-tren-6-000-ty-dong.html)).
- Công an Thái Nguyên cho biết nạn nhân của kịch bản giả danh công an "chủ yếu là người cao tuổi, người sinh sống ở vùng sâu, vùng xa" ([Bộ Công an](https://www.bocongan.gov.vn/bai-viet/kip-thoi-ngan-chan-vu-viec-gia-danh-luc-luong-cong-an-lua-dao-chiem-doat-tai-san-1782987242)).

**Khoảng trống.** Ngân hàng đã cảnh báo ở bước chuyển tiền, nhưng đòn thao túng quyết định xảy ra sớm hơn: kẻ gian cô lập nạn nhân ("không được trao đổi với ai"). Trong các vụ được ngăn kịp, luôn có một người thứ ba (giao dịch viên, nhân viên bưu điện, công an xã) xen vào trước khi chuyển tiền. Người cao tuổi chưa có công cụ đơn giản để **chuyển tiếp ảnh chụp hay link và nhận lời giải thích dễ hiểu**, trong khi con cháu thường không ở bên cạnh.

**Người dùng mục tiêu**

| Người dùng | Nhu cầu |
|---|---|
| Người cao tuổi (chính) | Chuyển tiếp, chụp màn hình hoặc dán tin đáng ngờ, nhận câu trả lời ngắn, rõ trong vài giây, không phải cài app |
| Người thân (phụ) | Được báo khi người cao tuổi đã liên kết nhận tin rủi ro cao, có một nút "Tôi đã liên hệ" |

### 3. Mục tiêu và phạm vi

**Tiêu chí thành công** (đặt trước khi build; nguồn: tài liệu kiến trúc §1.3, kế hoạch §4):

| Tiêu chí | Mục tiêu |
|---|---|
| Recall tin lừa đảo | ≥ 90% |
| Báo nhầm trên tin "lành tính nhưng trông đáng ngờ" | ≤ 10% |
| Độ trễ đầu-cuối (warm) | p95 ≤ 15 giây với văn bản và ảnh |
| Chi phí | ≤ 0,005 USD mỗi lượt; ≤ 50 USD/tháng ở quy mô demo |
| Quyền riêng tư | Ảnh chỉ nằm trong RAM của Lambda `ai`; LLM chỉ thấy văn bản đã che; log không chứa nội dung tin; mọi dữ liệu người dùng có TTL |

**Trong phạm vi (MVP, 6 tuần)**

- Văn bản, link (kể cả link ẩn sau chữ), ảnh chụp màn hình và album, tin chuyển tiếp; nhiều tin gửi liền nhau được gom thành một câu trả lời.
- Kết luận 4 mức kèm kịch bản, dấu hiệu, việc cần làm, tổng đài chính thức và trích nguồn cảnh báo chính thức (RAG).
- Onboarding có đồng ý chủ động, rút đồng ý, xoá dữ liệu.
- Liên kết người thân (double opt-in), cảnh báo người thân, nhắc lại sau 15 phút, nút xác nhận "Tôi đã liên hệ".
- Quản lý ngay trong chat: `/nguoithan`, `/lichsu`, `/rutdongy`, `/xoadulieu`, `/caidat`; checklist "30 phút đầu" khi đã bị lừa (`/bilua`).
- Nút "Bot báo sai" để thu feedback; kill switch chuyển sang chế độ chỉ chạy luật mà không cần deploy; xử lý đầu vào không hỗ trợ (video, sticker, `.apk`...).

**Phần đã cắt khỏi kế hoạch ban đầu để vừa 6 tuần** (kế hoạch v3, 19/10/2026; bản đầu là 12 tuần):

| Cắt / hoãn | Lý do |
|---|---|
| Voice và nhận dạng giọng nói local (ASR) | Không đủ thời gian; bot trả lời "chưa hỗ trợ voice" |
| VLM fallback cho ảnh khó | Không đủ thời gian; OCR local đã qua gate (mục 4) |
| Hỏi thêm nhiều lượt (multi-turn) | Không đủ thời gian |
| Telegram Mini App (giao diện web) | Mọi thao tác làm trong chat bằng lệnh và nút inline |
| Phase 2: kho tri thức sống, trang "chiêu lừa đang hot" | Ngoài khung 6 tuần |
| Pilot với 10–20 hộ gia đình | Thay bằng test nội bộ với team và người nhà; **không có số liệu người dùng thật ngoài team** |

**Cố ý không làm:** chặn cuộc gọi hay chặn chuyển khoản, danh sách đen công khai số điện thoại hoặc số tài khoản, phát hiện deepfake video thời gian thực, gửi SMS, ghi âm cuộc gọi.

### 4. Kiến trúc giải pháp

![Kiến trúc Ask First trên AWS (MVP)](/images/5-Workshop/architecture.png)

**Nguyên tắc thiết kế**

1. **Serverless, event-driven, không VPC, không NAT**: tải nhỏ và không đều; trả theo lượt; tránh chi phí NAT cố định khoảng 32 USD/tháng.
2. **AI hybrid local-first**: OCR, che PII, luật và embedding chạy trên CPU trong Lambda. Chỉ bước judge gọi Bedrock, và chỉ với văn bản đã che.
3. **Workflow có kiểm soát, không phải agent**: `Normalize → Extract → Mask PII → Signals (luật + URL) → Retrieve (RAG) → Guard → Judge → Fuse → Respond`. Tầng luật chạy được khi không có LLM, nên bot luôn trả lời.
4. **Không lưu nội dung gốc**: chỉ lưu kết luận, mã dấu hiệu và hash, TTL 30 ngày.

**Dịch vụ AWS và lý do chọn**

| Dịch vụ | Vai trò | Vì sao chọn |
|---|---|---|
| **AWS Lambda**: `webhook`, `worker`, `worker-dlq` (Java 21, zip) | Nhận update Telegram qua **Function URL**, điều phối lượt kiểm tra, gửi trả lời và cảnh báo người thân | Trả theo lượt gọi; Function URL cho HTTPS endpoint mà không cần API Gateway |
| **AWS Lambda**: `ai` (Python 3.12, container image, 3008 MB) | Toàn bộ pipeline AI với model local (OCR, embedding) | Container image chứa được thư viện ML vượt giới hạn 250 MB của gói zip; ảnh chỉ xử lý trong RAM |
| **Amazon SQS** + DLQ | Hàng đợi giữa `webhook` và `worker` | Webhook trả 200 ngay; giới hạn concurrency để giữ quota Bedrock; retry miễn phí; `DelaySeconds` dùng để gom tin (3 giây) và nhắc lại sau 15 phút mà không cần scheduler |
| **Amazon DynamoDB** (on-demand, single-table) | Người dùng, đồng ý, liên kết người thân, metadata lượt kiểm tra, dedup, cache, rate limit, feature flag | Truy cập key-value, **TTL** để tối thiểu hoá dữ liệu, conditional write cho idempotency, bật PITR |
| **Amazon Bedrock**: Qwen3 32B (judge), gpt-oss-120b (dự phòng) | Phân loại có cấu trúc trên văn bản đã che | Serverless, trả theo token, không phải vận hành GPU. Model open-weight có in-region ở Sydney, Singapore thì không |
| **Amazon Bedrock Guardrails** | Filter `PROMPT_ATTACK` ở input | Phát hiện prompt injection nằm trong tin được chuyển tiếp |
| **Amazon S3** | Trọng số model local, index RAG, bộ eval, mẫu người dùng tự nguyện đóng góp (`donated/` tự xoá sau 30 ngày) | Rẻ; đổi model không cần build lại image; Block Public Access + SSE |
| **Amazon ECR** | Chứa container image của `ai` | Bắt buộc với Lambda dạng image |
| **SSM Parameter Store** (SecureString) | Bot token, webhook secret, cấu hình stack | Không để secret trong code hay biến môi trường Lambda |
| **CloudWatch** + **SNS** | Log JSON có chung `trace_id`, alarm DLQ → email cho team | Alarm cần một đích gửi; SNS chỉ dùng cho alarm nội bộ |
| **IAM** + GitHub Actions **OIDC** | Role least-privilege (role của `ai` chỉ được đụng khoá `CACHE#` / `URL#`); CI/CD không lưu access key | Bảo mật mặc định |

**Cố ý không dùng:** API Gateway (Function URL là đủ), Step Functions (pipeline tuyến tính dưới 30 giây), OpenSearch / vector DB (corpus nhỏ, tính cosine trong RAM, RAG p95 45 ms trên Lambda), EC2/SageMaker GPU (đắt hơn nhiều so với Bedrock trả theo token ở quy mô này), Amazon Transcribe (voice đã hoãn), Textract, Bedrock Agents, VPC/NAT.

**Luồng dữ liệu chính** (số bước khớp với sơ đồ)

1. Người cao tuổi chuyển tiếp tin; Telegram POST update tới Function URL của `webhook` kèm `secret_token` (kiểm bằng so sánh constant-time).
2. `webhook` dedup `update_id`, kiểm đồng ý, gom tin vào `BUFFER#`, gửi "⏳ đang kiểm tra…" và đẩy job vào SQS với delay 3 giây. Telegram nhận phản hồi trong dưới 1 giây.
3. `worker` đọc buffer và cờ kill switch, rồi invoke `ai` đồng bộ với `op=analyze` (chỉ metadata và `file_id`, không bao giờ gửi bytes ảnh).
4. `ai` tải ảnh thẳng vào RAM, chạy OCR và model local, che số điện thoại, số tài khoản, số CCCD, OTP và email, áp luật và kiểm URL, rồi truy xuất scenario card (RAG).
5. `ai` gọi Bedrock Guardrails, rồi gọi judge (Qwen3 32B; chuyển sang gpt-oss-120b khi độ tin cậy thấp hoặc lỗi), fuse điểm luật và điểm LLM thành mức, render câu trả lời tiếng Việt từ template.
6. Nếu mức là `DANGEROUS` kèm hành động nguy hiểm, `worker` báo song song cho mọi người thân đã liên kết; trạng thái "đã báo" chỉ ghi khi Telegram trả `message_id`.
7. `worker` sửa tin "⏳" thành câu trả lời cuối và ghi metadata `CHECK#` (TTL 30 ngày); hẹn nhắc lại sau 15 phút nếu chưa người thân nào xác nhận.
8. Job lỗi 5 lần thì vào DLQ → CloudWatch alarm → email qua SNS.

{{% notice note %}}
Chi tiết cấu hình trên stack `dev` đang deploy (`infra/template.yaml`): maximum concurrency với SQS là **5** cho `worker` và **2** cho `worker-dlq`; SnapStart là tham số **mặc định tắt** cho tới khi đo cold start (ADR-008); model OCR là RapidOCR PP-OCRv6 small. VietOCR được bật cùng RapidOCR từ ngày 24/11/2026.
{{% /notice %}}

### 5. Kế hoạch và tiến độ

Sáu sprint một tuần, ba track song song (backend, AI, chung), ghép với nhau qua một hợp đồng JSON Schema giữa Java và Python.

| Tuần | Mốc / gate | Nội dung | Tình trạng (theo tài liệu repo) |
|---|---|---|---|
| W1 (19–25/10) | **G0** Hợp đồng | JSON Schema BE↔AI, fixtures, contract test trong CI | Xong: contract merge 23/10 ([#89](https://github.com/ask-first-fcaj/ask-first/pull/89)), BE duyệt và đóng G0 28/10 ([#2](https://github.com/ask-first-fcaj/ask-first/issues/2)); contract test phía Java vào CI 11/11 ([#104](https://github.com/ask-first-fcaj/ask-first/pull/104)) |
| W2 (26/10–01/11) | **G1** chọn LLM · **G2** chọn OCR | Benchmark model judge; so các stack OCR trên 50 ảnh chụp thật | G2 đạt 30/10 (RapidOCR, URL 94%). G1 chốt 11/11, trễ so với kế hoạch (Qwen3 32B, ADR-002) |
| W3 (02–08/11) | **M1** Văn bản end-to-end | Tin chuyển tiếp → kết luận thật (luật + Qwen3) | Không đạt đúng hạn: code pipeline văn bản xong 03/11, gọi Bedrock thật 11/11 ([#132](https://github.com/ask-first-fcaj/ask-first/pull/132)), Lambda `ai` nối AWS 23/11 ([#173](https://github.com/ask-first-fcaj/ask-first/pull/173)), INT-01 đóng 25/11 ([#56](https://github.com/ask-first-fcaj/ask-first/issues/56)) |
| W4 (09–15/11) | **M2** Ảnh + người thân | Ảnh chụp → OCR local → kết luận; cảnh báo người thân | Luồng ảnh đo trên Lambda ngày 25/11 (xem mục 7) |
| W5 (16–22/11) | **M3** RAG · **M4** MVP freeze | Trích nguồn cảnh báo chính thức; eval gate | Báo cáo eval M4 ngày 17/11; RAG p95 45 ms trên Lambda |
| W6 (23–29/11) | **M5** test nội bộ · **M6** thuyết trình · **M7** nộp workshop | Team và người nhà dùng thử, dự toán chi phí, eval cuối, hướng dẫn clean-up | Eval cuối 24/11, dự toán chi phí 24/11 |

Sau M4, nhóm chỉ sửa bug, chỉnh prompt hoặc ngưỡng (phải qua eval gate) và làm việc hạ tầng.

### 6. Dự toán chi phí

Giả định tải: **3.000 lượt kiểm tra/tháng**, văn bản và ảnh, không có voice. Các dịch vụ ngoài Bedrock được tính bằng **[AWS Pricing Calculator](https://calculator.aws/#/estimate?id=073f413f6ac370de48bf3bff0ba10a7b870ae807)** (tạo ngày 24/11/2026, `ap-southeast-1`, chọn "Without Free Tier"). Bedrock tính tay vì Calculator không có Qwen3 ở Sydney; số token đo trên pipeline thật.

| Dịch vụ | Cách tính | Không Free Tier (USD/tháng) | Có always-free (USD/tháng) |
|---|---|---|---|
| Bedrock LLM (Qwen3 32B + dự phòng gpt-oss-120b) | 2.250 lượt gọi LLM × 0,000792 USD (đo thật, đã gồm leo thang) | 1,78 | 1,78 |
| Bedrock Guardrails | 2.250 × 0,15 USD / 1.000 text unit | 0,34 | 0,34 |
| Lambda `ai` (3008 MB) | Calculator | 0,73 | 0 |
| Lambda `worker` (512 MB) | Calculator | 0,13 | 0 |
| Lambda `webhook` (512 MB) | Calculator | 0,03 | 0 |
| SQS (jobs + DLQ) | Calculator | 0,28 | 0 |
| DynamoDB on-demand | Calculator | 0,29 | < 0,05 |
| S3 | Calculator | 0,03 | 0,03 |
| ECR (image `ai`) | Calculator | 0,20 | 0,20 |
| CloudWatch (log, metric, alarm, dashboard) | Calculator | 2,35 | 0 |
| SNS (email alarm), SSM Parameter Store (standard) | Không đưa vào Calculator | ≈ 0 | 0 |
| **Tổng** | | **≈ 6,16** | **≈ 2,4** |

- Tổng Calculator chưa gồm Bedrock: 4,04 USD/tháng (48,48 USD cho 12 tháng, 0 USD trả trước).
- Mỗi lượt kiểm tra, chỉ tính token LLM: ≈ 0,000792 USD (≈ 20 đồng), thấp hơn nhiều so với mục tiêu 0,005 USD.
- Con số 2.250 lượt gọi LLM giả định 25% lượt được trả lời bằng fast path của luật hoặc cache. Tỉ lệ này **chưa kiểm chứng**, vì bộ eval gần như toàn tin lừa đảo và tin khó. Nếu mọi lượt đều gọi LLM thì Bedrock ≈ 2,7 USD/tháng.

**Đối chiếu với số thật** (kiểm ngày 25/11/2026 trên tài khoản dev của nhóm, đều là API miễn phí)

- **Free Tier:** tài khoản vẫn hưởng mức always-free. Tháng 11 tính đến 25/11: Lambda ≈ 8.200/400.000 GB-giây và 273/1 triệu request, SQS ≈ 178.000/1 triệu request, CloudWatch alarm 0,37/10. Cột "Có always-free" đúng với tài khoản này.
- **Thời gian chạy Lambda** (CloudWatch `Duration`, 14 ngày, traffic test của nhóm): `ai` 248 lượt, TB 2,7 s, p95 9,7 s; `worker` 73 lượt, TB 4,6 s, p95 14,2 s; `webhook` 50 lượt, TB 1,0 s, p95 3,0 s. `ai` và `worker` nằm trong mức 5 s đã nhập vào Calculator. `webhook` chạy lâu gấp đôi mức 0,5 s giả định, nên dòng này lên khoảng 0,06 USD khi không có Free Tier; tổng gần như không đổi. Mẫu nhỏ và là traffic test, chưa phải tải thật.
- **Chi phí thật, tài khoản dev:** AWS Budgets ghi nhận ≈ 0,09 USD cho tháng 11 đến 25/11, dự báo cả tháng ≈ 0,10 USD (không gồm Bedrock).
- **Chi phí thật, tài khoản chạy Bedrock** (Cost Explorer, 01–25/11): tổng **3,93 USD**, gồm 3,57 USD Bedrock và 0,36 USD thuế (≈ 10%).
  - Qwen3 32B: 11,8 triệu token vào, 0,94 triệu token ra = 2,40 USD.
  - gpt-oss-120b (dự phòng): 2,5 triệu token vào, 0,44 triệu token ra = 0,66 USD.
  - Guardrails: 3.266 text unit, tính theo dòng `ContentPolicyUnitsConsumed` (0,15 USD / 1.000 unit) = 0,49 USD. Dự toán dùng đúng dòng giá này.
  - Gemma 3 12B (chạy benchmark G1): 0,02 USD.
  - Phần lớn số lượt này là các lần chạy eval và test của nhóm, không phải người dùng thật.
- **Thuế:** hoá đơn có thêm ≈ 10% thuế mà bảng dự toán ở trên chưa tính (Pricing Calculator cũng không tính thuế).

**Tối ưu chi phí**

1. **Không VPC/NAT, không API Gateway, không OpenSearch, không GPU**: không có chi phí cố định hằng tháng; hoá đơn tăng theo mức dùng.
2. **AI local-first**: OCR, embedding và luật chạy trong Lambda, không tốn tiền theo token; chỉ judge gọi Bedrock với model open-weight giá rẻ.
3. **Fast path và cache**: mỗi lượt không gọi LLM tiết kiệm ≈ 0,00094 USD (token + Guardrails).
4. **Kill switch** (`CONFIG#global/FLAGS`): tắt judge LLM hoặc tắt nhận ảnh mà không cần deploy khi có alarm chi phí hay bị spam; rate limit theo người dùng chặn lạm dụng.
5. **Free Tier**: Lambda, SQS và CloudWatch nằm gọn trong mức always-free ở tải này (6,2 → 2,4 USD).
6. **Retention và lifecycle**: S3 đã tự xoá `donated/` sau 30 ngày. Đề xuất tiếp: giữ log 7 ngày (template chưa đặt `RetentionInDays`) và gắn lifecycle policy cho repo ECR `askfirst-dev-ai` nhóm đang dùng. Template `infra/github-oidc.yaml` đã có lifecycle giữ 5 image mới nhất, nhưng chỉ cho repo `askfirst-ai`; repo dev chưa có policy (kiểm 25/11/2026).

### 7. Rủi ro và cách giảm thiểu

| Rủi ro | Ảnh hưởng | Cách giảm thiểu | Tình trạng hiện tại |
|---|---|---|---|
| Bỏ sót tin lừa đảo (false negative) | Người cao tuổi mất tiền | Tầng luật có sàn điểm độc lập với LLM; không có mức "an toàn"; eval gate recall ≥ 90% trước mọi thay đổi prompt/model | Eval cuối: recall 100% trên 90 mẫu lừa đảo (3 lượt) |
| Báo nhầm tin lành tính | Người dùng mất niềm tin vào bot | Hiệu chỉnh ngưỡng fuse (70/60/25, ADR-005); gate báo nhầm ≤ 10% | 5% (1/20) trên tập lành tính trông đáng ngờ |
| Bộ eval nhỏ, chủ yếu tự soạn | Số liệu có thể lạc quan | Công khai hạn chế; thu mẫu đóng góp đã ẩn danh, có đồng ý | 134 mẫu có nhãn, **0 mẫu thật đã ẩn danh**; mục tiêu ban đầu là 300 |
| LLM lỗi, bị throttle hoặc hết quota | Không trả lời được | Model dự phòng, rồi chế độ chỉ chạy luật; bot luôn trả lời | Đã cài trong cascade |
| Prompt injection trong tin chuyển tiếp | Kết luận bị thao túng | Bedrock Guardrails `PROMPT_ATTACK`; phát hiện injection thì mức tối thiểu là `UNSURE`; output LLM chỉ đi qua template | Đã cài |
| Độ trễ và cold start | Trả lời chậm hơn mục tiêu 15 giây | Gom tin trên SQS; nạp model sẵn ở pha INIT của Lambda; làm nóng trước demo | Văn bản p95 3,5 giây (warm). **Ảnh đầu-cuối p95 17,0 giây, vượt 15 giây**; AI lead chấp nhận tới khoảng 30 giây. Init cold start của `ai` p50 6,2 giây |
| Chi phí tăng vọt do spam | Vượt ngân sách | Rate limit theo người dùng, kill switch, giới hạn concurrency SQS, alarm DLQ | AWS Budgets 50 USD/tháng, email cảnh báo khi chi phí thực vượt 10, 25, 40 và 50 USD (mức 10 và 25 USD thêm ngày 25/11/2026 cho khớp sơ đồ) |
| Dữ liệu cá nhân và sự đồng ý | Không tuân thủ pháp luật | Đồng ý chủ động và được lưu lại; lệnh rút đồng ý và xoá dữ liệu; che PII trước mọi lời gọi ra ngoài; không lưu nội dung gốc; TTL 30 ngày. Thiết kế đối chiếu Luật Bảo vệ dữ liệu cá nhân số 91/2025/QH15 (Quốc hội) và Nghị định 356/2025/NĐ-CP (Chính phủ); đây là ánh xạ thiết kế, không phải tư vấn pháp lý | |
| Model Bedrock không có ở region | Judge không chạy được | Sydney có model in-region; dự phòng Jakarta (`ap-southeast-3`); đổi model chỉ cần sửa `ai/config/models.yaml` | Chưa phải dùng |
| Nhân lực (thi cử, nhóm 4 người) | Trễ mốc | Làm theo cặp, cắt scope sớm (voice, VLM, multi-turn, Mini App), có sẵn thứ tự cắt tiếp nếu trễ | Đã cắt scope ở kế hoạch v3 |
