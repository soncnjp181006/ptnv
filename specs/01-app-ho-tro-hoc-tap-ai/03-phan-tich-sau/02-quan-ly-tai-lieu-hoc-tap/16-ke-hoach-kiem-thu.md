# 16 — Kế Hoạch Kiểm Thử: Tài Liệu Học Tập

## 1. Phạm vi
- Upload → parse → tag môn → search → hỏi AI → archive/xóa/purge; 4 đuôi PDF/DOCX/TXT/MD.
- Dữ liệu: giáo trình Toán 9, đề cương Lý 12, vở Anh, file scan mờ, file .exe, PDF 25MB.

## 2. Lớp test

| Lớp | Trường hợp | Kỳ vọng |
|-----|------------|---------|
| Unit | `validateType`, `chunk800/100`, `tagSuggest` | Đuổi .exe, chunks giữ page_no |
| Integration | Upload 5MB → S3 → queue → READY | Trừ quota, vector đủ chunks |
| Contract | `getReadyChunks` cho RAG | Chỉ READY + owner_id đúng |
| E2E | Nộp Toán 9 → hỏi "Vi-ét" → trích trang | Có nguồn trang 12 |
| Security | User B tìm doc PRIVATE user A | 403 + audit |
| Performance | 200 upload đồng thời, 10k search | Parse p95 <2p, search <600ms |
| Business | Free file 21MB / tổng 51MB | 413/403 + upsell Paid |
| Recovery | PDF lỗi retry 3 → FAILED → tải lại OK | DLQ + notify |

## 3. Môi trường
- Staging S3 bucket riêng, worker 2 replica, pgvector seed 5k chunks; không dùng đề thi thật có bản quyền.

## 4. Gate: pass ≥95%, READY rate ≥95% (trừ scan mờ), 0 rò PRIVATE.
