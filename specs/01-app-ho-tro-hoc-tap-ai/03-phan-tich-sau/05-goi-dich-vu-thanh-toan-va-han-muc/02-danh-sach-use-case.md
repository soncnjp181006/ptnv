# 02 — Danh Sách Use Case: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Module `05-goi-dich-vu-thanh-toan-va-han-muc` | Actor: `Student`, `System`, `PaymentGateway`, `SupportStaff`

| ID | Use case | Actor | Mô tả | Ưu tiên |
| :--- | :--- | :--- | :--- | :--- |
| UC-501 | Xem gói và so sánh Free/Paid | `Student` | Xem bảng giá `Month99k`, `Year990k`, quota từng gói | P0 |
| UC-502 | Mua gói Paid lần đầu | `Student` | Chọn chu kỳ + kênh, tạo `Order` PENDING, redirect gateway | P0 |
| UC-503 | Gia hạn gói Paid | `Student` | Gia hạn trước/sau `expiresAt`, cộng dồn chu kỳ | P0 |
| UC-504 | Hủy tự động gia hạn | `Student` | Tắt `autoRenew`, giữ quyền đến hết chu kỳ | P0 |
| UC-505 | Yêu cầu hoàn tiền | `Student` | Tạo `RefundRequest`, duyệt thủ công trong SLA | P1 |
| UC-506 | Nhận webhook thanh toán | `PaymentGateway` | Hệ thống verify signature, ingest idempotent | P0 |
| UC-507 | Đối soát giao dịch | `System` | Job reconcile `UNKNOWN`/`PENDING` quá SLA với gateway | P0 |
| UC-508 | Kiểm tra và trừ quota | `System` | `checkQuota` trước gọi AI, upload, sinh Quiz | P0 |
| UC-509 | Nhận thông báo quota/hết hạn | `Student` | Cảnh báo `80%`, `100%` quota; nhắc hết hạn T-7/T-3/T-1 | P0 |
| UC-510 | Xử lý grace period | `System` | 3 ngày sau hết hạn: giữ Paid, chặn mua chồng, rồi hạ Free | P0 |
| UC-511 | Xem lịch sử đơn và hóa đơn | `Student` | Danh sách `Order`, `Receipt`, trạng thái refund | P1 |
| UC-512 | Support tra cứu và void/refund thủ công | `SupportStaff` | Tra cứu `orderCode`, thực hiện `void`/`refund` theo phân quyền | P1 |

## Mô tả actor
- `Student`: học viên đã có tài khoản (module 01), thực hiện mua/gia hạn/hủy/hoàn và xem lịch sử.
- `System`: job nền (reset quota 00:00, reconcile, nhắc hết hạn, grace) chạy không cần người.
- `PaymentGateway`: `VNPay/Stripe` gửi webhook kết quả thu tiền về hệ thống.
- `SupportStaff`: nhân sự hỗ trợ có phân quyền tra cứu đơn và thao tác void/refund tay.

## Điều kiện đầu vào / đầu ra chính
| Use case | Tiền điều kiện | Hậu điều kiện |
| :--- | :--- | :--- |
| UC-502 | User đã đăng nhập, chưa có `PENDING` sống | `Order PENDING` + `paymentUrl` còn hạn 30 phút |
| UC-503 | Có `Subscription ACTIVE/GRACE` | `expiresAt` tăng theo chu kỳ, 1 receipt mới |
| UC-504 | Có `autoRenew = true` | `autoRenew = false`, giữ quyền đến `expiresAt` |
| UC-505 | Có `Order CAPTURED` trong hạn khiếu nại | `RefundRequest PENDING_REVIEW` chờ duyệt |
| UC-506 | Gateway ký HMAC đúng | Đơn chuyển trạng thái cuối hoặc giữ PENDING + reconcile |
| UC-508 | User thực hiện tác vụ tốn quota | Quota trừ 1 hoặc chặn `429 QUOTA_EXCEEDED` |

## Ghi chú phạm vi MVP
- UC-501, 502, 503, 504, 506, 507, 508, 509, 510 thuộc MVP bắt buộc.
- UC-505, 511, 512 ở mức cơ bản: form yêu cầu + duyệt thủ công, chưa tự động xét duyệt.
- Kênh MVP: 1 ví nội địa (`MoMo` hoặc `VNPay`) + 1 thẻ quốc tế (`Stripe`); thêm kênh sau MVP.
- Không hỗ trợ đổi gói giữa chu kỳ trong MVP (chỉ mua mới sau hết hạn hoặc nâng cấp thủ công).

## Ánh xạ use case tới flowchart (tệp 01)
| Use case | Bước L1 | Bước L2 |
| :--- | :--- | :--- |
| UC-501/502 | P1 ➔ P2 ➔ P3 | A1 ➔ A5 |
| UC-506/507 | P3 ➔ P4/P5 | A6 ➔ A10 |
| UC-503/504/510 | P4 ➔ P6 ➔ P7 | A8 ➔ A11 |
| UC-505/511/512 | Ngoài luồng mua chính | Luồng vận hành Support |
| UC-508/509 | P6 (enforce quota) | Check tại mọi tác vụ AI |

## Tần suất sử dụng dự kiến
- UC-508 chạy hàng triệu lần/ngày (mỗi câu AI) nên phải nhẹ (< 300ms, cache được).
- UC-502/503 tần suất thấp nhưng giá trị cao (trực tiếp ra tiền) nên đầu tư test kỹ nhất.
