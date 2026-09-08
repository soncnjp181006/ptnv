# 04 — Mô Hình Trạng Thái: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Hai máy trạng thái: `Subscription` và `SubscriptionOrder`. Múi giờ `Asia/Ho_Chi_Minh`.

## 4.1 Subscription (gói của user)

```mermaid
stateDiagram-v2
    [*] --> FREE: dang ky moi
    FREE --> PENDING_PAYMENT: tao don mua
    PENDING_PAYMENT --> ACTIVE: webhook CAPTURED
    PENDING_PAYMENT --> FREE: FAILED/TIMEOUT/CANCELLED
    ACTIVE --> ACTIVE: gia han thanh cong (expiresAt += cycle)
    ACTIVE --> GRACE: qua expiresAt, autoRenew that bai
    GRACE --> ACTIVE: thanh toan trong 3 ngay
    GRACE --> FREE: het grace 3 ngay 00:00 D+4
    ACTIVE --> FREE: tat autoRenew + het han / refund toan phan
    FREE --> [*]
```

| Từ ➔ Đến | Sự kiện | Hành động |
| :--- | :--- | :--- |
| `FREE ➔ PENDING_PAYMENT` | `createOrder` | Khóa mua chồng theo `userId` |
| `PENDING_PAYMENT ➔ ACTIVE` | `paymentCaptured` | Set `planId`, `startedAt`, `expiresAt`, cộng quota |
| `ACTIVE ➔ GRACE` | `expiresAt passed` | Giữ quyền 3 ngày, gửi nhắc gia hạn |
| `GRACE ➔ FREE` | `graceExpired` | Hạ gói, reset quota Free, ghi timeline |
| `ACTIVE ➔ FREE` | `refundFull` | Thu hồi quyền Paid ngay lập tức |

## 4.2 SubscriptionOrder (đơn thanh toán)

```mermaid
stateDiagram-v2
    [*] --> PENDING: tao don
    PENDING --> CAPTURED: webhook capture hop le
    PENDING --> FAILED: gateway bao that bai
    PENDING --> TIMEOUT: qua 30 phut khong webhook
    PENDING --> CANCELLED: user huy truoc khi tra tien
    CAPTURED --> REFUNDED: refund toan phan
    CAPTURED --> PARTIAL_REFUNDED: refund mot phan
    FAILED --> PENDING: tao lai don moi (orderCode moi)
    TIMEOUT --> PENDING: tao lai don moi
```

## 4.3 Quy tắc chuyển đổi
- Chỉ `CAPTURED` mới được sinh `Receipt`; `FAILED/TIMEOUT/CANCELLED` không sinh receipt.
- `REFUNDED/PARTIAL_REFUNDED` là trạng thái cuối, không chuyển tiếp thêm.
- `UNKNOWN` không phải trạng thái lưu DB mà là cờ `reconcileStatus=UNKNOWN` trên đơn `PENDING`.
- Mọi chuyển đổi ghi `SubscriptionEventLog` gồm `fromStatus`, `toStatus`, `actor`, `reason`, `occurredAt`.
