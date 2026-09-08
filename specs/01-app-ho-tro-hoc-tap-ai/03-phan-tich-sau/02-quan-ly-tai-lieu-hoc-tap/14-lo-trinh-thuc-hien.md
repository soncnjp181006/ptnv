# 14 — Lộ Trình Thực Hiện: Tài Liệu Học Tập

## Giai đoạn 1 (Tuần 1): Upload + quota
- `uploadDocument`, validate 4 đuôi, trần 20MB/file, quota 10 TL/50MB Free.
- S3 + virus-scan + `UPLOADING` → enqueue.
- Xong khi: chặn 403 quá quota + upsell Paid đúng.

## Giai đoạn 2 (Tuần 2–3): Worker RAG
- Parse PDF/DOCX/TXT/MD, chunks 800/100 + `page_no`, embed + pgvector → READY/FAILED.
- `tagSubject` AI gợi ý Toán/Lý/Hóa/Anh + `grade_level`.
- Retry 3 + DLQ + timeout 5p.

## Giai đoạn 3 (Tuần 4): Search + vòng đời
- `searchDocuments` full-text + vector, lọc môn/khối, toggle ARCHIVED.
- Archive/restore, soft-delete 30 ngày + purge, privacy PRIVATE, takedown tay Admin.
- Tải kiểm tra 5× mùa thi, search p95 < 600ms.

## Phụ thuộc
- Chờ Auth `owner_id + plan` (mock được); RAG hỏi AI/Quiz tiêu thụ READY sau.
