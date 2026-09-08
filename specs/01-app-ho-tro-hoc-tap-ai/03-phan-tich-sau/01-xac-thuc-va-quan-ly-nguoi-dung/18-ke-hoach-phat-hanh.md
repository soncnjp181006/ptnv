# 18 — Kế Hoạch Phát Hành: Xác Thực & Người Dùng

## 1. Chiến lược
- Phát hành cờ tính năng: `auth_v1` (email) → `google_oauth` → `paid_upgrade` → `admin_moderation`.
- Canary 5% learner khối 10 trước khai giảng, rollback bằng tắt cờ < 5 phút.

## 2. Checklist Go/No-Go

| Hạng mục | Tiêu chí | Chủ trì |
|----------|----------|---------|
| Bảo mật | Bcrypt, rotation, reuse detection pass pentest | Backend |
| Quota | Free 20 AI/ngày + Paid 99k/990k đúng | Billing/QA |
| Mail | Verify/reset đến < 60s, không vào spam edu | DevOps |
| Audit | 100% suspend/upgrade có log đúng giờ HCM | QA |
| Hiệu năng | 500 concurrent p95 < 800ms | QA |
| Rollback | Script revoke + tắt cờ OAuth/Paid | DevOps |

## 3. Sau phát hành
- Theo dõi login success, lock oan, verify rate, chuyển đổi Free→Paid 7 ngày.
- Trực khai giảng: cảnh báo lock tăng đột biến (phòng máy chung) để mở captcha thay vì lock cứng.
