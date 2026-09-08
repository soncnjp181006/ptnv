# Truy Vết, Độ Phủ, Chất Lượng Gọi Món (09-10-11)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/02-goi-mon-tai-ban/`

## 09-ma-tran-truy-vet.md
- Chặn đơn khi chưa SEATED → BR-V01 → Acceptance: không có đơn mồ côi.
- Hủy chỉ ở DRAFT → BR-S01/S02/P01 → Acceptance: hủy sau DRAFT đều có phê duyệt và audit.
- Chống trùng gửi → BR-C01 → Acceptance: không có 2 `order_line` trùng.

## 10-ma-tran-do-phu-nghiep-vu.md
- Phủ happy, alternative, business exception, technical exception, recovery, state, rule, authority, data. Không orphan lõi.

## 11-danh-gia-chat-luong.md
- PASS. Không happy-path-only, có L0/L1/L2 đánh số, DEC-O01 đã CONFIRMED.
