# 12 — Phạm Vi và Mục Tiêu: Quiz & Flashcard

> Module `quiz-flashcard` (BV-04) trong app học tập + AI freemium.

## 1. Mục tiêu SMART
- O1: Sinh Quiz 5/10/20 câu và deck 10–50 thẻ từ tài liệu, JSON hợp lệ ≥ 98%.
- O2: Làm bài DRAFT→IN_PROGRESS→SUBMITTED→SCORED, chấm local < 200ms.
- O3: Mỗi câu có giải thích + nguồn; tỉ lệ báo sai < 3%.
- O4: Zero double-SCORED; zero trừ quota oan khi sinh lỗi.

## 2. Trong phạm vi
- Cấu hình số câu/độ khó, sinh Quiz + Flashcard AI tốn 1 quota/lần (ledger chung BV-03).
- Làm bài, nộp, chấm local, xem giải thích sau SCORED, làm lại, ôn sai.
- Ôn flashcard SM-2, lịch sử, streak, báo câu sai, retry sinh, chống nộp đôi.
- Quota view chung, paywall khi cạn.

## 3. Ngoài phạm vi
- Thi giám sát, chấm tự luận AI, cấp chứng chỉ.
- Teacher giao bài hàng loạt + analytics lớp (phase 2).
- Fine-tune sinh đề theo năng lực adaptively (phase 3).

## 4. Persona
- Học viên tự luyện; teacher tạo đề nhanh; admin duyệt báo sai.
- Giới hạn: Free 10 câu/lần sinh; Paid/Teacher 20 câu/lần.

## 5. Ràng buộc
- Múi giờ `Asia/Ho_Chi_Minh`; `Subscription` TRIAL/FREE→PENDING_PAYMENT→ACTIVE→EXPIRING→EXPIRED/CANCELLED.
- Attempt chuẩn DRAFT→IN_PROGRESS→SUBMITTED→SCORED; giải thích sau chấm.

## 6. Thành công
- 100% nộp đôi chỉ 1 SCORED; 100% sinh lỗi hệ thống được hoàn quota.
- Bộ 300 câu duyệt tay: đáp án đúng ≥ 97%.

## 7. Giả định
- Tài liệu đã ingest RAG; LLM JSON mode ổn định; client giữ `version` khi nộp.

## 8. Liên kết mục tiêu với metric
- O1 (JSON ≥ 98%) ← metric `genJsonValidRate` trên 100 lần sinh mẫu.
- O2 (chấm < 200ms) ← latency submit→SCORED P95.
- O3 (báo sai < 3%) ← `QuestionReport`/tổng câu đã sinh theo tuần.
- O4 (zero double-SCORED) ← metric `submit409` + quét trùng SCORED nightly.
- Review metric hằng tuần; lệch > 10% phải có action owner rõ ràng.

## 9. Ràng buộc dùng chung với BV-03
- Ledger quota chung: cạn vì chat thì sinh đề cũng 429 và ngược lại.
- Thông điệp paywall thống nhất: còn X lượt AI hôm nay, reset 00:00 VN.

## 10. Điều kiện mở rộng phạm vi
- Teacher giao bài hàng loạt chỉ vào phase 2 khi PUBLISHED ổn định.
- Adaptive difficulty phase 3 cần đủ 10k attempt mẫu mới POC.
- Mọi O có deadline đo ở M3, không dời quá 1 sprint nếu chưa đạt.
