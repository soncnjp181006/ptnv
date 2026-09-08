# 01 — Mô Hình Quy Trình Nghiệp Vụ: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Module: `05-goi-dich-vu-thanh-toan-va-han-muc` | Múi giờ: `Asia/Ho_Chi_Minh` | Tiền tệ: `VND` | Cắt ngày quota: `00:00`

## 1. Mục đích
- Mô tả vòng đời gói dịch vụ: dùng Free ➔ mua/gia hạn Paid ➔ kiểm soát quota ➔ hết hạn/grace ➔ hủy/hoàn tiền.
- Làm đầu vào cho tính tiền, webhook, đối soát và thông báo.

## 2. L0 — Bối cảnh (Context)

```mermaid
flowchart TB
    U["fa:fa-user User Free/Paid"] -->|"1. Mua, gia han, huy goi"| S["fa:fa-credit-card He thong Goi & Thanh toan"]
    S -->|"2. Kiem tra quota truoc moi tac vu AI"| A["fa:fa-robot Module Gia su AI / Quiz"]
    S -->|"3. Tao yeu cau thu tien"| G["fa:fa-bank Cong thanh toan MoMo/VNPay/Stripe"]
    G -->|"4. Webhook ket qua thanh toan"| S
    S -->|"5. Thong bao quota, het han, bien dong"| N["fa:fa-bell Module Thong bao"]
```

## 3. L1 — Quy trình chính

```mermaid
flowchart TB
    Start((Bat dau)) -->|"1. Nguoi dung chon goi"| P1["Chon goi Free, Month99k, Year990k"]
    P1 -->|"2. Tao don hang pending"| P2["Tao SubscriptionOrder amount VND"]
    P2 -->|"3. Dieu huong sang cong thanh toan"| P3["Redirect MoMo/VNPay/Stripe"]
    P3 -->|"3A — Thanh toan thanh cong"| P4["Kich hoat Paid, cong quota"]
    P3 -->|"3B — Thanh toan that bai, huy"| P5["Giu Free, don TIMEOUT/CANCELLED"]
    P4 -->|"4. Su dung, tru quota hang ngay"| P6["Enforce quota AI, tai lieu, Quiz"]
    P6 -->|"5. Don den han, gia han that bai"| P7["Grace period 3 ngay roi ha Free"]
```

## 4. L2 — Chi tiết mua gói + webhook + đối soát

```mermaid
flowchart TB
    A1((User bam Mua goi)) -->|"1. Chon chu ky va kenh"| A2["Chon planId, cycle, channel"]
    A2 -->|"2. Khoa chong mua trung idempotencyKey"| A3["Kiem tra subscription dang ACTIVE, PENDING"]
    A3 -->|"3A — Dang co PENDING chua het han"| A4["Tra ve paymentUrl cu, khong tao don moi"]
    A3 -->|"3B — Duoc phep mua moi"| A5["Tao Order PENDING + payment Intent"]
    A5 -->|"4. Redirect cong thanh toan"| A6["Cho webhook capture"]
    A6 -->|"5. Nhan webhook co chu ky"| A7["Verify signature, idempotent ingest"]
    A7 -->|"6A — CAPTURED"| A8["ACTIVE goi, receipt, cong quota"]
    A7 -->|"6B — FAILED, EXPIRED"| A9["Danh dau Order that bai, giu Free"]
    A7 -->|"6C — Khong xac dinh, timeout"| A10["UNKNOWN, job doi soat reconcile"]
    A10 -->|"7. Reconcile voi gateway"| A8
    A10 -->|"7B — Van UNKNOWN qua SLA"| A9
    A8 -->|"8. Lên lich gia han, nhac het han"| A11["Scheduler + notification T-7, T-3, T-1 ngay"]
```

## 5. Quy ước chung
- `cutoffTime = 00:00 Asia/Ho_Chi_Minh`; quota ngày reset lúc cutoff.
- `gracePeriodDays = 3` sau `expiresAt`, trong grace giữ quyền Paid nhưng chặn gia hạn chồng.
- Mọi số tiền lưu `amountVnd` (số nguyên, đơn vị đồng), không dùng số thập phân.
- Mọi tạo đơn mua dùng `Idempotency-Key` của client + `orderCode` duy nhất phía server.
