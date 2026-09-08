# Ảnh Hưởng Liên Module Đặt Bàn (08-anh-huong-lien-module.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/01-dat-ban/08-anh-huong-lien-module.md`

- Trạng thái: `RESERVED` cho phép tạo đơn ở module gọi món. `RELEASED` chặn tạo đơn.
- Dữ liệu: `table_id` và `reservation_id` truyền sang gọi món, bếp, thanh toán.
- Phân quyền: lễ tân được xếp bàn, quản lý duyệt phạt cọc và gia hạn giữ.
- Tài chính: cọc thu trước phải trừ vào hóa đơn hoặc ghi nhận phạt, đối soát ở module thanh toán.
- Tồn kho: N/A cho đặt bàn thuần túy vì chưa gọi món. Lý do: chưa có `order_line`.
- Thông báo: tạo nhắc 15 / 30 / 50 phút và báo auto-release.
- Báo cáo: đếm đặt, hủy, bom, đến muộn để báo cáo chuỗi.
