# 17 — Tổng Hợp: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Tóm tắt 1 trang cho stakeholder: bán gì, thu tiền sao, kiểm soát gì.

## Bán gì
- 3 gói: `Free` (làm quen), `Month 99k`, `Year 990k` (tiết kiệm ~17%).
- Quota 4 chiều/gói: câu AI/ngày, số tài liệu, dung lượng MB, lượt sinh Quiz/ngày; reset 00:00.

## Thu tiền sao
- Tạo `Order PENDING` idempotent ➔ redirect VNPay/Stripe ➔ webhook verify HMAC ➔ `CAPTURED` mới kích hoạt Paid + receipt.
- Chống mua trùng bằng khóa `userId`; đơn treo 30p ➔ `TIMEOUT`.
- UNKNOWN không tự kích hoạt — job đối soát 15p tới 24h quyết định bù hoặc hủy.

## Giữ chân và xử lý sự cố
- Grace 3 ngày giữ Paid; nhắc T-7/T-3/T-1 lúc 08:00; cảnh báo quota 80%/100%.
- Hoàn tiền duyệt tay: full (3 ngày, dùng <10% quota), partial gói Năm theo công thức, void khi chưa capture.
- Mục tiêu: renew ≥ 60%, double-charge = 0, UNKNOWN > 24h = 0.

## Số liệu tài chính minh họa
- 1.000 user Paid tháng (99k) ➔ doanh thu gộp ~99 triệu/tháng; trừ phí gateway ~2% còn ~97 triệu.
- 200 user gói Năm (990k) ➔ ~198 triệu/năm trả trước, dòng tiền tốt hơn nhưng cần trích trước nghĩa vụ dịch vụ 12 tháng.
- Quota Free là van chi phí: mỗi câu AI tốn token; nếu Free quá rộng, chi phí LLM ăn hết biên Paid — cần hiệu chỉnh sau 1 tháng số liệu.

## Việc cần chốt
- DEC-501 cổng thanh toán, DEC-502 giá/phí, DEC-506 ngưỡng hoàn — cả 3 đang PROPOSED.
- Sau chốt ➔ 3 sprint (catalog/quota ➔ webhook ➔ reconcile/hoàn) rồi canary 10% user.

## Rủi ro lớn nhất trong 1 câu
- Mất webhook mà không reconcile kịp ➔ user trả tiền nhưng không có Paid ➔ mất niềm tin ngay ngày đầu thu phí; vì vậy reconcile + nút đối soát tay là bắt buộc, không được cắt.

## FAQ cho stakeholder
- "Vì sao 3 sprint mà không phải 1?" ➔ Vì tiền cần 3 lớp: thu đúng (S1–S2), đối soát đúng (S3), nhắc/giữ chân (S3); gộp lại sẽ thiếu test nhóm tiền.
- "Vì sao chưa có coupon/proration?" ➔ Mỗi cái +1 sprint và làm mờ trọng tâm canary; thêm sau khi renew ổn định.
- "Free có lỗ chi phí AI không?" ➔ Có kiểm soát bằng quota Free + kill-switch ngân sách ngày (R-505).
- "Khi nào có tiền về?" ➔ Ngay sau canary 10% nếu webhook + reconcile xanh; full 100% sau 7 ngày đối soát khớp.
- "Ai chịu trách nhiệm khi sai tiền?" ➔ Runbook tệp 18 + RACI tệp 08: vận hành xử lý, dev sửa gốc, kế toán đối soát.

## Phụ lục thuật ngữ (dùng chung toàn module)
| Thuật ngữ | Nghĩa |
| :--- | :--- |
| `capture` | Cổng xác nhận đã trừ tiền thật — chỉ lúc này mới kích hoạt Paid |
| `void` | Hủy đơn chưa capture — không phát sinh dòng tiền |
| `refund full/partial` | Hoàn toàn phần/một phần sau capture — có dòng tiền ngược |
| `idempotency` | Gửi lại bao nhiêu lần cũng chỉ 1 kết quả (khóa `orderCode/eventId`) |
| `reconcile` | Đối chiếu đơn hệ thống với cổng để xử lý đơn treo/UNKNOWN |
| `grace period` | 3 ngày sau hết hạn vẫn giữ Paid, chờ gia hạn |
| `paywall` | Màn hình chặn + mời nâng cấp khi hết quota |
| `receipt` | Biên nhận sau capture — chứng từ duy nhất ghi nhận doanh thu |

## Đọc tiếp ở đâu (bản đồ tệp)
- Muốn hiểu luồng tiền: đọc tệp 01 ➔ 04 ➔ 05 theo thứ tự.
- Muốn chốt scope: đọc tệp 10 ➔ 12 ➔ 06 (hỏi PO 3 DEC).
- Muốn bắt tay code/test: đọc tệp 15 ➔ 16 ➔ 14 ➔ 18.
