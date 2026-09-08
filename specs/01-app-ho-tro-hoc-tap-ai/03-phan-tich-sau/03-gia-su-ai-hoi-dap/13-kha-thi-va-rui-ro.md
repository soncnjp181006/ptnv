# 13 — Khả Thi và Rủi Ro: Gia Sư AI Hỏi Đáp

> Đánh giá kỹ thuật, chi phí LLM, pháp lý nội dung giáo dục.

## 1. Khả thi kỹ thuật
| Hạng mục | Đánh giá | Ghi chú |
|---|---|---|
| RAG Top-K + vector search | Cao | Stack phổ biến, POC 1 tuần |
| Stream SSE + citation | Cao | Chuẩn chat hiện nay |
| Quota atomic + Redis lock | Cao | Pattern quen thuộc |
| Idempotency 24h | Cao | Bảng + TTL index đơn giản |
| Toxicity filter 2 chiều | Trung bình | Phụ thuộc API kiểm duyệt tiếng Việt |
| Hallucination guard số liệu | Trung bình | Cần numeric-check custom |

## 2. Khả thi chi phí
- Mỗi câu hỏi ≈ embedding + 1 LLM call (+1 khi regenerate); retry tối đa 1 nên trần 2 calls/câu.
- Free 20/ngày là rào chắn chi phí; Paid fair-use 500/ngày kèm alert vượt.
- Ước tính: cache câu hỏi hot giảm ~15% chi phí; model fallback rẻ giảm 40% khi provider chính lỗi.

## 3. Ma trận rủi ro
| Mã | Rủi ro | Xác suất | Tác động | Giảm thiểu | Dự phòng |
|---|---|---|---|---|---|
| R-01 | Hallucination số liệu giáo dục | Cao | Cao | Citation bắt buộc + numeric-check | Gắn NEEDS_REVIEW + thú nhận |
| R-02 | Hết quota giữa chừng (concurrent) | Cao | Trung bình | Atomic decrement + lock | Rollback + 429 + Retry-After |
| R-03 | LLM timeout nhưng local success | Trung bình | Trung bình | Dedupe key trả cached | Không trừ quota thêm |
| R-04 | Partial completion stream đứt | Trung bình | Trung bình | Retry 1 backoff 2s | Fallback + hoàn quota |
| R-05 | Provider sập hoàn toàn | Thấp | Cao | Model fallback + breaker | Apology + alert P1 |
| R-06 | Bypass kiểm duyệt tiếng Việt | Trung bình | Cao | 2 lớp filter + khóa 1h | Review tay 24h |
| R-07 | Vượt chi phí LLM | Trung bình | Cao | Quota + fair-use + cache | Ngắt Paid mềm 429 |
| R-08 | Citation gãy (tài liệu xóa) | Thấp | Trung bình | Snapshot chunk 30 ngày | Trỏ bản snapshot |

## 4. Rủi ro pháp lý/giáo dục
- Nội dung sai gây học sai: gắn disclaimer "AI hỗ trợ, kiểm chứng giáo viên".
- PII học viên trong log: che trước khi lưu `ModerationLog`/`LlmCallLog`.

## 5. Kết luận khả thi
- KHẢ THI với điều kiện: POC numeric-check + filter tiếng Việt trong sprint 1.
- Go/No-go: No-go nếu moderation bypass > 0 trên bộ kiểm định độc hại 300 mẫu.

## 6. Kế hoạch POC và ngân sách dự phòng
- POC-01 (tuần 1): filter tiếng Việt trên 300 mẫu độc hại, pass khi recall ≥ 99%.
- POC-02 (tuần 2): numeric-check số liệu trên 200 câu, pass khi lỗi < 5%.
- Ngân sách: dự phòng 20% chi phí LLM cho retry/regenerate trong UAT.
- Nếu POC-01 fail: chuyển nhà cung cấp filter dự phòng, lùi M2 tối đa 1 tuần.
- Quyết định Go/No-go chốt tại sprint review sprint 1, có biên bản.
- POC fail 2 lần liên tiếp → dừng module 1 sprint để spike lại giải pháp.
- Chi phí drill giả lập tính vào ngân sách UAT, không vượt 5%.
