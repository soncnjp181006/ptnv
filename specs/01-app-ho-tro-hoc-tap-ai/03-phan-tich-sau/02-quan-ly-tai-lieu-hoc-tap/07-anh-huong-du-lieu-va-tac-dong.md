# 07 — Ảnh Hưởng Dữ Liệu & Tác Động: Tài Liệu Học Tập

## 1. Thực thể

| Bảng | Trường | Tác động |
|------|--------|----------|
| `documents` | `owner_id, title, file_type, size_mb, subject, grade_level, status, visibility, storage_key` | Lõi quản lý vở/bài giảng của learner |
| `doc_chunks` | `document_id, chunk_index, content, page_no, embedding(1536)` | Ngữ liệu RAG hỏi AI + trích dẫn trang |
| `doc_logs` | `document_id, stage, error_code, duration_ms` | Theo dõi parse/timeout |
| `quotas` | `docs_count, storage_used_mb` | Trừ/cộng khi upload/xóa/purge |
| `audit_logs` | `takedown, delete, restore` | Vết gỡ bản quyền |

## 2. CRUD & quota
- **Create:** upload +1 `docs_count`, +size; fail parse không hoàn quota tới khi xóa.
- **Archive:** không hoàn quota (vẫn chiếm 50MB) — chỉ delete/purge mới hoàn.
- **Purge:** xóa S3 + `doc_chunks` + vector, hoàn quota, giữ `doc_logs` ẩn danh 90 ngày.

## 3. Dung lượng & hiệu năng
- 10k learner Free × 50MB ≈ 500GB S3; Paid 5GB × 1k ≈ 5TB. Lifecycle S3 Intelligent-Tiering.
- `doc_chunks` ~1 PDF 100 trang ≈ 300 chunks; index HNSW pgvector, search p95 < 600ms.
- Worker parse scale ngang (queue SQS/BullMQ), timeout 5p, retry 3 + DLQ.

## 4. Toàn vẹn học thuật
- Chunk giữ `page_no` để AI trích dẫn "trang 12" chống bịa nguồn.
- File virus-scan trước S3; DOCX macro strip; PDF giới hạn 1000 trang.
