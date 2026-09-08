# 08 — Ảnh Hưởng Liên Module: Tài Liệu Học Tập

| Module đích | Hướng | Chi tiết | API / Sự kiện |
|-------------|-------|----------|---------------|
| Xác thực & quota | Docs → Auth | Trừ quota 10 TL/50MB; hết quota upsell Paid 99k | `checkDocQuota(owner_id)` |
| Hỏi đáp AI (RAG) | Docs → AI | Chỉ READY PRIVATE của owner được retrieve + trích dẫn trang | `getReadyChunks(doc_ids)` |
| Quiz tự động | Docs → Quiz | Sinh Quiz từ chunks READY; đề thi lậu bị gỡ thì khóa Quiz liên quan | evt `DOC_TAKEDOWN` |
| Tóm tắt bài học | Docs → Summary | Tóm tắt theo chương/môn từ `subject` tags | `GET /docs/:id/chunks` |
| Lộ trình học | Docs → Progress | Đếm tài liệu/môn gợi ý lộ trình ôn thi | `countDocsBySubject` |
| Thanh toán | Docs ↔ Billing | Paid 500 TL/5GB; hết hạn → cấm upload mới, giữ READY cũ | evt `SUBSCRIPTION_EXPIRED` |
| Kiểm duyệt | Docs ↔ Moderation | Quét đề thi lậu/bản quyền → Admin takedown | evt `DOC_FLAGGED` |
| Thông báo | Docs → Notify | Báo parse xong/FAILED, nhắc dọn khi gần đầy 50MB | `notifyDocReady/Full` |

## Rủi ro lan truyền
- Worker parse tắc → hỏi AI thiếu ngữ liệu → cần hàng đợi riêng + ưu tiên Paid.
- Xóa doc đang dùng sinh Quiz → Quiz mất nguồn → chặn xóa khi có Quiz `DRAFT`, hoặc detach nguồn.
- Rò PRIVATE sang learner khác → kiểm tra `owner_id` ở retriever mỗi query.
