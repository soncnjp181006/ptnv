# 09 — Ma Trận Truy Vết: Tài Liệu Học Tập

| Req (nhu cầu học sinh) | Use Case | Quy tắc | Test | Trạng thái |
|------------------------|----------|---------|------|------------|
| Nộp giáo trình PDF ôn thi | UC-DOC-01 upload | BR-DOC-01/02/03 | TC-DOC-01-01 PDF 5MB Free OK | Covered |
| Hỏi AI trên bài giảng | UC-DOC-02 + RAG | BR-DOC-04/05 | TC-DOC-02-02 chunks có page_no | Covered |
| Phân loại Toán/Lý theo khối | UC-DOC-03 tag | BR-DOC-05 | TC-DOC-03-01 gợi ý đúng môn 85% | Covered |
| Tìm lại công thức Vi-ét | UC-DOC-05 search | BR-DOC-04 | TC-DOC-05-01 top-3 có trang | Covered |
| Cất tài liệu hết kỳ | UC-DOC-08 archive | — | TC-DOC-08-01 loại khỏi RAG | Covered |
| Xóa nhầm lấy lại | UC-DOC-10/11 | BR-DOC-08 | TC-DOC-10-01 khôi phục 30 ngày | Covered |
| Quyền riêng tư vở ghi | UC-DOC-15 privacy | BR-DOC-06 | TC-DOC-15-01 user khác 403 | Covered |
| Gỡ đề lậu | UC-DOC-14 takedown | BR-DOC-07 | TC-DOC-14-01 audit takedown | Partial |

## Độ bao phủ
- 8 req → 10 UC → 8 BR → 8 TC: Covered 7/8, Partial 1 (quét bản quyền tự động dời Sprint 2, Sprint 1 làm tay).
