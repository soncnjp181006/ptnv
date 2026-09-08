# 13 — Khả Thi & Rủi Ro: Tài Liệu Học Tập

## 1. Khả thi
- `DocumentService` + S3 + BullMQ worker + pgvector: stack phổ biến, khả thi cao.
- Parse PDF (`pdf-parse`), DOCX (`mammoth`), TXT/MD native — không R&D; embed API có sẵn.
- Virus-scan (ClamAV) + strip macro DOCX trước parse.

## 2. Rủi ro

| ID | Rủi ro | P / I | Giảm thiểu |
|----|--------|-------|------------|
| R-DOC-01 | Nghẽn worker mùa ôn thi | Cao / Cao | Autoscale worker, ưu tiên Paid, timeout 5p + DLQ |
| R-DOC-02 | PDF scan mờ → FAILED hàng loạt | Cao / TB | Thông báo rõ + backlog OCR Sprint 2 |
| R-DOC-03 | Đề thi lậu/bản quyền | TB / Cao | Takedown tay S1, quét hash S2, audit |
| R-DOC-04 | Rò PRIVATE sang user khác | Thấp / Cao | Lọc `owner_id` ở retriever + test 403 |
| R-DOC-05 | Công thức Toán vỡ khi parse | TB / TB | Giữ LaTeX, ảnh → [figure] + cảnh báo |
| R-DOC-06 | Vượt chi phí S3/embed Free | TB / TB | Trần 50MB + 20MB/file, cache embed |

## 3. Lịch & chi phí
- 2 dev × 3 tuần: upload/quota (1w) + worker/RAG (1.5w) + search/xóa (0.5w).
- S3 ~500GB Free + embed ~30M tokens/tháng/10k user — trong ngân sách freemium.

## 4. Kết luận: GO, chốt DEC-DOC-01/02/03 trước code worker.
