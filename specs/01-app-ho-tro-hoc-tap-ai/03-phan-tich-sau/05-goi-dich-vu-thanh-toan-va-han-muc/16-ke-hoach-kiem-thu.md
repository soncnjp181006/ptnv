# 16 — Kế Hoạch Kiểm Thử: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Trọng tâm: tiền đúng + không trùng + phục hồi UNKNOWN. Môi trường sandbox cả 2 cổng.

| ID | Kịch bản | Bước | Kết quả mong đợi |
| :--- | :--- | :--- | :--- |
| TC-501 | Bảng giá | Mở `/pricing` | Đủ 3 gói, giá VND, quota rõ |
| TC-502 | Mua happy path | Mua Month99k sandbox ➔ trả tiền ➔ webhook | ACTIVE, receipt 99000, quota Paid |
| TC-503 | Grace | Set expiresAt=D, không trả ➔ check D+2, D+4 | D+2 Paid, 00:00 D+4 Free |
| TC-504 | Hủy auto-renew | Tắt trước kỳ charge | Giữ quyền, không charge mới |
| TC-505 | Hoàn tiền | Full trong 3 ngày; partial gói Năm; void PENDING | REFUNDED/PARTIAL/CANCELLED đúng |
| TC-506 | Concurrent mua | 10 req cùng Idempotency-Key | 1 đơn PENDING duy nhất |
| TC-507 | Webhook sai ký | Gửi HMAC sai | 401, đơn giữ PENDING |
| TC-508 | Webhook trùng | Gửi cùng eventId 3 lần | 1 lần kích hoạt, 2 lần 200-bỏ qua |
| TC-509 | Mất webhook | Chặn webhook, trả tiền thật sandbox | Job reconcile khớp ➔ ACTIVE bù |
| TC-510 | Quota | Dùng hết aiPerDay; chờ 00:00 | 429 + upgradeUrl; reset đúng |
| TC-511 | Thông báo | Ép quota 80%/100%, sub T-3 | Nhận đủ cảnh báo đúng giờ |
| TC-512 | Timeout đơn | Tạo đơn, không trả 31 phút | TIMEOUT + tạo đơn mới được |
| TC-513 | Tải + hoàn tiền thật | Số tiền nhỏ qua 2 cổng | Đối soát khớp 100% sandbox |

## Kiểm thử phi chức năng
- Tải: 100 tạo đơn/s, không trùng orderCode; webhook p95 < 2s.
- Bảo mật: replay webhook cũ > 5p bị từ chối; fuzz amount âm/bằng 0.
- Cổng vào phát hành: UNKNOWN > 24h = 0 trong 7 ngày canary.

## Môi trường và dữ liệu kiểm thử
- Môi trường: `staging` trỏ sandbox cả 2 cổng + đồng hồ giả để test grace/timeout mà không chờ thật.
- Dữ liệu: bộ thẻ/test-wallet sandbox; 3 user mẫu (Free, Month, Year); đơn ở mọi trạng thái để test dashboard.
- Không dùng tiền thật trừ TC-513 (số tiền nhỏ nhất, tài khoản nội bộ, hoàn ngay sau test).

## Tiêu chí vào/ra (entry/exit)
- Vào: API đóng băng request/response; seed dữ liệu xong; webhook sandbox thông.
- Ra: 100% TC P0 xanh; không còn bug P0/P1 mở; đối soát sandbox khớp 100%; runbook đã diễn tập 1 lần.
- Bug tiền (sai amount, double-active) luôn là P0, chặn phát hành dù ở canary.

## Test case bổ sung
| ID | Kịch bản | Kết quả mong đợi |
| :--- | :--- | :--- |
| TC-514 | Gia hạn chồng khi ACTIVE còn > 30 ngày | Chặn `SUBSCRIPTION_OVERLAP`, giữ đơn cũ |
| TC-515 | Webhook đến khi đơn đã TIMEOUT | Không hồi sinh đơn cũ; hướng dẫn tạo đơn mới |
| TC-516 | Đổi giá giữa chừng (planVersion mới) | Đơn cũ giữ giá cũ, đơn mới áp giá mới |

## Quản lý defect
- Kênh defect tiền gắn nhãn `BILLING`, SLA sửa P0 trong 24h kể cả canary.
- Mọi defect P0 phải có test hồi quy thêm vào suite trước khi đóng.
- Thống kê defect theo tuần trong canary; > 3 P0/tuần ➔ dừng mở rộng, sửa gốc.

## Lịch chạy kiểm thử
| Giai đoạn | Suite chạy | Tần suất |
| :--- | :--- | :--- |
| Dev đẩy code | TC-502/506/507/508 (nhóm tiền) | Mỗi commit |
| Cuối sprint | Toàn bộ TC-501 ➔ 516 | 1 lần + hồi quy |
| Trước canary | Chaos: ngắt webhook, double-click, sai ký | 1 buổi diễn tập |
| Trong canary | Đối soát gateway vs receipt mỗi ngày | Hằng ngày 09:00 |
