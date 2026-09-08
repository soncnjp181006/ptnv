# 03 — Ma Trận Kịch Bản: Quản Lý Tài Liệu Học Tập

## 1. UC-DOC-01 `uploadDocument`

| Kịch bản | Điều kiện | Luồng | Kết quả |
|----------|-----------|-------|---------|
| Main | PDF 5MB, còn quota Free | Validate → UPLOADING → S3 → enqueue parse | 201 + `document_id` |
| Alt-01 | TXT/MD nhỏ | Parse đồng bộ < 3s → READY luôn | 201 READY |
| Exc-01 | Vượt 10 TL hoặc 50MB | Chặn trước upload | 403 `DOC_QUOTA_EXCEEDED` |
| Exc-02 | Sai đuôi (.exe/.zip) | Từ chối | 400 `UNSUPPORTED_TYPE` |
| Exc-03 | File > 20MB (Free) | Từ chối, gợi ý Paid/nén | 413 `FILE_TOO_LARGE` |

## 2. UC-DOC-02 `extractText` (Worker)

| Kịch bản | Điều kiện | Luồng | Kết quả |
|----------|-----------|-------|---------|
| Main | PDF text chuẩn | Parse → chunks 800 + embed → READY | READY + vector |
| Alt-01 | DOCX công thức Toán | Giữ LaTeX/text, ảnh công thức thành `[figure]` | READY partial |
| Exc-01 | PDF scan mờ, không OCR được | FAILED + hướng dẫn chụp lại | FAILED + retry |
| Exc-02 | Timeout 5p | Kill job → FAILED → DLQ | Retry ≤ 3 lần |

## 3. UC-DOC-05/06 Tìm kiếm & xem

| Kịch bản | Luồng | Kết quả |
|----------|-------|---------|
| Main-search | Gõ "định lý Vi-ét" → full-text + vector trên docs READY của mình | Top chunks kèm trích dẫn trang |
| Alt-01 | Lọc môn Toán + lớp 9 | Kết quả thu hẹp đúng tag |
| Exc-01 | Tìm trên ARCHIVED | Mặc định loại, có toggle "gồm lưu trữ" |

## 4. UC-DOC-08/10/14 Lưu trữ / xóa / gỡ

| Kịch bản | Luồng | Kết quả |
|----------|-------|---------|
| Archive | READY → ARCHIVED, loại khỏi RAG mặc định | 200 |
| Soft-delete | → DELETED, giữ S3 + vector 30 ngày | 200, auto-purge |
| Admin-gỡ | Đề thi lậu → gỡ + audit + thông báo learner | 200 + `COPYRIGHT_TAKEDOWN` |
