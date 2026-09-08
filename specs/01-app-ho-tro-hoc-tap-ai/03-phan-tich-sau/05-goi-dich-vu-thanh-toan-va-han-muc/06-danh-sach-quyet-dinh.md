# 06 — Danh Sách Quyết Định: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Tất cả `Status = PROPOSED` (chờ User chốt). ID `DEC-5xx` cho module 05.

| ID | Quyết định | Phương án đề xuất | Phương án loại bỏ | Lý do | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| DEC-501 | Cổng thanh toán MVP | `VNPay + Stripe` | Chỉ 1 cổng duy nhất | Phủ thẻ nội địa + quốc tế, giảm rủi ro single point | PROPOSED |
| DEC-502 | Giá gói | `Free / 99k tháng / 990k năm` | Giá theo USD | Đơn giản, hợp tâm lý VND, Năm giảm ~17% | PROPOSED |
| DEC-503 | Chống mua trùng | Khóa `userId` + `Idempotency-Key` | Không khóa | Tránh double-charge khi double-click/retry | PROPOSED |
| DEC-504 | Đơn PENDING timeout | `30 phút` ➔ `TIMEOUT` | Chờ vô thời hạn | Giải phóng khóa mua, tránh kẹt user | PROPOSED |
| DEC-505 | Đối soát UNKNOWN | Job 15 phút tới 24h | Kích hoạt Paid ngay khi nghi ngờ | An toàn tiền, chỉ kích hoạt khi gateway xác nhận | PROPOSED |
| DEC-506 | Hoàn tiền MVP | Duyệt thủ công, SLA 3 ngày | Auto-refund | Kiểm soát gian lận dùng quota rồi hoàn | PROPOSED |
| DEC-507 | Hoàn một phần gói Năm | `partial = paid - usedDays*unitPrice - fee` | Không hoàn giữa chu kỳ | Công bằng, giữ chân user | PROPOSED |
| DEC-508 | Grace period | `3 ngày` giữ Paid | Cắt ngay khi hết hạn | Tránh gián đoạn học tập vì lỗi charge | PROPOSED |
| DEC-509 | Nhắc hết hạn | T-7/T-3/T-1 + ngày D lúc 08:00 | Chỉ nhắc 1 lần | Tăng tỉ lệ gia hạn, giảm khiếu nại | PROPOSED |
| DEC-510 | Không đổi gói giữa chu kỳ (MVP) | Chỉ mua mới sau hết hạn | Proration phức tạp | Giảm scope, proration để phase 2 | PROPOSED |

## Cần User chốt gấp
- DEC-501 (chọn cổng), DEC-502 (chốt giá + phí gateway ai chịu), DEC-506 (ngưỡng % quota cho phép hoàn).

## Phân tích chi tiết từng quyết định
- DEC-501: `VNPay` phủ chuyển khoản ngân hàng/QR nội địa, phí thấp; `Stripe` phủ thẻ Visa/Master quốc tế. Vận hành 2 cổng tăng công sức webhook ×2 nhưng tránh phụ thuộc đơn lẻ (R-508).
- DEC-502: mốc `99k` ngang một cốc trà sữa — tâm lý chi tiêu thử thấp; `990k` tặng ~2 tháng so với trả lẻ, đẩy cam kết năm và giảm churn thanh toán hàng tháng.
- DEC-503: khóa ở tầng DB (`partial unique index userId WHERE status=PENDING`) là chốt chặn cuối; lock bộ nhớ chỉ là tối ưu tốc độ. Test bắt buộc: double-click + retry cùng key.
- DEC-504: 30 phút khớp TTL `paymentUrl` phổ biến của VNPay/Stripe; quá hạn coi như user bỏ thanh toán.
- DEC-505: 24h là SLA tối đa tiền treo; sau 24h vẫn UNKNOWN thì con người quyết định, máy không đoán.
- DEC-506: duyệt tay giúp kiểm tra dấu hiệu gian lận (dùng hết quota rồi xin hoàn); SLA 3 ngày làm user yên tâm.
- DEC-507: công thức trừ `usedDays*unitPrice` theo ngày thực dùng + phí gateway thực tế — dễ giải thích cho user, dễ code, dễ kiểm.
- DEC-508: 3 ngày đủ che cuối tuần + 1 ngày xử lý ngân hàng; dài hơn sẽ bị lợi dụng dùng chùa.
- DEC-509: 3 mốc nhắc + ngày D là chuẩn SaaS; gửi 08:00 để user xử lý trong giờ hành chính.
- DEC-510: proration (tính tiền chênh khi đổi gói giữa chu kỳ) dễ sai và khó giải thích; dời phase 2 sau khi có số liệu renew.

## Quy trình chốt
1. PO chốt từng DEC bằng văn bản (comment/email), Agent chuyển `PROPOSED ➔ CONFIRMED`.
2. DEC bị bác bỏ ghi rõ lý do + phương án thay thế, không xóa dòng (giữ lịch sử).
3. Mọi thay đổi sau CONFIRMED phải qua tệp 19 (nhật ký) + đánh giá tác động tệp 07/08.

## DEC liên quan tới module khác (cần phối hợp)
| DEC | Module phối hợp | Nội dung phối hợp |
| :--- | :--- | :--- |
| DEC-502 | 03 Gia sư AI | Chốt quota Free đủ chặt để chi phí LLM < biên doanh thu |
| DEC-508/509 | Notify dùng chung | Mẫu tin + giờ gửi 08:00 + tần suất chống spam |
| DEC-506 | Kế toán/pháp lý | Ngưỡng hoàn + ai chịu phí gateway + mẫu biên nhận |

## Nhật ký chốt (mẫu, điền khi họp)
| Ngày | DEC | Kết quả | Người chốt |
| :--- | :--- | :--- | :--- |
| _chờ_ | DEC-501 | _chọn VNPay + Stripe / chỉ 1 cổng_ | PO |
| _chờ_ | DEC-502 | _giữ 99k/990k hay điều chỉnh_ | PO + Kế toán |
| _chờ_ | DEC-506 | _ngưỡng quota + SLA duyệt_ | PO |
