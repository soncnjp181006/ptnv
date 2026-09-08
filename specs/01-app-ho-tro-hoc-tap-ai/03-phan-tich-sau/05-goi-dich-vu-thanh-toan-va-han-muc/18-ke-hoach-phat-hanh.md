# 18 — Kế Hoạch Phát Hành: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Phát hành tiền cần canary + công tắc tắt Paid + đối soát ngày.

## 18.1 Chiến lược
- Canary: 10% user ➔ 50% ➔ 100%, mỗi nấc giữ 3–7 ngày nếu UNKNOWN = 0.
- Feature flag `paidEnabled`; tắt khẩn trong 5 phút nếu phát hiện double-charge.
- Đóng băng đổi giá trong tuần phát hành; mọi đổi giá sau cần migration `PlanCatalog` version.

## 18.2 Checklist Go-Live
- [ ] Webhook sandbox ➔ production cả 2 cổng, allowlist IP + HMAC đúng.
- [ ] Job reconcile + reset quota 00:00 + nhắc T-7/T-3/T-1 đã bật và có alert.
- [ ] Dashboard đơn theo giờ: CAPTURED/FAILED/UNKNOWN/TIMEOUT/REFUND.
- [ ] Runbook: UNKNOWN > 24h, double-charge, chargeback, refund tay.
- [ ] Support được phân quyền tra cứu orderCode + void theo BR-F05.

## 18.3 Rollback
- Lỗi kích hoạt sai: tắt `paidEnabled`, giữ Free toàn sàn, reconcile bù sau.
- Lỗi quota chặn nhầm: fail-open quota (cho qua + alert) tối đa 24h trong khi sửa.
- Sau 7 ngày canary: đối soát doanh thu gateway khớp receipt 100% mới mở 100%.

## 18.4 Giám sát sau phát hành (2 tuần đầu)
| Chỉ số | Tần suất xem | Ngưỡng báo động |
| :--- | :--- | :--- |
| Đơn PENDING > 30p | Mỗi 15 phút | > 5 đơn/giờ ➔ paging |
| Đơn UNKNOWN | Mỗi 15 phút | > 0 quá 2h ➔ paging |
| Webhook 4xx/5xx | Mỗi 5 phút | > 1% ➔ cảnh báo |
| Double ACTIVE cùng user | Realtime | = 1 ca ➔ P0 ngay |
| Renew rate tuần canary | Mỗi ngày | < 40% ➔ xem lại nhắc hạn |

## 18.5 Truyền thông phát hành
- Thông báo trước 3 ngày: giá, quota, chính sách hoàn, kênh hỗ trợ khi lỗi thanh toán.
- Trang FAQ: "trả tiền rồi chưa lên Paid?" ➔ hướng dẫn bấm "Tôi đã trả tiền" + SLA xử lý 2h.
- Kênh tiếp nhận sự cố tiền ưu tiên riêng (gắn nhãn `BILLING-P0`), SLA phản hồi 30 phút giờ hành chính.

## 18.6 Rollback dữ liệu (khi kích hoạt sai hàng loạt)
1. Đóng băng tạo đơn mới (`paidEnabled = false`) để chặn lan rộng.
2. Xuất danh sách user bị ảnh hưởng + trạng thái đúng/sai từ log audit.
3. Chạy script bù theo từng ca (kích hoạt thiếu / thu hồi thừa), mỗi ca có dry-run.
4. Đối soát lại với gateway trước khi mở bán lại; công bố sự cố + đền bù (gia hạn thêm ngày).

## 18.7 Liên lạc khẩn
| Tình huống | Kênh | Người quyết |
| :--- | :--- | :--- |
| Double-charge | Gọi + chat nhóm BILLING-P0 | Tech lead (tắt bán) + PO (truyền thông) |
| Gateway down > 1h | Trang trạng thái + banner app | PO |
| Sai quota chặn hàng loạt | Banner + fail-open tạm | Tech lead |

## 18.8 Sau phát hành 30 ngày (đóng vòng canary)
- [ ] Đối soát doanh thu 30 ngày khớp 100% gateway vs receipt vs kế toán.
- [ ] Renew rate tháng đầu ≥ 50% (mục tiêu 60% từ tháng 2).
- [ ] Tổng kết defect BILLING + bổ sung test hồi quy còn thiếu.
- [ ] Chốt backlog phase 2 (proration, coupon, VAT) theo số liệu thực tế.
