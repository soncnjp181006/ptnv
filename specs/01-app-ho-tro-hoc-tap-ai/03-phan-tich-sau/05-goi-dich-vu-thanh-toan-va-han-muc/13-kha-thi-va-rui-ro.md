# 13 — Khả Thi Và Rủi Ro: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Rủi ro tiền tệ được ưu tiên cao nhất.

| ID | Rủi ro | Xác suất | Tác động | Giảm thiểu |
| :--- | :--- | :--- | :--- | :--- |
| R-501 | Double-charge khi mua concurrent | T.Bình | Cao | Khóa `userId` + idempotency, test concurrent |
| R-502 | Webhook giả mạo | Thấp | Rất cao | Verify HMAC + timestamp ±5p, allowlist IP |
| R-503 | Mất webhook / gateway down | T.Bình | Cao | Retry + reconcile 15p + nút đối soát tay |
| R-504 | UNKNOWN kéo dài | Thấp | Cao | SLA 24h, escalate Support, không tự kích hoạt |
| R-505 | Vỡ chi phí LLM do quota sai | T.Bình | Cao | `checkQuota` đồng bộ + alert vượt ngưỡng chi phí |
| R-506 | Chargeback / gian lận hoàn | T.Bình | T.Bình | Hoàn tay, ngưỡng 10% quota, log đầy đủ |
| R-507 | Sai cutoff quota (múi giờ) | Thấp | T.Bình | Chuẩn `Asia/Ho_Chi_Minh`, test DST/quanh 00:00 |
| R-508 | Phụ thuộc 1 cổng duy nhất | T.Bình | Cao | 2 cổng MVP (DEC-501), chuyển kênh nhanh |

## Khả thi
- Kỹ thuật: KHẢ THI — webhook + reconcile là bài toán chuẩn, SDK VNPay/Stripe sẵn.
- Tài chính: KHẢ THI nếu quota Free đủ chặt (xem DEC-502, phối hợp module 03).
- Pháp lý: CẦN kiểm tra điều khoản hoàn/VAT với kế toán trước phát hành.
- Kết luận: Triển khai được trong 3 sprint (xem tệp 14) sau khi chốt cổng thanh toán.

## Kế hoạch ứng phó chi tiết (top 4 rủi ro)
- R-501 double-charge: ngoài khóa DB, thêm alert realtime khi 2 `CAPTURED` cùng user trong 5 phút; runbook hoàn tiền khẩn trong 24h + thư xin lỗi mẫu.
- R-502 webhook giả: xoay `webhookSecret` định kỳ 90 ngày, hỗ trợ 2 secret song song trong 7 ngày chuyển đổi; mọi secret lưu vault, không hardcode.
- R-503 gateway down: trang thái thái hệ thống công khai; nút "Tôi đã trả tiền" đẩy đơn vào hàng reconcile ưu tiên (SLA 2h làm việc).
- R-505 vỡ chi phí LLM: đặt trần chi phí AI/ngày ở tầng hạ tầng (kill-switch giảm quota Free tạm thời) + alert khi đạt 70%/90% ngân sách.

## Ma trận phụ thuộc quyết định
| Quyết định chưa chốt | Rủi ro bị chặn | Phương án tạm thời |
| :--- | :--- | :--- |
| DEC-501 cổng thanh toán | Không code webhook production | Code trước với 1 cổng sandbox (VNPay), tách interface gateway để cắm thêm cổng sau |
| DEC-502 giá/phí | Sai amountVnd mẫu | Dùng hằng số cấu hình `PRICE_MONTH_VND`, đổi không cần sửa code |
| DEC-506 ngưỡng hoàn | Support không biết duyệt sao | Mặc định ngưỡng 10% + 3 ngày, chốt sau vẫn kịp |

## Rủi ro tuân thủ và pháp lý
| ID | Rủi ro | Giảm thiểu |
| :--- | :--- | :--- |
| R-509 | Giá hiển thị chưa gồm VAT gây khiếu nại | Ghi rõ "đã gồm VAT (nếu áp dụng)" + hỏi kế toán trước phát hành |
| R-510 | Điều khoản hoàn không công khai | Đăng chính sách hoàn trên trang giá + màn hình trước thanh toán |
| R-511 | Lưu dữ liệu thanh toán quá hạn | Chỉ lưu `orderCode/gatewayRef`, không lưu số thẻ; log thô webhook mã hóa |
| R-512 | Trừ tiền tự động không báo trước | Nhắc T-7/T-3/T-1 + cho tắt auto-renew 1 chạm (UC-504) |

## Dự phòng ngân sách và thời gian
- Dự phòng 20% effort sprint 3 cho phát sinh webhook thực tế khác sandbox.
- Ngân sách test thanh toán thật: ~10 giao dịch × 99k (hoàn ngay sau test) + phí gateway.
- Nếu DEC-501 trễ > 2 tuần: phát hành bản "Paid thủ công" (chuyển khoản + kích hoạt tay) để không trễ monetization.

## Tín hiệu cảnh báo sớm (leading indicators)
- Tỉ lệ webhook retry > 5%/ngày ➔ cổng hoặc mạng có vấn đề, kiểm tra trước khi thành sự cố.
- Đơn PENDING tăng đột biến giờ thấp điểm ➔ có thể nút mua bị double-fire sau deploy mới.
- Ticket hỏi "trừ tiền chưa lên gói" ≥ 3/ngày ➔ reconcile hoặc thiếu nút đối soát tay.
