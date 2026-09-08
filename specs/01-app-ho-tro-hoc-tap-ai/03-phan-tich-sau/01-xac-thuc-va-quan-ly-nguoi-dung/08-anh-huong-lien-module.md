# 08 — Ảnh Hưởng Liên Module: Xác Thực & Người Dùng

> Module gốc: Auth/User → các module học tập + AI tiêu thụ.

| Module đích | Hướng tác động | Chi tiết | API / Sự kiện |
|-------------|----------------|----------|---------------|
| Quản lý tài liệu học tập | Auth → Docs | Chỉ ACTIVE_* được upload; Free giới hạn 10 TL + 50MB; DELETED ẩn tài liệu | `GET /auth/me/quota`, evt `USER_SUSPENDED` |
| Hỏi đáp AI (RAG) | Auth → AI | Quota 20 câu/ngày Free; Paid mở rộng; SUSPENDED chặn 403 | `checkQuota(user_id)` |
| Quiz tự động | Auth → Quiz | Free 3 bộ/ngày; Paid không giới hạn hợp lý; lưu `created_by` | `canCreateQuiz(user_id)` |
| Lộ trình & tiến độ học | Auth → Progress | `grade_level/subjects` cá nhân hóa gợi ý bài học | `GET /users/me/profile` |
| Thanh toán gói Paid | Auth ↔ Billing | Upgrade/downgrade đổi `plan`; webhook hết hạn | evt `SUBSCRIPTION_EXPIRED` |
| Thông báo học tập | Auth → Notify | Gửi mail verify, reset pass, nhắc hết hạn Paid trước 7 ngày | `sendMail(type, user_id)` |
| Kiểm duyệt & an toàn AI | Auth ↔ Moderation | Spam AI → `reason_code=SPAM_AI` → suspend; kháng nghị mở lại | evt `USER_SUSPENDED/UNSUSPENDED` |
| Phân tích học tập (Admin) | Auth → Analytics | Đếm DAU/MAU Free vs Paid, tỉ lệ chuyển đổi 99k/990k | `user_id, plan, status` ẩn danh |

## Rủi ro lan truyền
- Auth sập → toàn bộ hỏi AI, Quiz, tài liệu tê liệt → cần cache JWT verify local + fallback read-only.
- Sai `plan` → Free dùng lậu tính năng Paid (tóm tắt dài, Quiz nâng cao) → kiểm tra plan ở server mỗi request AI.
- Xóa user không revoke session → token cũ vẫn hỏi AI → bắt buộc revoke toàn bộ `sessions` khi SUSPENDED/DELETED.
