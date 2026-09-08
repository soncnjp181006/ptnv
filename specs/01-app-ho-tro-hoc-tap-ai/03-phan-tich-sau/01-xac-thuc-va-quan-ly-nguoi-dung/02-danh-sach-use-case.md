# 02 — Danh Sách Use Case: Xác Thực & Quản Lý Người Dùng

> Actor chính: `Guest`, `LearnerFree`, `LearnerPaid`, `Admin`, `System`

## 1. Bảng Use Case

| ID | Tên Use Case | Actor | Mô tả | Ưu tiên |
|----|--------------|-------|-------|----------|
| UC-AUTH-01 | `registerWithEmail` | Guest | Đăng ký bằng email + password, nhận `ACTIVE_FREE`, gửi mail verify | Must |
| UC-AUTH-02 | `loginWithEmail` | Guest | Đăng nhập email/password, cấp `access_token` + `refresh_token` | Must |
| UC-AUTH-03 | `loginWithGoogle` | Guest | Đăng nhập OAuth Google, liên kết `oauth_sub` | Must |
| UC-AUTH-04 | `verifyEmail` | LearnerFree | Kích hoạt email qua token 24h | Must |
| UC-AUTH-05 | `refreshSession` | LearnerFree/Paid | Xoay vòng refresh token, thu hồi token cũ | Must |
| UC-AUTH-06 | `logout` | Learner | Thu hồi session hiện tại | Must |
| UC-AUTH-07 | `forgotResetPassword` | Guest | Quên mật khẩu: gửi link reset 30 phút | Must |
| UC-AUTH-08 | `updateProfile` | Learner | Sửa `display_name`, `avatar_url`, `grade_level`, `subjects` | Should |
| UC-AUTH-09 | `upgradeToPaid` | LearnerFree | Nâng cấp `ACTIVE_FREE → ACTIVE_PAID` (99k/tháng, 990k/năm) | Must |
| UC-AUTH-10 | `downgradeToFree` | System | Hết hạn Paid → về Free, giữ dữ liệu học tập | Must |
| UC-AUTH-11 | `suspendUser` | Admin | Khóa `→ SUSPENDED` do vi phạm / spam AI | Must |
| UC-AUTH-12 | `unsuspendUser` | Admin | Mở khóa `SUSPENDED → ACTIVE_*` | Must |
| UC-AUTH-13 | `softDeleteUser` | Learner/Admin | Xóa mềm `→ DELETED`, giữ 30 ngày | Should |
| UC-AUTH-14 | `viewAuditLog` | Admin | Tra cứu `audit_logs` theo user / action / khoảng ngày | Should |
| UC-AUTH-15 | `linkGoogleAccount` | Learner | Liên kết thêm Google vào tài khoản email có sẵn | Could |

## 2. Liên quan quota freemium
- Sau `registerWithEmail` / `loginWithGoogle`, `QuotaService.initQuota(user_id)` tạo quota Free: 20 câu AI/ngày, 10 tài liệu, 50MB, 3 bộ Quiz/ngày.
- `upgradeToPaid` gọi `BillingService` rồi mở quota không giới hạn hợp lý + lịch sử thanh toán.

## 3. Yêu cầu phi chức năng
- `login` p95 < 800ms, chịu 500 concurrent; rate-limit 10 req/phút/IP cho login.
- Mật khẩu tối thiểu 8 ký tự, có chữ + số; khóa 15 phút sau 5 lần sai.
- Mọi UC-AUTH-11/12/13 bắt buộc ghi audit với `reason_code`.

## 4. Đầu ra cho thiết kế
- API: `POST /auth/register`, `POST /auth/login`, `POST /auth/google`, `POST /auth/refresh`, `PATCH /users/me`, `POST /admin/users/:id/suspend`.
- Bảng: `users(id, email, password_hash, oauth_sub, role, status, failed_attempts)`, `sessions`, `audit_logs`.
