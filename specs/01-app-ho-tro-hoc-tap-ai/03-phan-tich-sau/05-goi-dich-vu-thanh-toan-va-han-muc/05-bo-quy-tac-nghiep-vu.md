# 05 — Bộ Quy Tắc Nghiệp Vụ: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Format: `BR-XXX / WHEN / MUST / OTHERWISE / AFFECTS / SOURCE / STATUS`. Tiền tệ `VND`, cutoff `00:00 Asia/Ho_Chi_Minh`.

## Nhóm giá và quota (BR-P)
- BR-P01 | WHEN user xem bảng giá | MUST hiển thị `Free/Month99k/Year990k` kèm quota `aiMessages/day`, `docs`, `storageMb`, `quizGen/day` | OTHERWISE coi là lỗi hiển thị P1 | AFFECTS `PlanCatalog` | SOURCE FACT | STATUS PROPOSED
- BR-P02 | WHEN tính giá Năm | MUST `Year990k = 990000`, rẻ hơn 12 tháng lẻ (~17% off) và hiển thị số tiết kiệm | OTHERWISE báo giá sai P0 | AFFECTS `PlanCatalog` | SOURCE FACT | STATUS PROPOSED
- BR-P03 | WHEN trừ quota | MUST kiểm tra `remaining > 0` theo ngày `00:00` trước mọi gọi AI/upload/sinh Quiz | OTHERWISE chặn `QUOTA_EXCEEDED` | AFFECTS `QuotaLedger` | SOURCE PROPOSED | STATUS PROPOSED

## Nhóm tài chính (BR-F)
- BR-F01 | WHEN tạo đơn mua | MUST sinh `orderCode` duy nhất + `Idempotency-Key`, `amountVnd` nguyên dương | OTHERWISE từ chối `INVALID_AMOUNT` | AFFECTS `SubscriptionOrder` | SOURCE PROPOSED | STATUS PROPOSED
- BR-F02 | WHEN capture thành công | MUST chỉ `CAPTURED` mới sinh `Receipt` và kích hoạt Paid | OTHERWISE không ghi nhận doanh thu | AFFECTS `Receipt` | SOURCE PROPOSED | STATUS PROPOSED
- BR-F03 | WHEN hoàn tiền toàn phần | MUST `refundAmount == paidAmount - gatewayFee` (nếu chính sách), đơn ➔ `REFUNDED`, hạ Free ngay | OTHERWISE treo công nợ | AFFECTS `RefundRequest` | SOURCE PROPOSED | STATUS PROPOSED
- BR-F04 | WHEN hoàn tiền một phần | MUST `0 < partial < paid`, đơn ➔ `PARTIAL_REFUNDED`, giữ Paid đến hết phần chưa hoàn | OTHERWISE từ chối `INVALID_REFUND` | AFFECTS `RefundRequest` | SOURCE PROPOSED | STATUS PROPOSED
- BR-F05 | WHEN void đơn chưa capture | MUST chuyển `PENDING ➔ CANCELLED`, không phát sinh dòng tiền | OTHERWISE khóa tiền user oan | AFFECTS `SubscriptionOrder` | SOURCE PROPOSED | STATUS PROPOSED
- BR-F06 | WHEN mua chồng (concurrent) | MUST khóa `userId` (DB unique pending / distributed lock), chỉ 1 `PENDING` sống | OTHERWISE trả `paymentUrl` cũ | AFFECTS `SubscriptionOrder` | SOURCE PROPOSED | STATUS PROPOSED

## Nhóm thời gian (BR-T)
- BR-T01 | WHEN reset quota ngày | MUST chạy lúc `00:00 Asia/Ho_Chi_Minh`, reset `aiMessages/day`, `quizGen/day` | OTHERWISE quota lệch ngày | AFFECTS `QuotaLedger` | SOURCE FACT | STATUS PROPOSED
- BR-T02 | WHEN hết hạn Paid | MUST mở `gracePeriodDays = 3`, giữ quyền Paid đến `00:00` ngày D+4 | OTHERWISE hạ Free sai hẹn | AFFECTS `Subscription` | SOURCE FACT | STATUS PROPOSED
- BR-T03 | WHEN đơn PENDING quá 30 phút không webhook | MUST chuyển `TIMEOUT`, giải phóng khóa mua | OTHERWISE kẹt mua chồng | AFFECTS `SubscriptionOrder` | SOURCE PROPOSED | STATUS PROPOSED
- BR-T04 | WHEN nhắc hết hạn | MUST gửi T-7, T-3, T-1 và ngày hết hạn lúc 08:00 | OTHERWISE coi là thiếu thông báo | AFFECTS `Notification` | SOURCE PROPOSED | STATUS PROPOSED

## Nhóm tích hợp (BR-X)
- BR-X01 | WHEN nhận webhook | MUST verify `signature` + `timestamp ±5 phút` trước khi xử lý | OTHERWISE trả `401`, không đổi trạng thái | AFFECTS `WebhookEvent` | SOURCE PROPOSED | STATUS PROPOSED
- BR-X02 | WHEN webhook trùng `eventId` | MUST ingest idempotent: trả `200 OK`, bỏ qua bản trùng | OTHERWISE kích hoạt gói 2 lần | AFFECTS `WebhookEvent` | SOURCE PROPOSED | STATUS PROPOSED
- BR-X03 | WHEN webhook retry | MUST gateway retry với backoff tới khi `200 OK` hoặc hết 24h; hệ thống chịu trùng lặp | OTHERWISE mất sự kiện thanh toán | AFFECTS `WebhookEvent` | SOURCE PROPOSED | STATUS PROPOSED
- BR-X04 | WHEN trạng thái cuối UNKNOWN | MUST đánh dấu `reconcileStatus=UNKNOWN`, job đối soát query gateway mỗi 15 phút tới 24h | OTHERWISE treo đơn vô thời hạn | AFFECTS `ReconcileJob` | SOURCE PROPOSED | STATUS PROPOSED

## Nhóm khôi phục (BR-RV)
- BR-RV01 | WHEN reconcile khớp `CAPTURED` | MUST kích hoạt Paid bù + gửi receipt bù + log `RECOVERED` | OTHERWISE thất thu quyền lợi user | AFFECTS `Subscription` | SOURCE PROPOSED | STATUS PROPOSED
- BR-RV02 | WHEN reconcile khớp `FAILED/NOT_FOUND` | MUST chuyển đơn `FAILED/TIMEOUT`, mở lại quyền mua | AFFECTS `SubscriptionOrder` | SOURCE PROPOSED | STATUS PROPOSED
- BR-RV03 | WHEN charge tự động lỗi trong grace | MUST retry tối đa `3 lần/ngày`, sau đó chờ thanh toán thủ công | OTHERWISE spam trừ tiền | AFFECTS `AutoRenew` | SOURCE PROPOSED | STATUS PROPOSED

## Nhóm thông báo (BR-N)
- BR-N01 | WHEN quota đạt 80% ngày | MUST gửi 1 cảnh báo/ngày/kênh, không spam mỗi lần gọi | OTHERWISE user tắt thông báo | AFFECTS `Notification` | SOURCE PROPOSED | STATUS PROPOSED
- BR-N02 | WHEN quota hết 100% | MUST chặn + trả `upgradeUrl` trong response để client mở paywall | OTHERWISE user không biết nâng cấp ở đâu | AFFECTS `QuotaLedger` | SOURCE PROPOSED | STATUS PROPOSED
- BR-N03 | WHEN thanh toán thành công/hoàn tiền | MUST gửi receipt/biên nhận trong 5 phút | OTHERWISE khiếu nại thiếu chứng từ | AFFECTS `Receipt` | SOURCE PROPOSED | STATUS PROPOSED

## Nhóm kiểm toán và lưu trữ (BR-AU)
- BR-AU01 | WHEN mọi chuyển đổi Order/Subscription | MUST ghi audit `who, when, from, to, reason`, giữ tối thiểu 5 năm | OTHERWISE không đối soát được | AFFECTS `AuditLog` | SOURCE PROPOSED | STATUS PROPOSED
- BR-AU02 | WHEN webhook đến | MUST lưu nguyên `payload` thô trước khi xử lý để replay khi cần | OTHERWISE mất bằng chứng tranh chấp | AFFECTS `WebhookEvent` | SOURCE PROPOSED | STATUS PROPOSED

## Ví dụ minh họa
- Ví dụ BR-F04: gói Năm 990k, dùng 100 ngày (`unitPrice = 990000/365 ≈ 2712đ/ngày`), phí gateway 5k ➔ hoàn ≈ `990000 - 271200 - 5000 = 713800đ`.
- Ví dụ BR-T02: `expiresAt = 10/10/2026 23:59` ➔ grace đến hết `13/10`, `00:00 14/10` hạ Free nếu chưa trả.
- Ví dụ BR-X02: gateway retry cùng `eventId` 3 lần ➔ lần 1 kích hoạt, lần 2–3 trả `200 OK` + log `DUPLICATE_IGNORED`.

## Bảng tổng hợp số lượng quy tắc
| Nhóm | Số BR | Phủ use case |
| :--- | :--- | :--- |
| BR-P (giá, quota) | 3 | UC-501, UC-508 |
| BR-F (tài chính) | 6 | UC-502 ➔ 505, UC-511/512 |
| BR-T (thời gian) | 4 | UC-502, 503, 509, 510 |
| BR-X (tích hợp) | 4 | UC-506, UC-507 |
| BR-RV (khôi phục) | 3 | UC-503, UC-507, UC-510 |
| BR-N/BR-AU | 5 | UC-508, 509 + kiểm toán |

## Quy tắc đặt mã và vòng đời BR
- Mã `BR-<nhóm><số>` tăng dần; không tái dùng mã đã xóa (giữ lịch sử).
- `STATUS` chỉ dùng `PROPOSED` ở vòng phân tích; chuyển `CONFIRMED` khi User chốt DEC liên quan.
