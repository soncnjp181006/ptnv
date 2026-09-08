# 12 — Phạm Vi và Mục Tiêu: Gia Sư AI Hỏi Đáp

> Module `ai-tutor-qa` trong app hỗ trợ học tập + AI freemium.

## 1. Mục tiêu (SMART)
- O1: Học viên Free hỏi tối đa 20 câu/ngày, Paid fair-use 500/ngày, citation ≥ 95%.
- O2: P95 trả lời đầu tiên (TTFT) < 4s, hoàn tất P95 < 30s.
- O3: Tỉ lệ HELPFUL ≥ 80% sau 4 tuần; report sai được duyệt < 48h.
- O4: Zero double-charge quota khi retry; zero vượt quota khi concurrent.

## 2. Trong phạm vi (In-scope)
- Chat text + stream SSE theo ngữ cảnh `Document` (RAG Top-K=5).
- Quota hold/commit/rollback, `Idempotency-Key`, reset `Asia/Ho_Chi_Minh`.
- Citation `SourceRef`, vote, `AnswerReport`, lọc độc hại 2 chiều.
- Retry 1 lần, fallback apology, hoàn quota lỗi hệ thống.
- Lịch sử `ChatSession`, xem quota còn lại, paywall gợi ý.

## 3. Ngoài phạm vi (Out-scope)
- Voice chat realtime, gọi video gia sư người thật.
- Chấm thi chính thức, cấp chứng chỉ.
- Đa LLM routing thông minh, fine-tune model riêng.
- Xuất PDF (UC-14) dời phase 2.

## 4. Persona và giới hạn
- Học viên THCS–đại học, giáo viên tạo tài liệu; Admin duyệt báo cáo.
- Giới hạn: câu hỏi 1–2000 ký tự; file RAG do module tài liệu quản lý.

## 5. Ràng buộc
- Múi giờ `Asia/Ho_Chi_Minh`; quota Free 20/ngày; `Subscription` gồm TRIAL/FREE→PENDING_PAYMENT→ACTIVE→EXPIRING→EXPIRED/CANCELLED.
- Tuân thủ nội dung giáo dục, che PII trong log kiểm duyệt.

## 6. Tiêu chí thành công
- 100% request hết quota trả 429 + paywall đúng; 100% retry cùng key không trừ thêm.
- Citation coverage ≥ 95% trên bộ kiểm định 200 câu.
- Không có P0 moderation bypass trong UAT.

## 7. Giả định
- `LlmProvider` uptime ≥ 99.5%; vector DB P95 < 1.2s.
- Client luôn sinh `Idempotency-Key` đúng chuẩn UUID v4.

## 8. Liên kết mục tiêu với metric
- O1 (citation ≥ 95%) ← metric `citationCoverage` trên bộ 200 câu mẫu.
- O2 (TTFT < 4s) ← dashboard latency P50/P95 theo ngày.
- O3 (HELPFUL ≥ 80%) ← `FeedbackVote` tổng hợp tuần, alert khi < 75%.
- O4 (zero double-charge) ← metric `dedupeHit` + `quotaRefund` đối soát ledger.
- Review metric hằng tuần trong sprint review; lệch > 10% phải có action.
- Metric lệch > 10% hai tuần liên tiếp → review lại O tương ứng.
- PO sở hữu O1/O3, Tech Lead sở hữu O2/O4.
- Dashboard metric đặt ở trang tổng quan sprint, ai cũng xem được.
