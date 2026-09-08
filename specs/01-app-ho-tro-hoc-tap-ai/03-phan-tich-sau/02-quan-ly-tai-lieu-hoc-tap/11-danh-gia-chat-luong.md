# 11 — Đánh Giá Chất Lượng: Tài Liệu Học Tập

| Tiêu chí | Điểm/10 | Nhận xét |
|----------|---------|----------|
| Đầy đủ upload→READY→xóa | 9 | Kín vòng đời UPLOADING→DELETED |
| Gắn AI/RAG thực chất | 9 | Chunks + page_no + retriever PRIVATE |
| Quota Free rõ | 9 | 10 TL/50MB/20MB-file nhất quán auth |
| Xử lý công thức Toán | 7 | Mới giữ text, ảnh công thức còn [figure] |
| Tìm kiếm học liệu | 8 | Đủ full-text + vector cơ bản |
| Bản quyền đề thi | 7 | Mới gỡ tay, thiếu quét tự động |
| **Tổng** | **49/60 ≈ 82%** | Đạt gate ≥75% |

## Slop-flags
- Không cho hỏi AI trên FAILED/ARCHIVED mặc định — tránh bịa kiến thức.
- Không public tài liệu học sinh khi chưa consent.
- Không cộng dồn quota khi archive — chỉ delete mới hoàn.

## Cải tiến
- A1: OCR nhẹ cho PDF scan đề thi (Sprint 2).
- A2: Rerank + highlight trang nguồn trong trích dẫn.
- A3: Mẫu tag `subject/grade_level` chuẩn Bộ GD (Toán 9, Vật lý 12...).
