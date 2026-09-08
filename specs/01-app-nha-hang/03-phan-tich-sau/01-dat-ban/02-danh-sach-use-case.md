# Danh Sách Use Case Đặt Bàn (02-danh-sach-use-case.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/01-dat-ban/02-danh-sach-use-case.md`

| Mã | Use Case | Actor chính | Đầu vào | Đầu ra | Trạng thái |
| :--- | :--- | :--- | :--- | :--- | :--- |
| UC-01 | Tạo yêu cầu đặt bàn | `customer` | Điểm, ngày giờ, số khách, liên hệ | `reservation` ở `HOLD` hoặc `RESERVED` | PROPOSED |
| UC-02 | Đề xuất giờ lân cận / Waitlist | `system` | Khung giờ yêu cầu hết bàn | Đề xuất hoặc `waitlist_entry` | PROPOSED |
| UC-03 | Thu cọc giữ chỗ | `customer`, `cashier` | Quy tắc cọc theo nhóm / lễ | Giao dịch cọc đã đối soát | PROPOSED |
| UC-04 | Lễ tân xác nhận và xếp bàn | `receptionist` | `reservation`, sơ đồ bàn | Bàn `RESERVED`, phân công khu vực | PROPOSED |
| UC-05 | Xác nhận khách đến (Seated) | `receptionist` | `reservation` tới giờ | Bàn `OCCUPIED`, đơn được phép tạo | PROPOSED |
| UC-06 | Hủy đặt bàn | `customer`, `receptionist` | `reservation`, thời điểm hủy | Bàn `RELEASED`, xử lý cọc | PROPOSED |
| UC-07 | Auto-release khi quá giờ | `system` | `grace_period` | Bàn `RELEASED`, xử lý cọc | PROPOSED |
| UC-08 | Xử lý trùng bàn concurrent | `system`, `receptionist` | Hai thao tác cùng `table_id` | Một bên thắng, một bên đề xuất lại | PROPOSED |

Không có Use Case nào được coi là policy chốt khi chưa qua `06-danh-sach-quyet-dinh.md`.
