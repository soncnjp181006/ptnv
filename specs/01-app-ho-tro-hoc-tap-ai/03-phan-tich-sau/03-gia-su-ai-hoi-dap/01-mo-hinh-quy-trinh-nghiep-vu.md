# 01 — Mô Hình Quy Trình Nghiệp Vụ: Gia Sư AI Hỏi Đáp

> Module: `ai-tutor-qa` | Mã nghiệp vụ: `BV-03` | Múi giờ: `Asia/Ho_Chi_Minh` | Quota Free: `20` câu AI/ngày

## 1. Mục đích
- Mô tả luồng hỏi đáp AI theo ngữ cảnh tài liệu (RAG): hỏi → kiểm tra quota → truy hồi → gọi LLM → trích dẫn nguồn → đánh giá.
- Làm căn cứ cho Use Case, Business Rule, State Model và kiểm thử.
- Ngăn hallucination, lạm dụng quota, nội dung độc hại và lỗi retry/timeout.

## 2. Thuật ngữ và định danh
| Thuật ngữ | Identifier | Ghi chú |
|---|---|---|
| Phiên hỏi đáp | `ChatSession` | Một chủ đề hội thoại của `User` |
| Tin nhắn hỏi | `ChatMessage` (role=`USER`) | Câu hỏi của học viên |
| Tin nhắn đáp | `ChatMessage` (role=`ASSISTANT`) | Câu trả lời của AI |
| Bản ghi quota | `AiQuotaLedger` | Trừ quota theo ngày `Asia/Ho_Chi_Minh` |
| Đoạn trích nguồn | `RagChunk` | Chunk từ `Document` + `Embedding` |
| Đánh giá hữu ích | `FeedbackVote` | `HELPFUL` / `NOT_HELPFUL` |
| Báo cáo sai | `AnswerReport` | Học viên báo câu trả lời sai |

## 3. Quy trình L0 — Toàn cảnh (End-to-End)

```mermaid
flowchart TB
    U["👤 Học viên User"] -->|"1. Gửi câu hỏi questionText"| S["💬 ChatSession / ChatMessage"]
    S -->|"2. Kiểm tra quota AiQuotaLedger"| Q{"❓ Còn quota?"}
    Q -->|"3A — Còn quota: truy hồi RAG"| R["📚 RAG Retrieval RagChunk"]
    Q -->|"3B — Hết quota: chặn + upsell"| B["⛔ QuotaExceeded + Paywall"]
    R -->|"4. Gọi LLM có ngữ cảnh"| L["🤖 LlmProvider"]
    L -->|"5. Trả lời kèm citation + feedback"| U
    U -->|"6. Đánh giá / báo cáo sai"| F["⭐ FeedbackVote / AnswerReport"]
    F -->|"7. Tổng hợp cải thiện"| A["📊 Admin Review Queue"]
```

## 4. Quy trình L1 — Luồng nghiệp vụ chi tiết

```mermaid
flowchart TB
    A1["📝 Nhập câu hỏi + chọn tài liệu Document"] -->|"1. Validate độ dài và ngôn ngữ"| A2["🛡️ Lọc nội dung độc hại ToxicityFilter"]
    A2 -->|"2. Nội dung sạch: kiểm tra quota"| A3{"❓ AiQuotaLedger còn lượt?"}
    A3 -->|"3A — Còn quota: giữ chỗ quota hold"| A4["📚 Truy hồi Top-K RagChunk"]
    A3 -->|"3B — Hết quota: trả 429 + gợi ý nâng cấp"| A9["💳 Paywall Subscription"]
    A4 -->|"4. Dựng prompt có citation bắt buộc"| A5["🤖 Gọi LLM timeout 30s"]
    A5 -->|"5. Thành công: lưu ASSISTANT + citation"| A6["💾 Persist ChatMessage + SourceRef"]
    A6 -->|"6. Hiển thị + xin feedback"| A7["⭐ Feedback HELPFUL/NOT_HELPFUL"]
    A7 -->|"7. Nếu báo sai: tạo ticket"| A8["🚩 AnswerReport → ReviewQueue"]
    A5 -->|"8. Timeout/lỗi: retry 1 lần + fallback"| A4
```

## 5. Quy trình L2 — Xử lý kỹ thuật sâu (quota, idempotency, fallback)

```mermaid
flowchart TB
    B1["📥 POST /chat/messages + Idempotency-Key"] -->|"1. Dedupe Idempotency-Key 24h"| B2["🔍 Kiểm tra concurrent request per User"]
    B2 -->|"2. Không trùng: atomic decrement quota"| B3{"❓ Trừ quota thành công?"}
    B3 -->|"3A — Thành công: embed câu hỏi"| B4["🧮 Embedding + vector search Top-K=5"]
    B3 -->|"3B — Thất bại: hết quota giữa chừng"| B10["⛔ Rollback hold + trả 429 QUOTA_EXCEEDED"]
    B4 -->|"4. Gọi LLM stream + citations"| B5{"❓ LLM timeout sau 30s?"}
    B5 -->|"5A — Không timeout: validate citation"| B6["✅ Lưu ASSISTANT + SourceRef + usage"]
    B5 -->|"5B — Timeout nhưng local success: đối soát"| B7["🔄 Reconcile: đã lưu thì trả cached"]
    B6 -->|"6. Trả SSE + feedback widget"| B8["⭐ Client render citation + vote"]
    B7 -->|"7. Chưa lưu: retry 1 lần exponential"| B9["↩️ Retry once + fallback apology"]
    B9 -->|"8. Ghi log + metric"| B11["📈 LlmCallLog latency/tokens/error"]
```

## 6. Ma trận trách nhiệm RACI
| Hoạt động | User | AiTutorService | LlmProvider | Admin |
|---|---|---|---|---|
| Gửi câu hỏi | R | A | — | — |
| Kiểm tra/trừ quota | — | R/A | — | — |
| Truy hồi RAG | — | R | — | — |
| Gọi LLM | — | R | R | — |
| Duyệt AnswerReport | — | — | — | R/A |

## 7. Ghi chú kiểm soát
- Mọi mũi tên L0/L1/L2 đã đánh số; nhánh dùng hậu tố A/B.
- `Idempotency-Key` bắt buộc cho POST message để chống double-charge quota khi retry.
- Citation bắt buộc khi câu trả lời dựa trên tài liệu; nếu không đủ ngữ cảnh phải thú nhận.
