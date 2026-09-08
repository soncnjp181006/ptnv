# 10 — Ma Trận Độ Phủ Nghiệp Vụ: Tài Liệu Học Tập

| Nhóm | Cần | Phủ | % | Ghi chú |
|------|-----|-----|---|---------|
| Upload + quota Free 50MB | 6 | 6 | 100% | Đủ 4 đuôi PDF/DOCX/TXT/MD |
| Trích xuất + chunks RAG | 6 | 5 | 83% | Thiếu OCR scan |
| Gắn thẻ môn/khối | 4 | 4 | 100% | AI gợi ý + tay |
| Tìm kiếm học liệu | 4 | 3 | 75% | Semantic Sprint 1 cơ bản |
| Lưu trữ/khôi phục/xóa | 5 | 5 | 100% | Purge 30 ngày |
| Riêng tư + bản quyền | 4 | 3 | 75% | Quét tự động dời S2 |
| **Tổng** | **29** | **26** | **90%** | Đủ gate |

## Lỗ hổng & bù đắp
- G1 OCR PDF scan đề thi → bù: báo FAILED rõ + hướng dẫn gõ lại/chụp nét, backlog OCR Sprint 2.
- G2 Semantic search sâu → Sprint 1 dùng pgvector top-k đơn giản, S2 rerank.
- G3 Thư viện chung → giữ PRIVATE, chia sẻ link lớp Sprint 2.

## Gate: 90% → PASS có điều kiện.
