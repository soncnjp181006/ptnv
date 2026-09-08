# Danh Sách Quyết Định Thanh Toán (06-danh-sach-quyet-dinh.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/04-thanh-toan-doi-soat/06-danh-sach-quyet-dinh.md`

```text
ID: DEC-P01
Priority: P0
Loại câu hỏi: Đơn chọn
Vấn đề: Khi ví / thẻ timeout không rõ đã trừ tiền chưa, có cho thu lại ngay không?
Bối cảnh: Thu lại ngay dễ double-charge khách fine dining. Khóa chờ quá lâu kẹt bàn và ca. Đây là rủi ro tiền bạc trực tiếp.
Phương án A: Khóa chờ đối soát, cấm thu lại.
Phương án B: Cho thu lại ngay bằng tiền mặt / QR khác.
Phương án C: Cho thu lại sau N phút nếu chưa có kết quả.
Đề xuất của Agent: A.
Lý do: Bảo vệ tiền của khách và uy tín fine dining, chấp nhận kẹt tạm thời có kiểm soát.
Ảnh hưởng: BR-F01, BR-D01, UC-05, UC-07, L1 nhánh 6C.
Chốt: Phương án A theo User.
Trạng thái: CONFIRMED — Khóa chờ đối soát
```
