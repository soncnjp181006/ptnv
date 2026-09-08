# Bộ Quy Tắc Gọi Món (05-bo-quy-tac-nghiep-vu.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/02-goi-mon-tai-ban/05-bo-quy-tac-nghiep-vu.md`

- BR-V01: WHEN bàn chưa `SEATED`, THE `system` MUST chặn tạo đơn. OTHERWISE đơn mồ côi. STATUS `PROPOSED`.
- BR-S01: WHEN `order_line` ở `DRAFT`, THE `waiter` MUST được sửa / hủy trực tiếp. OTHERWISE chậm phục vụ. SOURCE DEC-O01. STATUS `CONFIRMED`.
- BR-S02: WHEN `order_line` đã rời `DRAFT`, THE `system` MUST yêu cầu phê duyệt (bếp xác nhận nếu chưa làm, quản lý duyệt nếu đã làm). OTHERWISE bếp làm thừa hoặc thất thoát. SOURCE DEC-O01. STATUS `CONFIRMED`.
- BR-P01: WHEN hủy món đã `COOKING` / `SERVED`, THE `branch_manager` MUST duyệt và ghi phí nếu có. OTHERWISE thất thoát. STATUS `PROPOSED`.
- BR-C01: WHEN gửi trùng do thao tác 2 lần, THE `system` MUST chỉ ghi một `order_line`. OTHERWISE trùng món. STATUS `PROPOSED`.
