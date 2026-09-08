# Ảnh Hưởng Dữ Liệu Gọi Món (07-anh-huong-du-lieu-va-tac-dong.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/02-goi-mon-tai-ban/07-anh-huong-du-lieu-va-tac-dong.md`

- Tạo `order` theo bàn `SEATED`, tạo `order_line` ở `DRAFT`.
- Chuyển `KITCHEN_SENT` tạo yêu cầu bếp, khóa sửa trực tiếp.
- Hủy sau `DRAFT` phải ghi `approval` và `audit`, có thể sinh phí.
- Món `OUT_OF_STOCK` cập nhật khả dụng theo điểm tạm thời.
