# 15 — Backlog Và Tiêu Chí Nghiệm Thu: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> `Story` + Acceptance Criteria kiểm được. Ưu tiên P0 trước.

| ID | Story | Acceptance Criteria |
| :--- | :--- | :--- |
| ST-501 | Xem bảng giá | Hiển thị đủ 3 gói + quota + nút Mua; giá đúng 0/99000/990000 VND |
| ST-502 | Tạo đơn idempotent | Double-click 2 lần cùng key ➔ 1 đơn PENDING + 1 paymentUrl |
| ST-503 | Webhook capture | Webhook hợp lệ ➔ ACTIVE + receipt ≤ 10s; trùng eventId bị bỏ qua |
| ST-504 | Chặn chữ ký sai | Sai HMAC ➔ 401, đơn không đổi trạng thái |
| ST-505 | Gia hạn + grace | Gia hạn D+2 ➔ ACTIVE; không gia hạn ➔ 00:00 D+4 hạ Free |
| ST-506 | Hủy auto-renew | Tắt ➔ giữ quyền đến expiresAt, không charge kỳ sau |
| ST-507 | Hoàn full/partial/void | Full ➔ REFUNDED + hạ Free; partial đúng công thức; void không dòng tiền |
| ST-508 | Reconcile UNKNOWN | Giả lập mất webhook ➔ job 15p khớp CAPTURED ➔ kích hoạt bù |
| ST-509 | Đơn TIMEOUT | PENDING 30p không webhook ➔ TIMEOUT + mua lại được |
| ST-510 | Quota chặn/reset | Hết aiPerDay ➔ 429 + upgradeUrl; 00:00 reset về limit |
| ST-511 | Thông báo | Quota 80%/100% + T-7/T-3/T-1 gửi đúng giờ 08:00 |
| ST-512 | Lịch sử + receipt | List đơn + tải receipt PDF/ảnh, đúng số tiền |

## Định nghĩa hoàn thành (DoD)
- Mọi story P0 có test tự động + log kiểm toán; không double-charge trong test tải 100 req/s.

## Tiêu chí phi chức năng chung (áp cho mọi story P0)
- Bảo mật: mọi endpoint tiền yêu cầu đăng nhập; webhook yêu cầu HMAC; Support thao tác cần quyền + ghi audit.
- Hiệu năng: API tạo đơn p95 < 1s; webhook ingest p95 < 2s; dashboard đơn tải 1000 dòng phân trang < 2s.
- Quan sát được: mỗi story sinh log có `orderCode/userId`, dashboard đếm được theo trạng thái giờ.
- Khả năng chạy lại: job nào cũng idempotent, chạy lại 10 lần vẫn đúng 1 kết quả.

## Thứ tự triển khai khuyến nghị
1. ST-501 ➔ ST-502 ➔ ST-510 (bán được + kiểm soát được) — xong là có khung monetization.
2. ST-503 ➔ ST-504 ➔ ST-505 ➔ ST-506 (webhook + concurrent + hoàn) — tiền vào đúng.
3. ST-508 ➔ ST-509 ➔ ST-507 (reconcile + timeout + gia hạn) — chống kẹt + giữ chân.
4. ST-511 ➔ ST-512 (thông báo + lịch sử) — hoàn thiện trải nghiệm trước canary.

## Story bổ sung (vận hành)
| ID | Story | Acceptance Criteria |
| :--- | :--- | :--- |
| ST-513 | Dashboard đơn cho vận hành | Lọc theo trạng thái/gateway/ngày; xuất CSV đối soát |
| ST-514 | Nút "Tôi đã trả tiền" | User bấm ➔ đơn vào hàng reconcile ưu tiên, SLA 2h |
| ST-515 | Xoay webhook secret | 2 secret song song 7 ngày, không rớt webhook nào |

## Ước lượng tương đối (story point, 1sp ≈ 0.5 ngày)
- ST-501/504/511/512: 2sp mỗi story (CRUD + UI đơn giản).
- ST-502/503/505/507/509/510: 3sp mỗi story (luồng + job).
- ST-506/508 (webhook 2 cổng + retry): 5sp mỗi story (khó nhất).
- Tổng MVP ≈ 45sp ≈ 3 sprint cho team 3 dev + 1 QA.

## Quy tắc tách story khi tràn sprint
- Webhook cổng thứ 2 (ST-506 Stripe) được dời 1 sprint mà không chặn canary cổng đầu.
- Notify (ST-511) được thay bằng email text tay trong canary nếu job chưa xong.
- Không được dời: idempotency, reconcile, quota — dời là mất an toàn tiền.
