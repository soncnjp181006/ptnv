# 05 — Bộ Quy Tắc Nghiệp Vụ: Gia Sư AI Hỏi Đáp

> Format chuẩn: `BR-XXX / WHEN / MUST / OTHERWISE / AFFECTS / SOURCE / STATUS`.

## BR-001 — Kiểm tra quota trước mọi gọi LLM
- WHEN: `Student` gửi `ChatMessage` role USER tới `AiTutorService`.
- MUST: Hệ thống kiểm tra `AiQuotaLedger` theo ngày `Asia/Ho_Chi_Minh` và giữ chỗ 1 lượt trước khi gọi `LlmProvider`.
- OTHERWISE: Từ chối `429 QUOTA_EXCEEDED`, không gọi LLM, hiển thị paywall nâng cấp.
- AFFECTS: UC-01, UC-02, `AiQuotaLedger`, `ChatMessage`.
- SOURCE: Chính sách freemium Free 20 câu/ngày.
- STATUS: APPROVED.

## BR-002 — Trừ quota nguyên tử chống concurrent
- WHEN: Nhiều request đồng thời của cùng `User` khi quota sắp cạn.
- MUST: Trừ quota bằng transaction nguyên tử (`UPDATE ... WHERE remaining > 0`); request thua nhận 429 kèm `Retry-After`.
- OTHERWISE: Cấm trừ quota về âm; ghi `quotaRaceBlocked` metric.
- AFFECTS: UC-02, UC-13, SC-UC02-03.
- SOURCE: AGENT_INFERENCE từ yêu cầu concurrent request.
- STATUS: APPROVED.

## BR-003 — Idempotency chống double-charge
- WHEN: Client retry `POST /chat/messages` do timeout/mất mạng.
- MUST: Yêu cầu header `Idempotency-Key` (UUID v4); server dedupe 24h theo `(userId, key)`, retry cùng key không trừ quota thêm.
- OTHERWISE: Thiếu key → 400; key khác → tính câu hỏi mới và trừ quota mới.
- AFFECTS: UC-10, `IdempotencyRecord`, SC-UC10-01.
- SOURCE: AGENT_INFERENCE từ yêu cầu idempotency.
- STATUS: APPROVED.

## BR-004 — Trích dẫn nguồn bắt buộc
- WHEN: Câu trả lời dựa trên `RagChunk` của `Document`.
- MUST: Mỗi khẳng định事实 phải kèm `SourceRef {documentId, chunkId, page}`; similarity ngưỡng `>= 0.72`.
- OTHERWISE: Chặn xuất bản, regenerate tối đa 1 lần; nếu vẫn thiếu thì trả lời thành thật "ngoài tài liệu".
- AFFECTS: UC-03, UC-04, chống hallucination.
- SOURCE: Yêu cầu trích dẫn nguồn + chống hallucination.
- STATUS: APPROVED.

## BR-005 — Lọc nội dung độc hại hai chiều
- WHEN: Có `questionText` vào hoặc `answerText` ra.
- MUST: Chạy `ToxicityFilter`; input vi phạm → 422 không trừ quota; output vi phạm → thay bằng mẫu từ chối + log.
- OTHERWISE: Mọi vi phạm ghi `ModerationLog`, vượt 3 lần/ngày thì khóa chat 1h.
- AFFECTS: UC-09, `ChatMessage`, `ModerationLog`.
- SOURCE: Yêu cầu lọc nội dung độc hại.
- STATUS: APPROVED.

## BR-006 — Timeout, retry và fallback
- WHEN: `LlmProvider` quá `30s` hoặc lỗi transient `5xx/429`.
- MUST: Retry đúng 1 lần (backoff 2s) với cùng key; thất bại thì rollback hold quota và trả fallback xin lỗi + nút thử lại.
- OTHERWISE: Cấm retry lỗi `4xx` nghiệp vụ; cấm retry vòng 2 tự động.
- AFFECTS: UC-10, SC-UC10-02, `LlmCallLog`.
- SOURCE: Yêu cầu retry/timeout/fallback + partial completion.
- STATUS: APPROVED.

## BR-007 — Hoàn quota khi lỗi hệ thống
- WHEN: LLM thất bại cuối (`FAILED_FINAL`) không do lỗi người dùng.
- MUST: Rollback hold (hoàn 1 quota) và ghi `quotaRefund {reason}`.
- OTHERWISE: Không hoàn quota cho câu trả lời thành công dù user vote NOT_HELPFUL.
- AFFECTS: UC-02, UC-10, `AiQuotaLedger`.
- SOURCE: AGENT_INFERENCE (công bằng freemium).
- STATUS: PROPOSED.

## BR-008 — Giới hạn báo cáo và vote
- WHEN: `Student` vote hoặc tạo `AnswerReport`.
- MUST: Mỗi ASSISTANT message 1 vote (đổi phải PATCH); tối đa 5 `AnswerReport`/ngày/user.
- OTHERWISE: Vượt hạn → 429, giữ dữ liệu cũ.
- AFFECTS: UC-05, UC-06, `FeedbackVote`, `AnswerReport`.
- SOURCE: Yêu cầu đánh giá hữu ích + báo cáo câu sai.
- STATUS: APPROVED.

## BR-009 — Reset quota theo Asia/Ho_Chi_Minh
- WHEN: Đồng hồ sang `00:00` giờ `Asia/Ho_Chi_Minh`.
- MUST: Reset `usedToday=0` cho gói Free; Paid fair-use reset cùng thời điểm.
- OTHERWISE: Job reset lỗi → giữ ledger cũ + alert, không cho quota âm hay vô hạn.
- AFFECTS: UC-12, scheduler `quota-reset`.
- SOURCE: Quy ước chung múi giờ + quota.
- STATUS: APPROVED.

## BR-010 — Thú nhận khi thiếu ngữ cảnh
- WHEN: Không có `RagChunk` đạt ngưỡng similarity.
- MUST: Trả lời rõ "không tìm thấy trong tài liệu đã chọn", gợi ý thu hẹp phạm vi, vẫn tính 1 quota.
- OTHERWISE: Cấm bịa số liệu/trang tài liệu không tồn tại.
- AFFECTS: UC-04, chống hallucination.
- SOURCE: Yêu cầu trích dẫn + hallucination.
- STATUS: APPROVED.
