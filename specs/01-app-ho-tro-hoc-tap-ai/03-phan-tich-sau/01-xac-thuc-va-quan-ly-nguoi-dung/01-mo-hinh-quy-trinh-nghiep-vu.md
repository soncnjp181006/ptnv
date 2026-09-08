# 01 — Mô Hình Quy Trình Nghiệp Vụ: Xác Thực & Quản Lý Người Dùng

> Module: `auth-user-management` | Múi giờ: `Asia/Ho_Chi_Minh` | Vai trò: `Guest`, `LearnerFree`, `LearnerPaid`, `Admin`

## 1. Mục đích
- Chuẩn hóa luồng đăng ký, đăng nhập (email/password + Google OAuth), quản lý hồ sơ, phân vai, khóa/mở tài khoản và audit.
- Làm đầu vào cho thiết kế API `AuthService`, bảng `users`, `sessions`, `audit_logs`.

## 2. L0 — Sơ đồ ngữ cảnh (Context)

```mermaid
flowchart TB
    G[Guest / Learner] -->|"1. Gửi yêu cầu đăng ký / đăng nhập"| AUS[AuthService]
    AUS -->|"2. Ủy quyền OAuth"| GG[Google OAuth2]
    GG -->|"3. Trả id_token + profile"| AUS
    AUS -->|"4. Cấp session JWT + refresh_token"| G
    AUS -->|"5. Ghi audit đăng nhập / phân vai"| ADB[(audit_logs)]
    AD[Admin] -->|"6. Khóa / mở / phân vai"| AUS
    AUS -->|"7. Áp quota Free: 20 câu AI/ngày"| QS[QuotaService]
```

## 3. L1 — Quy trình lõi (Core Process)

```mermaid
flowchart TB
    A[Start: Guest truy cập] -->|"1. Chọn đăng ký email hoặc Google"| B{Chọn kênh}
    B -->|"2A — Email/password"| C[Validate email + password >=8 ký tự]
    B -->|"2B — Google OAuth"| D[Redirect Google consent + callback id_token]
    C -->|"3. Tạo UserAccount ACTIVE_FREE + hash bcrypt"| E[Lưu users + gửi email verify]
    D -->|"4. Map sub Google → users.oauth_sub"| E
    E -->|"5. Đăng nhập: cấp access_token 15p + refresh_token 30d"| F[LearnerFree dùng AI với quota]
    F -->|"6. Nâng cấp Paid 99k/tháng hoặc 990k/năm"| G[ACTIVE_PAID]
    G -->|"7. Ghi audit + đồng bộ quota"| H[End]
```

## 4. L2 — Ngoại lệ & phục hồi (Exception & Recovery)

```mermaid
flowchart TB
    S[Login request] -->|"1. Kiểm tra email tồn tại"| C1{User tồn tại?}
    C1 -->|"2A — Có"| C2{Kiểm tra password / id_token}
    C1 -->|"2B — Không"| R1[Trả 404 + gợi ý đăng ký]
    C2 -->|"3A — Có: hợp lệ"| C3{Tài khoản SUSPENDED?}
    C2 -->|"3B — Không: sai"| R2[Tăng failed_attempts + lock sau 5 lần 15p]
    C3 -->|"4A — Có: bị khóa"| R3[Trả 403 + hướng dẫn kháng nghị]
    C3 -->|"4B — Không: bình thường"| OK[Cấp token + reset failed_attempts]
    R2 -->|"5. Ghi audit LOGIN_FAILED"| AL[(audit_logs)]
    OK -->|"6. Ghi audit LOGIN_SUCCESS"| AL
```

## 5. Ghi chú nghiệp vụ
- Password hash `bcrypt(cost=12)`, verify email qua token 24h, refresh rotation chống reuse.
- Google OAuth là đề xuất mặc định; vẫn giữ email/password cho học sinh không có Gmail edu.
- Mọi chuyển trạng thái `GUEST → ACTIVE_FREE → ACTIVE_PAID → SUSPENDED / DELETED` đều ghi `audit_logs(actor, action, target, timestamp)`.
- Xóa tài khoản là soft-delete 30 ngày trước khi purge, giữ audit 12 tháng.
