# Danh Sách Quyết Định Gọi Món (06-danh-sach-quyet-dinh.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/02-goi-mon-tai-ban/06-danh-sach-quyet-dinh.md`

```text
ID: DEC-O01
Priority: P0
Loại câu hỏi: Đơn chọn
Vấn đề: Cho sửa / hủy món tới bước nào mà không cần quản lý duyệt?
Bối cảnh: Fine dining cần linh hoạt trải nghiệm nhưng bếp đã làm thì hủy gây lãng phí. Phân quyền sai sẽ kẹt bếp hoặc thất thoát.
Phương án A: Chỉ khi DRAFT.
Phương án B: Tới KITCHEN_SENT nếu bếp chưa làm.
Phương án C: Tới COOKING với xác nhận bếp.
Đề xuất của Agent: B.
Lý do: Cân bằng tốc độ phục vụ và chống lãng phí bếp.
Ảnh hưởng: BR-S01, BR-S02, BR-P01, UC-03 tới UC-05.
Chốt: Phương án A. Mọi hủy sau DRAFT đều cần phê duyệt. Lý do User: kiểm soát chặt lãng phí fine dining.
Trạng thái: CONFIRMED — Chỉ khi DRAFT
```
