# 02 — Danh Sách Use Case: Gia Sư AI Hỏi Đáp

> Module: `ai-tutor-qa` | Mã: `BV-03` | Múi giờ: `Asia/Ho_Chi_Minh`

## 1. Tổng quan
- Actor chính: `Student` (Free/Paid), `Admin` (duyệt báo cáo), Hệ thống `LlmProvider`.
- Kênh: mobile app + web; xác thực `JWT`; mọi POST quota-sensitive yêu cầu `Idempotency-Key`.
- Quota: Free `20` câu AI/ngày (reset `00:00` `Asia/Ho_Chi_Minh`); Paid không giới hạn hợp lý (`fair-use 500/ngày`).

## 2. Danh sách Use Case
| Mã | Tên Use Case | Actor | Mô tả | Ưu tiên |
|---|---|---|---|---|
| UC-01 | Gửi câu hỏi AI | `Student` | Nhập `questionText` (1–2000 ký tự), chọn phạm vi `Document` | Must |
| UC-02 | Kiểm tra quota trước gọi LLM | System | Đọc `AiQuotaLedger`, giữ chỗ (hold) 1 lượt trước khi gọi LLM | Must |
| UC-03 | Truy hồi ngữ cảnh RAG | System | Embed câu hỏi, tìm Top-K `RagChunk`, lọc theo `similarity >= 0.72` | Must |
| UC-04 | Nhận câu trả lời kèm trích dẫn | `Student` | Stream SSE `ASSISTANT` message + `SourceRef` (tên tài liệu + trang) | Must |
| UC-05 | Đánh giá hữu ích | `Student` | Vote `HELPFUL`/`NOT_HELPFUL` mỗi `ASSISTANT` message 1 lần | Should |
| UC-06 | Báo cáo câu trả lời sai | `Student` | Tạo `AnswerReport` với lý do + đoạn sai, tối đa 5 báo cáo/ngày | Should |
| UC-07 | Xem lịch sử hội thoại | `Student` | Phân trang `ChatSession`, tìm kiếm theo từ khóa | Should |
| UC-08 | Tạo/xóa phiên chat | `Student` | Tạo `ChatSession` mới, xóa mềm (soft-delete) phiên cũ | Should |
| UC-09 | Lọc nội dung độc hại | System | `ToxicityFilter` chặn input/output vi phạm trước khi lưu/hiển thị | Must |
| UC-10 | Retry khi timeout | `Student`/System | Retry tối đa 1 lần với cùng `Idempotency-Key`, không trừ quota 2 lần | Must |
| UC-11 | Duyệt báo cáo sai | `Admin` | Duyệt `AnswerReport`: `CONFIRMED`/`REJECTED`, gắn nhãn cải thiện RAG | Should |
| UC-12 | Xem quota còn lại | `Student` | Hiển thị `remainingToday`, `resetAt`, gợi ý nâng cấp khi cạn | Must |
| UC-13 | Chặn concurrent vượt quota | System | Khóa lạc quan/queue per `User`, concurrent thứ N+1 chờ hoặc 429 | Must |
| UC-14 | Xuất hội thoại | `Student` (Paid) | Xuất `ChatSession` ra PDF/Markdown kèm citation | Could |

## 3. Đặc tả chi tiết Use Case trọng yếu
### UC-01 — Gửi câu hỏi AI
- Tiền điều kiện: đăng nhập; `ChatSession` ở trạng thái `ACTIVE`.
- Luồng chính: nhập câu hỏi → validate → lọc độc hại → hold quota → RAG → LLM → lưu → hiển thị.
- Hậu điều kiện: sinh `ChatMessage` USER + ASSISTANT, `AiQuotaLedger` trừ 1.

### UC-02 — Kiểm tra quota trước gọi LLM
- Tiền điều kiện: xác định `subscriptionPlan` (FREE/PAID) và ngày hiện tại `Asia/Ho_Chi_Minh`.
- Luồng chính: đọc ledger → nếu còn thì `hold` → gọi LLM → `commit`; nếu LLM lỗi hệ thống thì `rollback`.
- Ngoại lệ: hết quota giữa chừng (2 tab gửi đồng thời) → 1 request thắng, còn lại `429 QUOTA_EXCEEDED`.

### UC-04 — Nhận câu trả lời kèm trích dẫn
- Mỗi khẳng định từ tài liệu phải có `SourceRef {documentId, chunkId, page}`.
- Nếu similarity thấp: trả lời dạng "không tìm thấy trong tài liệu" + gợi ý đổi cách hỏi, vẫn tính quota.

### UC-10 — Retry khi timeout
- Client retry với cùng `Idempotency-Key`; server dedupe 24h, trả kết quả cached nếu `local success` đã lưu.
- Chỉ retry lỗi transient (`TIMEOUT`, `5xx`, `RATE_LIMIT`); không retry `4xx` nghiệp vụ.

## 4. Ma trận Actor ↔ Use Case
| Actor | Use Case |
|---|---|
| `Student` Free | UC-01, UC-04, UC-05, UC-06, UC-07, UC-08, UC-10, UC-12 |
| `Student` Paid | Toàn bộ Student + UC-14 |
| System | UC-02, UC-03, UC-09, UC-13 |
| `Admin` | UC-11 |

## 5. Quy tắc định danh
- `ChatSession`: `sess_{ulid}`; `ChatMessage`: `msg_{ulid}`; `AnswerReport`: `rep_{yyyyMMdd}_{seq}`.
- `Idempotency-Key`: UUID v4 do client sinh, scope per `User`.

## 6. Ghi chú phạm vi
- Không hỗ trợ voice-call realtime trong phase này; chỉ text + stream.
- Không dùng LLM để chấm điểm thi chính thức; chỉ hỗ trợ học tập.
