# 17 — Tổng Hợp: Xác Thực & Quản Lý Người Dùng

## 1. Bức tranh một trang
- Module cửa ngõ: Guest đăng ký email/Google → ACTIVE_FREE (20 câu AI/ngày, 10 TL, 50MB, 3 Quiz/ngày) → nâng Paid 99k/990k → ACTIVE_PAID; vi phạm → SUSPENDED, xin xóa → DELETED (purge 30 ngày). Mọi bước ghi audit.

## 2. Quyết định đã chốt
- Giữ email/password + Google OAuth; JWT 15p + refresh xoay 30d; lock 5 lần/15p; verify mềm (5 câu/ngày khi chưa verify).
- Giá Paid 99k/tháng, 990k/năm; reset quota 00:00 `Asia/Ho_Chi_Minh`; audit giữ 12 tháng.

## 3. Việc còn mở (chờ user)
- DEC-AUTH-02/05 chốt cứng trước Sprint 1; SSO trường học và gói học kỳ để backlog.
- Consent PHHS <13 tuổi cần mẫu pháp lý.

## 4. Sẵn sàng sang thiết kế
- Đủ API `POST /auth/*`, schema `users/sessions/audit_logs/quotas/subscriptions`, mã lỗi chuẩn.
- Rủi ro lớn nhất (spam AI, brute-force) đã có giảm thiểu; độ phủ 91% → PASS có điều kiện.
