# 14 — Lộ Trình Thực Hiện: Xác Thực & Người Dùng

> 3 chặng, gắn mốc khai giảng và ôn thi.

## Giai đoạn 1 (Tuần 1–2): Auth lõi
- `register/login/refresh/logout`, hash bcrypt, JWT 15p/30d, rate-limit.
- Quota Free init: 20 câu AI/ngày, 10 TL, 50MB, 3 Quiz/ngày.
- Tiêu chí xong: login p95 < 800ms, test lock 5 lần pass.

## Giai đoạn 2 (Tuần 3): OAuth + hồ sơ + Paid hook
- Google OAuth `loginWithGoogle` + `linkGoogleAccount`, verify/reset mail.
- `updateProfile` (`grade_level`, `subjects`) + gợi ý môn sau đăng ký.
- `upgradeToPaid` 99k/990k + cron downgrade 00:05 `Asia/Ho_Chi_Minh`.

## Giai đoạn 3 (Tuần 4): Quản trị + cứng hóa
- `suspend/unsuspend/softDelete` + `viewAuditLog` + export cơ bản.
- Reuse detection, logout xa, cảnh báo phiên lạ, captcha sau 3 lần sai.
- Pen-test nội bộ + review audit 12 tháng.

## Phụ thuộc
- Chờ DEC-AUTH-02/05 trước Giai đoạn 2; Billing webhook trước upgrade Paid.
- Không chặn module tài liệu: dùng mock `GET /auth/me` trong lúc auth chưa xong.
