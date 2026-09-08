# 14 — Lộ Trình Thực Hiện: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> 3 sprint × 2 tuần. Phụ thuộc module 01 (user) và Notify.

| Sprint | Mục tiêu | Đầu ra |
| :--- | :--- | :--- |
| S1: Catalog + Order + Quota | Bảng giá, tạo đơn idempotent, `checkQuota` chặn module 02/03/04 | `GET /plans`, `POST /orders`, quota middleware |
| S2: Webhook + Kích hoạt | Verify signature, ingest idempotent, receipt, gia hạn/hủy/grace | Webhook 2 cổng, `Subscription ACTIVE/GRACE/FREE` |
| S3: Reconcile + Hoàn + Thông báo | Job đối soát, form hoàn tay, nhắc T-7/T-3/T-1 + quota 80%/100% | Dashboard đơn, `RefundRequest`, notify job |

## Cột mốc
- M1 (hết S1): mua thử sandbox end-to-end nội bộ, quota chặn đúng.
- M2 (hết S2): thanh toán thật số tiền nhỏ (99k) qua cả 2 cổng, receipt đúng.
- M3 (hết S3): chaos test (mất webhook, double-click, UNKNOWN) đạt 100% phục hồi.
- Phát hành: bật Paid cho 10% user trước (canary), theo dõi renew + UNKNOWN 1 tuần.

## Công việc chi tiết từng sprint
- S1-W1: chốt DEC-501/502; dựng `PlanCatalog` + API `GET /plans`; schema `Subscription/Order/QuotaLedger`.
- S1-W2: API `POST /orders` idempotent + khóa concurrent; middleware `checkQuota` cắm vào module 03/04; job reset 00:00.
- S2-W1: webhook VNPay (verify, ingest, receipt) trên sandbox; máy trạng thái Subscription + grace.
- S2-W2: webhook Stripe; gia hạn/hủy auto-renew; test thanh toán thật số tiền nhỏ nội bộ.
- S3-W1: job reconcile + dashboard đơn + form hoàn tay + phân quyền Support.
- S3-W2: notify quota/hết hạn; chaos test; runbook; canary 10%.

## Phụ thuộc và đường găng
- Đường găng: DEC-501 ➔ webhook ➔ reconcile ➔ canary; trễ DEC-501 quá 1 tuần sẽ trượt M2.
- Phụ thuộc ngoài: tài khoản merchant 2 cổng, webhook URL công khai (staging), kế toán xác nhận mẫu receipt.
- Song song được: UI bảng giá + dashboard đơn làm song song với backend từng sprint.

## Định nghĩa sẵn sàng (ready) cho mỗi sprint
- DEC liên quan đã CONFIRMED hoặc có phương án tạm (tệp 13).
- API contract (request/response/mã lỗi) đã review với client (web/app).
- Seed dữ liệu test + tài khoản sandbox cổng thanh toán sẵn sàng.
- Tiêu chí nghiệm thu từng story (tệp 15) đã chốt với QA.

## Nghi thức demo cuối sprint
- S1: demo mua sandbox + quota chặn AI khi hết lượt (dùng user Free test).
- S2: demo trả tiền thật 99k + receipt + gia hạn + hủy auto-renew trên staging.
- S3: demo chaos (ngắt webhook 10 phút ➔ reconcile bù) + dashboard đơn + hoàn tay.
- Mỗi demo ghi biên bản + quyết định phát hành tiếp hay sửa (go/no-go).

## Phân công team gợi ý
| Vai trò | S1 | S2 | S3 |
| :--- | :--- | :--- | :--- |
| Backend A | Catalog + Order idempotent | Webhook VNPay + receipt | Reconcile + dashboard đơn |
| Backend B | Quota middleware + job reset | Webhook Stripe + grace | Hoàn tay + notify job |
| Frontend | Trang giá + màn mua | Trạng thái đơn + lịch sử | Paywall hết quota + FAQ |
| QA | TC-501/502/510/512 | TC-503/504/506/507/513 | TC-505/508/509/511 + chaos |
