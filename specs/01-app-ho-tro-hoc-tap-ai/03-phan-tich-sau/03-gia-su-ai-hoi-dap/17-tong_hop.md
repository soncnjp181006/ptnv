# 17 — Tổng Hợp: Gia Sư AI Hỏi Đáp

> Tóm tắt phân tích sâu module `ai-tutor-qa` (BV-03).

## 1. Bức tranh một trang
- Học viên hỏi → hold quota → RAG Top-K=5 → LLM stream + citation → vote/báo sai.
- Hàng rào: quota nguyên tử, idempotency 24h, retry 1 lần, lọc độc hại 2 chiều, thú nhận khi thiếu ngữ cảnh.
- Mọi lỗi hệ thống hoàn quota; mọi retry cùng key không tính phí thêm.

## 2. Quyết định chốt (PROPOSED, chờ duyệt)
- Similarity `0.72`, timeout `30s`, retry `1`, TTL key `24h`, hold→commit/rollback, lock per user.
- Xem chi tiết `06-danh-sach-quyet-dinh.md` (DEC-01→DEC-12).

## 3. Quy tắc vàng (BR)
- BR-001 quota trước LLM; BR-002 atomic chống race; BR-003 idempotency; BR-004 citation.
- BR-005 kiểm duyệt; BR-006 retry/fallback; BR-007 hoàn quota; BR-009 reset `Asia/Ho_Chi_Minh`.

## 4. Trạng thái máy
- `ChatMessage`: PENDING_MODERATION → QUOTA_HELD → RETRIEVING → STREAMING → COMPLETED.
- Nhánh: BLOCKED / FAILED_RETRYABLE / FAILED_FINAL; timeout-nhưng-local-success trả cached.
- `AnswerReport`: PENDING → CONFIRMED → FIXED (hoặc REJECTED/DUPLICATE).

## 5. Rủi ro top 3
1. Hallucination số liệu → numeric-check sprint 3.
2. Concurrent hết quota giữa chừng → atomic + lock + test race.
3. Bypass kiểm duyệt tiếng Việt → 2 lớp filter + review tay.

## 6. Độ phủ và chất lượng
- Must 100%, Should 92%; điểm trung bình 3.9/5 (ĐẠT có điều kiện).
- Gate release: citation ≥ 95%, 0 double-charge, 0 moderation bypass.

## 7. Bước tiếp theo
- POC filter + numeric-check sprint 1; chốt DEC Human Gate; implement theo `14-lo-trinh-thuc-hien.md`.

## 8. Sơ đồ liên kết tài liệu
- 01 (quy trình) → 02 (use case) → 03 (kịch bản) → 05 (quy tắc).
- 05 → 06 (quyết định) → 07 (dữ liệu) → 08 (liên module).
- 02/03/05 → 09 (truy vết) → 10 (độ phủ) → 11 (chất lượng).
- 12/13 → 14 (lộ trình) → 15 (backlog) → 16 (kiểm thử) → 18 (phát hành).
- 19 ghi mọi thay đổi của 18 tệp còn lại, giờ `Asia/Ho_Chi_Minh`.

## 9. Checklist đọc hiểu cho thành viên mới
- [ ] Đọc 01 (3 sơ đồ L0/L1/L2) và 04 (máy trạng thái message).
- [ ] Đọc 05 (BR-001→BR-010) và 06 (DEC cần Human Gate).
- [ ] Chạy thử TC-02 (hết quota) và TC-08 (retry cached) trên staging.
- [ ] Nắm giờ reset quota và ý nghĩa `Idempotency-Key` trước khi code.

## 10. Thuật ngữ tra nhanh
- Hold/commit/rollback: giữ chỗ → chốt trừ → hoàn quota.
- Local success: server đã lưu dù client thấy timeout.
- Partial completion: stream/JSON dở dang cần retry đúng 1 lần.
