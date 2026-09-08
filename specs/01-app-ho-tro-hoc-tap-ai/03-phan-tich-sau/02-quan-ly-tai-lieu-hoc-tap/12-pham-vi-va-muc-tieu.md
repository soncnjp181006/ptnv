# 12 — Phạm Vi & Mục Tiêu: Tài Liệu Học Tập

## 1. Mục tiêu
- O1: 90% upload PDF/DOCX/TXT/MD < 20MB parse READY trong 2 phút.
- O2: 85% tag môn/khối AI gợi ý đúng, learner xác nhận 1-chạm.
- O3: Tìm kiếm học liệu p95 < 600ms, top-3 có trích dẫn trang.
- O4: 0 rò PRIVATE; 100% takedown bản quyền có audit.

## 2. IN
- Upload 4 đuôi, quota Free 10 TL/50MB, Paid 500 TL/5GB.
- Parse async, chunks 800/overlap 100, embed + vector, tag `subject/grade_level`.
- Search full-text + semantic, archive/restore, soft-delete 30 ngày, privacy PRIVATE.

## 3. OUT
- Soạn thảo online (Google Docs clone), OCR full, thư viện public, chia sẻ liên trường (Sprint 3+).
- Chấm bài tự luận ảnh viết tay.

## 4. Giả định
- Học sinh nộp slide/giáo trình photo mờ → cần thông báo FAILED thân thiện.
- Mùa ôn thi cuối kỳ upload tăng 5× → worker autoscale.
- Giờ log/reset theo `Asia/Ho_Chi_Minh`.

## 5. Thành công
- READY rate ≥ 95% (trừ scan mờ), hỏi AI có nguồn trang ≥ 90% câu trả lời.
