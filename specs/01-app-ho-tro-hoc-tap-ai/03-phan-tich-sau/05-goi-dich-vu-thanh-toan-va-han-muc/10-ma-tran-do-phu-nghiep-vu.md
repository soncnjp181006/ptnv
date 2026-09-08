# 10 — Ma Trận Độ Phủ Nghiệp Vụ: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Đánh giá use case nào bắt buộc cho MVP monetization.

| Use case | MVP | Lý do | Rủi ro nếu cắt |
| :--- | :--- | :--- | :--- |
| UC-501 xem gói | Có | Cửa bán hàng | Không bán được |
| UC-502 mua lần đầu | Có | Doanh thu | Mất monetization |
| UC-503 gia hạn | Có | Giữ chân Paid | Churn cao |
| UC-504 hủy auto-renew | Có | Niềm tin, pháp lý | Khiếu nại trừ tiền oan |
| UC-506 webhook | Có | Kích hoạt gói | Không thu được tiền |
| UC-507 đối soát | Có | Chống kẹt UNKNOWN | Đơn treo, hỗ trợ quá tải |
| UC-508 quota | Có | Kiểm soát chi phí LLM | Vỡ chi phí AI |
| UC-509 thông báo | Có | Gia hạn + upsell | Tụt renew rate |
| UC-510 grace 3 ngày | Có | Trải nghiệm hết hạn | Gián đoạn học tập |
| UC-505 hoàn tiền | Cơ bản | Form + duyệt tay | Khiếu nại chargeback |
| UC-511 lịch sử/receipt | Cơ bản | Minh bạch | Mất niềm tin |
| UC-512 support void/refund | Cơ bản | Vận hành | Không xử lý sự cố |

## Kết luận
- MVP bắt buộc: 9 use case đầy đủ + 3 use case ở mức cơ bản (form tay, chưa tự động).
- Ngoài MVP (phase 2): đổi gói giữa chu kỳ (proration), coupon/khuyến mãi, xuất hóa đơn VAT điện tử.

## Lý do giữ/cắt từng nhóm (chi tiết)
- Giữ webhook + reconcile đầy đủ vì đây là nơi tiền đi qua; cắt là mất khả năng thu tiền và đối soát.
- Giữ quota vì chi phí LLM tính theo token; không có quota thì hóa đơn AI vượt doanh thu (R-505).
- Giữ grace + thông báo vì chi phí thấp (vài job cron) mà tác động renew rate cao.
- Hoàn tiền chỉ cần form + duyệt tay ở MVP vì tần suất thấp; tự động hóa để sau khi có dữ liệu gian lận.
- Lịch sử/receipt cơ bản là yêu cầu niềm tin tối thiểu; bản đẹp (PDF VAT) để phase 2.

## Backlog phase 2 (đã nhìn thấy, chưa làm)
1. Proration đổi gói giữa chu kỳ (Free➔Month➔Year linh hoạt).
2. Coupon, mã giới thiệu, giá ưu đãi học sinh/sinh viên.
3. Hóa đơn VAT điện tử + xuất báo cáo doanh thu cho kế toán.
4. Tự động gia hạn bằng thẻ lưu (tokenization) thay vì redirect mỗi kỳ.
5. Gói nhóm/lớp học (mua cho nhiều học viên, quản trị viên phân bổ seat).

## Tiêu chí đưa use case vào MVP (scoring)
| Tiêu chí (trọng số) | Giải thích |
| :--- | :--- |
| Ra tiền trực tiếp (×3) | Use case nào chạm dòng tiền được ưu tiên tuyệt đối |
| Chống mất tiền (×3) | Reconcile, idempotency, audit ngang hàng với thu tiền |
| Kiểm soát chi phí (×2) | Quota chặn vỡ chi phí LLM |
| Giữ chân Paid (×2) | Grace, nhắc hạn, hoàn minh bạch |
| Niềm tin tối thiểu (×1) | Receipt, lịch sử, Support tra cứu |

## Cách đọc bảng độ phủ
- Cột MVP = "Có" nghĩa là đầy đủ luồng + test + vận hành trong 3 sprint.
- "Cơ bản" nghĩa là form tay + quy trình thủ công, đủ dùng cho < 1000 user đầu.
- Mọi mục "Cơ bản" đều có vé phase 2 để tự động hóa khi volume tăng.
