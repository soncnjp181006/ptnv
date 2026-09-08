# 13 — Khả Thi & Rủi Ro: Xác Thực & Người Dùng

## 1. Khả thi kỹ thuật
- Stack đề xuất: `AuthService` Node/Nest + Postgres `users/sessions` + Redis session + Google OAuth2 — đội đã quen, khả thi cao.
- Bcrypt + JWT rotation + JWKS verify đều là thư viện chuẩn, không R&D.
- Cron downgrade Paid→Free chạy 00:05 `Asia/Ho_Chi_Minh`, idempotent theo `subscription_id`.

## 2. Ma trận rủi ro

| ID | Rủi ro | Xác suất / Tác động | Giảm thiểu |
|----|--------|---------------------|------------|
| R-AUTH-01 | Spam nick ảo bào quota AI Free | Cao / Cao | Verify mail giới hạn 5 câu/ngày, rate-limit IP, captcha |
| R-AUTH-02 | Dò mật khẩu phòng máy chung | TB / Cao | Lock 5 lần/15p, cảnh báo đăng nhập lạ, logout xa |
| R-AUTH-03 | Reuse refresh token bị đánh cắp | Thấp / Cao | Rotation + reuse detection → revoke all |
| R-AUTH-04 | Google OAuth downtime khai giảng | Thấp / Cao | Fallback email/password + trang trạng thái |
| R-AUTH-05 | Sai plan → Free xài lậu Paid | TB / TB | Check plan server-side mỗi request AI/Quiz |
| R-AUTH-06 | Khiếu nại xóa dữ liệu học sinh | TB / TB | Soft-delete 30 ngày + purge PII đúng hẹn |

## 3. Khả thi lịch & chi phí
- 2 dev × 2 tuần cho auth lõi + OAuth + audit; 0.5 tuần cho billing hook 99k/990k.
- Chi phí: Google OAuth miễn phí; mail OTP ~2–5tr/tháng/100k user; SMS không dùng.

## 4. Kết luận
- GO với điều kiện: chốt DEC-AUTH-02 (verify mềm) + captcha trước code login.
