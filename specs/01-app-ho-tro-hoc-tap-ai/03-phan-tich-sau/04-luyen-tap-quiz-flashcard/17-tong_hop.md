# 17 — Tổng Hợp: Luyện Tập Quiz & Flashcard

> Tóm tắt phân tích sâu `quiz-flashcard` (BV-04).

## 1. Một trang
- Sinh (tốn 1 quota AI/lần, ledger chung BV-03) → DRAFT → làm IN_PROGRESS → nộp SUBMITTED → chấm SCORED → giải thích + làm lại miễn phí.
- Flashcard SM-2 ôn hằng ngày; báo câu sai cải thiện kho đề.
- Hàng rào: validate JSON 4 options/1 correct, khớp RAG, idempotency, retry 1, version chống nộp đôi.

## 2. Quyết định chốt (PROPOSED)
- 1 lượt/lần sinh, ledger chung, timeout 45s, retry 1, chấm local, version nộp, SM-2, 4 options.
- Chi tiết `06-danh-sach-quyet-dinh.md` DEC-01→DEC-12.

## 3. Quy tắc vàng
- BR-001 quota sinh; BR-002 luyện miễn phí; BR-003 idempotency; BR-004 validate JSON.
- BR-006 retry partial; BR-007 hoàn quota; BR-008 chống nộp đôi; BR-009 giải thích sau chấm.

## 4. Máy trạng thái
- `QuizAttempt`: DRAFT → IN_PROGRESS → SUBMITTED → SCORED (+ EXPIRED auto-submit, ABANDONED).
- `GenerationJob`: QUEUED → RUNNING → (PARTIAL) → SUCCEEDED / FAILED_RETRYABLE / FAILED_FINAL.

## 5. Rủi ro top 3
1. Partial JSON thiếu câu → retry + hoàn quota.
2. Hallucination đáp án → khớp RAG + duyệt mẫu 300 câu.
3. Double-submit 2 tab → version + 409.

## 6. Phủ/chất lượng
- Must 100%, Should 94%; điểm 3.9/5 ĐẠT có điều kiện.
- Gate: JSON ≥ 98%, đáp án ≥ 97%, 0 double-SCORED.

## 7. Tiếp theo
- Chốt DEC quota chung + giới hạn Free; POC JSON staging; theo `14-lo-trinh-thuc-hien.md`.

## 8. Sơ đồ liên kết tài liệu
- 01 (quy trình) → 02 (use case) → 03 (kịch bản) → 05 (quy tắc).
- 05 → 06 (quyết định) → 07 (dữ liệu) → 08 (liên module).
- 02/03/05 → 09 (truy vết) → 10 (độ phủ) → 11 (chất lượng).
- 12/13 → 14 (lộ trình) → 15 (backlog) → 16 (kiểm thử) → 18 (phát hành).
- 19 ghi mọi thay đổi của 18 tệp còn lại, giờ `Asia/Ho_Chi_Minh`.

## 9. Checklist thành viên mới
- [ ] Đọc 01 (L0/L1/L2) và 04 (máy trạng thái attempt chuẩn).
- [ ] Đọc 05 (BR-001→BR-010) và 06 (DEC Human Gate).
- [ ] Chạy TQ-03 (hết quota) và TQ-10 (nộp đôi) trên staging.
- [ ] Hiểu ledger chung QA+Quiz và ý nghĩa `version` khi nộp.

## 10. Tra nhanh
- Partial: JSON thiếu câu → retry 1 → hoàn quota nếu vẫn thiếu.
- Local success: job đã lưu DRAFT dù client thấy timeout.
- Mọi số liệu trong file là mục tiêu baseline 1.0, cập nhật khi POC xong.
- Câu hỏi chưa rõ → ghi issue, không tự suy diễn khi implement.
