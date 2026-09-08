# 12 — Phạm Vi Và Mục Tiêu: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Mục tiêu: thu tiền đúng, kích hoạt đúng, kiểm soát chi phí LLM bằng quota.

## 12.1 Mục tiêu đo được
- Renew rate tháng ≥ 60% sau grace; webhook ingest thành công ≥ 99.9%.
- Đơn UNKNOWN > 24h = 0 (mọi đơn đều có trạng thái cuối sau reconcile).
- Double-charge = 0 sự cố/tháng; khiếu nại sai quota < 1%.
- Thời gian kích hoạt Paid sau webhook ≤ 10s (p95).

## 12.2 Trong phạm vi (IN)
- Bảng giá 3 gói, mua/gia hạn/hủy auto-renew, hoàn tay (full/partial/void).
- Webhook 2 cổng MVP, retry idempotent, job đối soát, receipt cơ bản.
- Quota 4 chiều: `aiPerDay`, `maxDocs`, `storageMb`, `quizPerDay`; reset 00:00.
- Thông báo quota 80%/100%, nhắc hết hạn T-7/T-3/T-1, grace 3 ngày.

## 12.3 Ngoài phạm vi (OUT — phase 2)
- Đổi gói giữa chu kỳ (proration), coupon/mã giảm giá, ví nội bộ/credit.
- Hóa đơn VAT điện tử, thanh toán trả góp, nhiều tiền tệ ngoài VND.
- Tự động xét duyệt hoàn tiền bằng luật AI.

## 12.4 Giả định & ràng buộc
- Giá chốt `99k/990k VND`; phí gateway do VNPay/Stripe thu theo biểu của họ.
- User có tài khoản module 01 trước khi mua; mỗi user 1 subscription.
- Pháp lý: hiển thị giá đã gồm VAT (nếu có), điều khoản hoàn công khai.

## 12.5 Chỉ số thành công chi tiết (KPI)
| KPI | Mục tiêu | Cách đo |
| :--- | :--- | :--- |
| Renew rate tháng | ≥ 60% | `gia hạn thành công / hết hạn trong tháng` |
| Webhook ingest thành công | ≥ 99.9% | log `WebhookEvent` theo ngày |
| Đơn UNKNOWN > 24h | = 0 | dashboard reconcile |
| Double-charge/tháng | = 0 | đối soát gateway + khiếu nại |
| Kích hoạt Paid sau webhook (p95) | ≤ 10s | trace `webhook ➔ ACTIVE` |
| Khiếu nại sai quota | < 1% user Paid | ticket Support gắn nhãn quota |
| Tỉ lệ mở thông báo gia hạn T-3 | ≥ 35% | log Notify |

## 12.6 Ràng buộc phi chức năng
- Bảo mật: khóa API webhook bằng HMAC + allowlist IP; dữ liệu thẻ không chạm server (redirect PCI-scope-out).
- Hiệu năng: tạo đơn p95 < 1s; webhook ingest p95 < 2s; chịu 100 tạo đơn/s giờ cao điểm.
- Sẵn sàng: reconcile và reset quota phải chạy được khi deploy (job idempotent, chạy lại an toàn).

## 12.7 Tuyên bố giá trị từng gói (để viết trang giá)
- Free: "Học thử AI mỗi ngày — đủ để cảm nhận gia sư AI trước khi trả tiền."
- Month 99k: "Học không giới hạn suy nghĩ theo ngày — hủy bất cứ lúc nào, giữ quyền đến hết tháng."
- Year 990k: "Cam kết cả năm, tiết kiệm 2 tháng — cho người học nghiêm túc."
- Cam kết chung: "Trả tiền rồi chưa lên Paid? Đối soát trong 2h làm việc hoặc hoàn tiền."

## 12.8 Rủi ro ngoài phạm vi cần truyền thông rõ
- Trang giá phải ghi: chưa đổi gói giữa chu kỳ, chưa coupon, chưa hóa đơn VAT điện tử.
- Màn hình thanh toán phải ghi: điều khoản hoàn + kênh hỗ trợ BILLING-P0.
- Thiếu 2 dòng này là nguồn khiếu nại top 1 — PO xác nhận trước phát hành.
