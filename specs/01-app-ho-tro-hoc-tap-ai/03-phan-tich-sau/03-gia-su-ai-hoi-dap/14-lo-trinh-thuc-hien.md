# 14 — Lộ Trình Thực Hiện: Gia Sư AI Hỏi Đáp

> 3 sprint × 2 tuần, từ nền quota/RAG tới retry và hardening.

## 1. Sprint 1 — Nền chat + quota + RAG (tuần 1–2)
- Dựng `ChatSession/ChatMessage/SourceRef`, API gửi hỏi + stream SSE.
- `AiQuotaLedger` hold/commit/rollback, reset `Asia/Ho_Chi_Minh`, màn hình quota.
- RAG Top-K=5, ngưỡng 0.72, citation cơ bản.
- Exit: chat E2E có citation, hết quota trả 429 đúng.

## 2. Sprint 2 — Idempotency + retry + kiểm duyệt (tuần 3–4)
- `Idempotency-Key` dedupe 24h, retry 1 lần, xử lý timeout-nhưng-local-success.
- `ToxicityFilter` 2 chiều, `BLOCKED`, khóa 1h sau 3 vi phạm.
- Vote + `AnswerReport PENDING`, trang lịch sử chat.
- Exit: test concurrent 0 double-charge; moderation pass bộ 300 mẫu.

## 3. Sprint 3 — Hallucination guard + admin + hardening (tuần 5–6)
- Numeric-check, `NEEDS_REVIEW`, thú nhận khi thiếu ngữ cảnh.
- Admin duyệt `CONFIRMED/REJECTED/DUPLICATE/FIXED`, event `report.confirmed`.
- Redis lock per user, fallback model rẻ, cache câu hot, dashboard quota/citation.
- Exit: citation ≥ 95%, TTFT P95 < 4s, UAT không P0.

## 4. Cột mốc (milestone)
| Mốc | Thời điểm | Điều kiện |
|---|---|---|
| M1 Chat có citation | Cuối sprint 1 | Demo 20 câu RAG thật |
| M2 Quota an toàn | Cuối sprint 2 | Race test pass |
| M3 Release readiness | Cuối sprint 3 | Gate file 11 PASS |

## 5. Phụ thuộc
- Module tài liệu phải có API chunk trước sprint 1; Billing quota API trước sprint 1.
- Khóa API LLM + filter kiểm duyệt cấp trước ngày kickoff 3 ngày.

## 6. Rủi ro lộ trình
- Filter tiếng Việt kém → tốn sprint 2; dự phòng dùng filter thứ 2.
- LLM latency cao → cắt regenerate, giữ thú nhận để kịp M3.

## 7. Nhân sự và vai trò
- 1 BE (quota + idempotency), 1 BE (RAG + stream), 1 FE (chat + paywall), 1 QA.
- AI owner: ngưỡng similarity + numeric-check; DBA review migration M-02/M-04.
- Họp đồng bộ 15 phút/ngày trong sprint 1–2 để xử lý nghẽn filter/LLM.

## 8. Tiêu chí đóng sprint
- Sprint đóng khi 100% exit criteria đạt + demo được trên staging.
- Task dở chuyển rõ sang sprint sau, không tự động lăn mà thiếu review.

## 9. Theo dõi sau từng sprint
- Đo lại citation coverage và quota race sau mỗi sprint, cập nhật file 10.
- Retro ghi 3 điều làm tốt và 2 điều cần sửa cho sprint kế tiếp.
- Retro action có owner và hạn, kiểm tra lại ở sprint planning sau.
