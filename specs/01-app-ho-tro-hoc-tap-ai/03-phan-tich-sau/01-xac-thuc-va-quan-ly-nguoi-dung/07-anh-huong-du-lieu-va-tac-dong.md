# 07 — Ảnh Hưởng Dữ Liệu & Tác Động: Xác Thực & Người Dùng

> Phạm vi: học tập + AI, không dùng khái niệm bán hàng/nhà hàng.

## 1. Thực thể chịu tác động

| Bảng | Trường mới/sửa | Tác động |
|------|----------------|----------|
| `users` | `role(GUEST/LEARNER/ADMIN)`, `plan(FREE/PAID)`, `status`, `oauth_sub`, `grade_level`, `subjects[]` | Trung tâm phân quyền hỏi AI, tạo Quiz |
| `sessions` | `refresh_hash`, `expires_at`, `revoked` | Thu hồi khi suspend/xóa |
| `audit_logs` | `actor, action, target_user, reason_code, created_at` | Truy vết khóa/mở, nâng gói |
| `quotas` | `ai_questions_day, docs_count, storage_mb, quiz_day` | Init Free: 20/ngày, 10 TL, 50MB, 3 Quiz/ngày |
| `subscriptions` | `plan_monthly/yearly, started_at, expires_at` | Đồng bộ ACTIVE_PAID |

## 2. Tác động CRUD
- **Create:** register tạo 1 `users` + 1 `quotas` + 1 audit `REGISTER`. Google login lần đầu auto-create tương tự.
- **Update:** `updateProfile` chỉ sửa `display_name/avatar/grade/subjects`, cấm sửa `role/plan` phía client.
- **Delete:** soft-delete; `sessions` revoke hết; tài liệu + lịch sử Quiz của learner giữ lại 30 ngày cho khôi phục.

## 3. Dung lượng & hiệu năng
- 100k learner ≈ `users` ~50MB + `audit_logs` ~2GB/năm (giữ 12 tháng). Index `email`, `oauth_sub`, `status`.
- Login p95 < 800ms nhờ cache `sessions` Redis 15p; refresh rotation chống race bằng `version` lạc quan.

## 4. Toàn vẹn & riêng tư
- PII (email, avatar) mã hóa transit TLS, hash password bcrypt; purge PII sau DELETED 30 ngày.
- Log hỏi AI không lưu nguyên văn câu nhạy cảm của học sinh quá 90 ngày ở Free.
- Múi giờ ghi log thống nhất `Asia/Ho_Chi_Minh`, tránh lệch reset quota 00:00.
