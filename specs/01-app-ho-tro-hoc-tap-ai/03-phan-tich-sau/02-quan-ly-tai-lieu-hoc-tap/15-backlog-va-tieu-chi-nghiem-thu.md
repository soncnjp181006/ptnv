# 15 — Backlog & Tiêu Chí Nghiệm Thu: Tài Liệu Học Tập

| ID | Story học tập | Ưu tiên | Acceptance |
|----|---------------|---------|------------|
| US-DOC-01 | Là học sinh, tôi nộp PDF giáo trình để hỏi AI | Must | 201 UPLOADING → READY < 2p, trừ quota |
| US-DOC-02 | Là learner Free, tôi bị chặn khi quá 50MB | Must | 403 + gợi ý Paid 99k |
| US-DOC-03 | Là learner, tôi xem tag môn AI gợi ý | Must | Gợi ý đúng ≥85%, 1-chạm xác nhận |
| US-DOC-04 | Là learner, tôi tìm "Vi-ét" trong vở mình | Must | Top-3 kèm trang, <600ms |
| US-DOC-05 | Là learner, tôi hỏi AI chỉ trên tài liệu READY | Must | Câu trả lời có trích dẫn trang |
| US-DOC-06 | Là learner, tôi cất tài liệu hết kỳ | Should | ARCHIVED loại khỏi RAG mặc định |
| US-DOC-07 | Là learner, tôi xóa nhầm lấy lại | Must | Khôi phục trong 30 ngày |
| US-DOC-08 | Là Admin, tôi gỡ đề lậu | Must | DELETED + audit + báo learner |

## DoD
- API OpenAPI `POST /docs`, `GET /docs/search`; unit ≥75%, e2e upload→READY→hỏi AI pass.
- Không rò PRIVATE (test cross-owner 403); log giờ HCM; S3 lifecycle + purge đúng 30 ngày.
- Tổng ~20 point, 2 dev × 3–4 tuần.
