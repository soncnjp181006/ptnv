# 04 — Mô Hình Trạng Thái: UserAccount

> Vòng đời chốt: `GUEST → ACTIVE_FREE → ACTIVE_PAID → SUSPENDED / DELETED`. Múi giờ `Asia/Ho_Chi_Minh`.

## 1. Sơ đồ trạng thái

```mermaid
flowchart TB
    GUEST[GUEST: chưa đăng ký] -->|"1. register / google login"| AF[ACTIVE_FREE]
    AF -->|"2. upgrade Paid 99k/990k"| AP[ACTIVE_PAID]
    AP -->|"3. hết hạn gói"| AF
    AF -->|"4. vi phạm / spam"| SUS[SUSPENDED]
    AP -->|"5. vi phạm / gian lận thanh toán"| SUS
    SUS -->|"6. Admin mở khóa"| AF
    AF -->|"7. yêu cầu xóa"| DEL[DELETED: soft-delete 30 ngày]
    AP -->|"7. yêu cầu xóa"| DEL
    SUS -->|"8. xóa sau kỷ luật"| DEL
    DEL -->|"9. quá 30 ngày: purge"| PURGED[(PURGED)]
```

## 2. Bảng chuyển trạng thái

| Từ → Đến | Sự kiện `event` | Điều kiện guard | Hành động |
|----------|-----------------|-----------------|-----------|
| GUEST → ACTIVE_FREE | `REGISTER_SUCCESS` | Email/OAuth hợp lệ | Tạo `users`, init quota Free (20 câu AI/ngày, 10 TL, 50MB) |
| ACTIVE_FREE → ACTIVE_PAID | `SUBSCRIBE_SUCCESS` | Thanh toán OK | Tạo `subscriptions`, mở quota |
| ACTIVE_PAID → ACTIVE_FREE | `SUBSCRIPTION_EXPIRED` | Cron 00:05 | Giữ tài liệu, chặn tính năng Paid |
| * → SUSPENDED | `SUSPEND` | Admin + `reason_code` | Thu hồi JWT/refresh, chặn login 403 |
| SUSPENDED → ACTIVE_* | `UNSUSPEND` | Admin duyệt | Khôi phục trạng thái trước đó |
| * → DELETED | `REQUEST_DELETE` | Xác nhận 2 bước | Ẩn profile, giữ audit 12 tháng |
| DELETED → PURGED | `PURGE_JOB` | Sau 30 ngày | Xóa PII, giữ log ẩn danh |

## 3. Quy tắc
- `PENDING_VERIFY` là cờ phụ của ACTIVE_FREE, không phải state độc lập: `email_verified=false` thì giới hạn 5 câu AI/ngày.
- `failed_attempts >= 5` không đổi state mà khóa tạm `locked_until = now + 15p`.
- Mọi chuyển state ghi `audit_logs(prev_status, new_status, actor, reason)`.
- Không cho `DELETED → ACTIVE_*` (phải đăng ký lại email mới).

## 4. Liên quan phiên học tập
- Khi SUSPENDED/DELETED: `QuotaService` khóa hỏi AI, `DocumentService` chỉ đọc (SUSPENDED) hoặc ẩn (DELETED).
- Khi ACTIVE_PAID hết hạn giữa buổi Quiz AI: cho hoàn thành bài đang làm, bài mới áp quota Free.
