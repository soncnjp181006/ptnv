# Ảnh Hưởng Dữ Liệu Và Tác Động Đặt Bàn (07-anh-huong-du-lieu-va-tac-dong.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/01-dat-ban/07-anh-huong-du-lieu-va-tac-dong.md`

## Tạo / sửa / xóa theo mutation

- Tạo `reservation`: `reservation_id`, `branch_id`, `table_id`, `business_date`, khung giờ, số khách, liên hệ, `state=HOLD`.
- Cập nhật `table`: `AVAILABLE` sang `HOLD` rồi `RESERVED`, ngược lại về `AVAILABLE` khi nhả / hủy.
- Tạo giao dịch cọc khi thuộc ngưỡng DEC-R02: số tiền, kênh, trạng thái đối soát.
- Ghi `audit`: mọi auto-release, hoàn / phạt cọc, override của `branch_manager`.
- Dữ liệu suy ra: tỉ lệ bom bàn, tỉ lệ đến muộn quá 60 phút, doanh thu cọc phạt.

## Sở hữu và nhất quán

- `table` thuộc điểm bán, `reservation` thuộc điểm bán nhưng chịu cấu hình chuỗi (khung giờ, ngưỡng cọc).
- Không cho xóa cứng `reservation` đã `SEATED` hoặc đã thu cọc. Chỉ đóng vòng đời về `RELEASED`.
