# 07 — Ảnh Hưởng Dữ Liệu Và Tác Động: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Thực thể chính: `PlanCatalog`, `Subscription`, `SubscriptionOrder`, `Receipt`, `RefundRequest`, `QuotaLedger`, `WebhookEvent`.

## 7.1 Mô hình dữ liệu (rút gọn)
- `PlanCatalog(planId, name, cycle, priceVnd, aiPerDay, maxDocs, storageMb, quizPerDay)`.
- `Subscription(subId, userId, planId, status, startedAt, expiresAt, autoRenew, graceUntil)`.
- `SubscriptionOrder(orderId, orderCode UNIQUE, userId, planId, amountVnd, status, reconcileStatus, idempotencyKey, paymentUrl, gatewayRef)`.
- `Receipt(receiptId, orderId UNIQUE, amountVnd, issuedAt, invoiceNo)`.
- `RefundRequest(refundId, orderId, type FULL/PARTIAL, amountVnd, status, reason)`.
- `QuotaLedger(userId, date, aiUsed, aiLimit, quizUsed, quizLimit, docsUsed, storageMb)`.
- `WebhookEvent(eventId UNIQUE, gateway, orderCode, payload, signatureOk, receivedAt)`.

## 7.2 Tác động theo use case
| Use case | Tạo | Cập nhật | Đọc | Xóa |
| :--- | :--- | :--- | :--- | :--- |
| UC-502 mua gói | `SubscriptionOrder(PENDING)`, `WebhookEvent` | `Subscription FREE➔PENDING_PAYMENT` | `PlanCatalog` | Không |
| UC-503 gia hạn | `SubscriptionOrder` mới | `expiresAt += cycle` | `Subscription` | Không |
| UC-505 hoàn tiền | `RefundRequest` | `Order➔REFUNDED/PARTIAL`, `Subscription➔FREE` | `Receipt` | Không |
| UC-506 webhook | `WebhookEvent` | `Order➔CAPTURED/FAILED` | `SubscriptionOrder` | Không |
| UC-508 quota | `QuotaLedger` ngày mới | `aiUsed/quizUsed += 1` | `Subscription` (lấy limit) | Không |

## 7.3 Ràng buộc toàn vẹn
- `orderCode UNIQUE`; `eventId UNIQUE` (idempotency webhook).
- Chỉ 1 `Subscription ACTIVE` mỗi `userId`; chỉ 1 `Order PENDING` sống mỗi `userId` (partial unique index).
- `Receipt.orderId UNIQUE` — mỗi đơn CAPTURED đúng 1 receipt.
- `RefundRequest.amountVnd <= Order.amountVnd`; `PARTIAL` yêu cầu `0 < amount < paid`.
- `QuotaLedger(date)` theo ngày `Asia/Ho_Chi_Minh`, reset 00:00 bằng job idempotent (upsert theo `userId+date`).
- Không xóa cứng đơn/receipt/refund (giữ 5 năm phục vụ đối soát, thuế).

## 7.4 Từ điển trường quan trọng
| Trường | Kiểu | Ràng buộc | Ví dụ |
| :--- | :--- | :--- | :--- |
| `orderCode` | string(20) | UNIQUE, sinh server `ORD-yyyyMMdd-xxxxxx` | `ORD-20260908-A3F9K2` |
| `amountVnd` | int | > 0, đơn vị đồng | `99000`, `990000` |
| `Subscription.status` | enum | `FREE/PENDING_PAYMENT/ACTIVE/GRACE` | `ACTIVE` |
| `Order.status` | enum | `PENDING/CAPTURED/FAILED/TIMEOUT/CANCELLED/REFUNDED/PARTIAL_REFUNDED` | `CAPTURED` |
| `reconcileStatus` | enum | `OK/UNKNOWN/MISMATCH` | `UNKNOWN` |
| `idempotencyKey` | uuid/string(64) | client gửi, server lưu UNIQUE theo user+ngày | `9f3a...` |
| `gatewayRef` | string | mã giao dịch phía cổng, NULL khi chưa có | `VNP_884122` |
| `expiresAt/graceUntil` | timestamptz | luôn quy đổi hiển thị `Asia/Ho_Chi_Minh` | `2026-10-10T16:59:00Z` |

## 7.5 Chỉ mục và hiệu năng
- Index `Subscription(userId)`, `SubscriptionOrder(userId, status)`, `WebhookEvent(orderCode)`, `QuotaLedger(userId, date)`.
- Webhook ingest phải commit `WebhookEvent` trước khi xử lý nghiệp vụ để replay an toàn.
- Job reset quota chạy partition theo `userId % 32` để tránh khóa bảng giờ 00:00.
