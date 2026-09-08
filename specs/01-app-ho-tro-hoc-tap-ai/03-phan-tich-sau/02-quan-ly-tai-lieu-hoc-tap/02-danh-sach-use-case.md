# 02 — Danh Sách Use Case: Quản Lý Tài Liệu Học Tập

> Actor: `LearnerFree`, `LearnerPaid`, `Admin`, `System(Worker)`.

| ID | Tên Use Case | Actor | Mô tả | Ưu tiên |
|----|--------------|-------|-------|----------|
| UC-DOC-01 | `uploadDocument` | Learner | Tải PDF/DOCX/TXT/MD, tạo UPLOADING | Must |
| UC-DOC-02 | `extractText` | System | Worker trích văn bản, chuyển PROCESSING | Must |
| UC-DOC-03 | `tagSubject` | Learner/System | Gắn `subject` (Toán/Lý/Hóa/Anh...), `grade_level`, gợi ý AI | Must |
| UC-DOC-04 | `listMyDocuments` | Learner | Liệt kê + lọc theo môn/trạng thái | Must |
| UC-DOC-05 | `searchDocuments` | Learner | Tìm full-text + semantic trên tài liệu của mình | Must |
| UC-DOC-06 | `viewDocument` | Learner | Xem chi tiết + preview + chunks READY | Must |
| UC-DOC-07 | `renameDocument` | Learner | Đổi `title` hiển thị | Should |
| UC-DOC-08 | `archiveDocument` | Learner | ARCHIVED khi hết kỳ, ẩn khỏi hỏi AI mặc định | Should |
| UC-DOC-09 | `restoreDocument` | Learner | ARCHIVED → READY | Should |
| UC-DOC-10 | `softDeleteDocument` | Learner | DELETED, giữ 30 ngày | Must |
| UC-DOC-11 | `purgeDocument` | System | Xóa cứng sau 30 ngày + xóa vector | Must |
| UC-DOC-12 | `checkDocQuota` | System | Đếm 10 TL / 50MB Free trước upload | Must |
| UC-DOC-13 | `retryProcessing` | Learner/System | Thử lại FAILED tối đa 3 lần | Should |
| UC-DOC-14 | `removeViolatingDoc` | Admin | Gỡ tài liệu vi phạm bản quyền/đề thi lậu | Must |
| UC-DOC-15 | `setDocPrivacy` | Learner | PRIVATE mặc định, bật/tắt link chia sẻ lớp | Could |

## Liên quan AI/RAG
- Chỉ `READY` mới được hỏi AI và sinh Quiz; `ARCHIVED/FAILED` bị loại khỏi retriever.
- Mỗi doc chia `chunks(800 tokens, overlap 100)` + `embedding`, lưu `doc_chunks`.

## Phi chức năng
- Upload p95 < 3s (file 20MB), parse async < 2 phút; tìm kiếm < 600ms/10k docs.
- Quota Free: chặn 403 `DOC_QUOTA_EXCEEDED` / `STORAGE_QUOTA_EXCEEDED` kèm upsell Paid.
