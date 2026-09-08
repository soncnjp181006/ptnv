# 17 — Tổng Hợp: Quản Lý Tài Liệu Học Tập

## 1. Một trang
- Learner nộp PDF/DOCX/TXT/MD (Free ≤10 TL/50MB/20MB-file) → UPLOADING → worker parse chunks 800/100 + embed → READY gắn tag môn/khối → hỏi AI + sinh Quiz có trích dẫn trang → ARCHIVED hết kỳ / DELETED purge 30 ngày. Mặc định PRIVATE.

## 2. Đã chốt
- 4 đuôi, quota Free/Paid (500 TL/5GB Paid), chỉ READY vào RAG, retry 3 + timeout 5p, takedown tay Sprint 1.

## 3. Còn mở
- DEC-DOC-01/02/03 (PPTX, OCR scan, trần Free) chờ user; thư viện public + link lớp để Sprint 2.

## 4. Sẵn sàng thiết kế
- Đủ API `POST /docs`, `GET /docs/search`, schema `documents/doc_chunks/doc_logs`, mã lỗi quota; độ phủ 90% → PASS có điều kiện.
