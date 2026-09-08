# 03 — Ma Trận Kịch Bản: Xác Thực & Quản Lý Người Dùng

> Bao phủ Main / Alternative / Exception cho từng UC-AUTH.

## 1. UC-AUTH-01 `registerWithEmail`

| Kịch bản | Điều kiện | Luồng | Kết quả |
|----------|-----------|-------|---------|
| Main | Email chưa tồn tại, password hợp lệ | Nhập form → validate → hash bcrypt → tạo ACTIVE_FREE → gửi mail verify | 201 + quota Free khởi tạo |
| Alt-01 | Email đã tồn tại chưa verify | Gửi lại mail verify, không tạo mới | 200 + `EMAIL_ALREADY_PENDING` |
| Exc-01 | Email sai định dạng | Chặn client + server validate | 400 `INVALID_EMAIL` |
| Exc-02 | Password < 8 ký tự | Từ chối | 400 `WEAK_PASSWORD` |

## 2. UC-AUTH-02/03 Đăng nhập

| Kịch bản | Điều kiện | Luồng | Kết quả |
|----------|-----------|-------|---------|
| Main-email | Đúng password, status ACTIVE_* | Check hash → reset failed → cấp JWT 15p + refresh 30d | 200 + session |
| Main-google | `id_token` hợp lệ | Verify Google → map `oauth_sub` → login hoặc auto-register Free | 200 + session |
| Alt-01 | Chưa verify email | Cho login nhưng chặn hỏi AI quá 5 câu/ngày tới khi verify | 200 + `EMAIL_UNVERIFIED` flag |
| Exc-01 | Sai 5 lần | `failed_attempts=5` → lock 15p | 429 `ACCOUNT_LOCKED` |
| Exc-02 | SUSPENDED | Chặn + hiện lý do + link kháng nghị | 403 `ACCOUNT_SUSPENDED` |
| Exc-03 | Google token hết hạn | Yêu cầu login lại | 401 `OAUTH_EXPIRED` |

## 3. UC-AUTH-07 Quên mật khẩu

| Kịch bản | Điều kiện | Luồng | Kết quả |
|----------|-----------|-------|---------|
| Main | Email tồn tại | Gửi link reset 30p (1 link/active) → đổi pass → thu hồi mọi session | 200 |
| Exc-01 | Email không tồn tại | Trả 200 giả (chống enumerate) nhưng không gửi mail | 200 + log nội bộ |

## 4. UC-AUTH-09/10 Nâng / hạ gói

| Kịch bản | Luồng | Kết quả |
|----------|-------|---------|
| Main-upgrade | Chọn Tháng 99k / Năm 990k → thanh toán OK → ACTIVE_PAID + mở quota | 200 + `subscription` |
| Exc-pay-fail | Thanh toán lỗi → giữ ACTIVE_FREE | 402 `PAYMENT_FAILED` |
| Main-downgrade | Cron hết hạn → ACTIVE_FREE, giữ tài liệu + lịch sử Quiz | Job chạy 00:05 Asia/Ho_Chi_Minh |

## 5. UC-AUTH-11/12/13 Admin & xóa

| Kịch bản | Luồng | Kết quả |
|----------|-------|---------|
| Suspend | Admin nhập `reason_code` (SPAM_AI, ABUSE, FRAUD) → SUSPENDED → thu hồi token | 200 + audit |
| Unsuspend | Admin mở → về trạng thái trước đó | 200 + audit |
| Soft-delete | Learner xác nhận → DELETED + ẩn hồ sơ, giữ audit 12 tháng | 200, purge sau 30 ngày |

## 6. Ma trận bao phủ
- Tổng 15 UC, 28 kịch bản: 8 main, 9 alternative, 11 exception. Mọi exception đều có mã lỗi tiếng Anh + message tiếng Việt.
