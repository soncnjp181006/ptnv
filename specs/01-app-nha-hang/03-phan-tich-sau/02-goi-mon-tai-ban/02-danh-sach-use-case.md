# Danh Sách Use Case Gọi Món (02-danh-sach-use-case.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/02-goi-mon-tai-ban/02-danh-sach-use-case.md`

| Mã | Use Case | Actor | Đầu ra | Trạng thái |
| :--- | :--- | :--- | :--- | :--- |
| UC-01 | Ghi món vào đơn nháp | `waiter`, `customer` | `order_line` ở `DRAFT` | PROPOSED |
| UC-02 | Gửi bếp / bar | `waiter`, `system` | `order_line` sang `KITCHEN_SENT` | PROPOSED |
| UC-03 | Sửa / hủy món chưa gửi | `waiter` | Cập nhật nháp trực tiếp | PROPOSED |
| UC-04 | Xin hủy món đã gửi | `waiter`, `kitchen_staff` | Hủy có xác nhận bếp | PROPOSED |
| UC-05 | Duyệt hủy món đã làm | `branch_manager` | Hủy có phí và audit | PROPOSED |
| UC-06 | Báo hết món và gợi ý thay thế | `kitchen_staff` | Món `OUT_OF_STOCK` | PROPOSED |
