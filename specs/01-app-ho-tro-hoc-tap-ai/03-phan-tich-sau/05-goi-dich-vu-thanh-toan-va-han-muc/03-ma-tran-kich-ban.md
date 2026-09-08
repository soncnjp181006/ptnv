# 03 — Ma Trận Kịch Bản: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Mỗi use case có `Happy`, `Alternative`, `Exception`. Trạng thái đơn: `PENDING/CAPTURED/FAILED/TIMEOUT/CANCELLED/REFUNDED/PARTIAL`.

## UC-502 Mua gói Paid lần đầu
- Happy: chọn `Month99k` ➔ tạo `Order(PENDING)` ➔ redirect ➔ webhook `CAPTURED` ➔ `Subscription ACTIVE`, cộng quota, gửi receipt.
- Alternative (3A): đã có `Order PENDING` còn hiệu lực ➔ trả lại `paymentUrl` cũ, không tạo đơn mới.
- Exception (3B): webhook `FAILED` ➔ đơn `FAILED`, giữ Free, cho thử lại với `Idempotency-Key` mới.
- Exception (timeout): 30 phút không webhook ➔ đơn `TIMEOUT`, user tạo đơn mới với `orderCode` mới.

## UC-503 Gia hạn gói
- Happy: gia hạn trước `expiresAt - 7 ngày` ➔ `expiresAt = expiresAt + cycle`, giữ `SubscriptionId` cũ.
- Alternative: gia hạn trong grace 3 ngày ➔ kích hoạt lại Paid từ thời điểm thanh toán, không truy thu ngày grace.
- Exception: mua chồng khi `ACTIVE còn > 30 ngày` ➔ chặn, báo `SUBSCRIPTION_OVERLAP`.

## UC-504 Hủy tự động gia hạn
- Happy: tắt `autoRenew` ➔ giữ quyền đến `expiresAt`, sau đó tự hạ Free.
- Alternative: bật lại `autoRenew` trước `expiresAt` ➔ lịch charge tiếp theo được khôi phục.
- Exception: hủy khi đang `PENDING` ➔ chuyển đơn sang `CANCELLED`, không thu tiền.

## UC-505 Hoàn tiền
- Happy: yêu cầu trong 3 ngày, chưa dùng quá 10% quota ➔ duyệt ➔ `REFUNDED` toàn phần, hạ Free.
- Alternative: hoàn một phần (dùng dở chu kỳ Năm) ➔ tính `partial = paid - usedDays*unitPrice - fee`, đơn `PARTIAL`.
- Exception: quá hạn khiếu nại 7 ngày ➔ từ chối, giữ nguyên Paid, ghi `RefundRequest REJECTED`.

## UC-506 Webhook thanh toán
- Happy: signature hợp lệ, `orderCode` tồn tại ➔ ingest 1 lần, chuyển `CAPTURED`.
- Alternative (retry): webhook trùng `eventId` ➔ bỏ qua, trả `200 OK` (idempotent).
- Exception: sai chữ ký ➔ trả `401`, ghi log cảnh báo, không đổi trạng thái đơn.

## UC-507 Đối soát
- Happy: job phát hiện `PENDING > 15 phút` ➔ query gateway ➔ khớp `CAPTURED` ➔ kích hoạt gói.
- Alternative: gateway báo `NOT_FOUND` ➔ đơn `TIMEOUT`, giải phóng khóa mua.
- Exception (`UNKNOWN` kéo dài): quá SLA 24h vẫn `UNKNOWN` ➔ khoanh vùng, báo Support, giữ trạng thái, không tự kích hoạt.

## UC-508 Quota
- Happy: `remaining > 0` ➔ trừ 1, cho phép gọi AI.
- Alternative: đạt 80% ➔ cho phép nhưng bắn cảnh báo.
- Exception: hết quota ➔ chặn `QUOTA_EXCEEDED`, gợi ý nâng cấp, ghi sự kiện cho báo cáo.

## Bảng tổng hợp mức độ bao phủ kịch bản
| Use case | Happy | Alternative | Exception | Ghi chú |
| :--- | :--- | :--- | :--- | :--- |
| UC-502/503/504 | Có | Có | Có | Nhóm mua–gia hạn–hủy đủ 3 nhánh |
| UC-505 | Có | Có | Có | Full/partial/void + từ chối quá hạn |
| UC-506/507 | Có | Có | Có | Retry, trùng event, UNKNOWN |
| UC-508/509/510 | Có | Có | Có | Quota, thông báo, grace |

## UC-510 Grace period
- Happy: hết hạn ngày D, user gia hạn ngày D+2 ➔ Paid liên tục.
- Alternative: không gia hạn sau D+3 ➔ 00:00 ngày D+4 hạ Free, reset quota Free.
- Exception: charge tự động lỗi trong grace ➔ retry tối đa 3 lần/ngày, sau đó chờ thanh toán thủ công.

## UC-511/512 Lịch sử và hỗ trợ
- Happy: user mở lịch sử ➔ thấy đủ đơn, receipt, trạng thái refund theo thời gian giảm dần.
- Alternative: Support tra cứu bằng `orderCode` hoặc số điện thoại ➔ thấy toàn bộ timeline đơn.
- Exception: Support thao tác void/refund vượt hạn mức phân quyền ➔ chặn `FORBIDDEN`, ghi audit.

## Quy tắc ưu tiên khi xung đột kịch bản
- An toàn tiền luôn thắng trải nghiệm: nghi ngờ ➔ giữ `PENDING` + reconcile, không tự kích hoạt.
- Trùng lặp luôn resolve theo idempotency: bản đầu thắng, bản sau bỏ qua có log.
- Mọi exception đều phải có mã lỗi machine-readable (`SUBSCRIPTION_OVERLAP`, `QUOTA_EXCEEDED`, `INVALID_REFUND`) để client hiển thị đúng.
