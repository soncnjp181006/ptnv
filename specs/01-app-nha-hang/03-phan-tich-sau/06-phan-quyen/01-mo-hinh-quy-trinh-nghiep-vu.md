# Phân Quyền (06-phan-quyen gộp lõi)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/06-phan-quyen/`

## Ma trận quyền đề xuất (PROPOSED)

| Hành động | Lễ tân | Phục vụ | Bếp | Thu ngân | Quản lý điểm | Quản trị chuỗi |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Tạo / xếp đặt bàn | Có | Không | Không | Không | Có | Xem |
| Seated | Có | Không | Không | Không | Có | Xem |
| Ghi món DRAFT | Không | Có | Không | Không | Có | Xem |
| Hủy sau DRAFT | Không | Xin | Xác nhận | Không | Duyệt | Xem |
| Báo hết món | Không | Không | Có tạm | Không | Duyệt chính | Xem |
| Thu tiền trong hạn mức | Không | Không | Không | Có | Có | Xem |
| Hoàn vượt hạn mức | Không | Không | Không | Không | Duyệt | Xem |
| Đóng ca | Không | Không | Không | Có | Duyệt | Xem |
| Cấu hình chuỗi | Không | Không | Không | Không | Đề xuất | Duyệt |

- BR-P01: mọi duyệt vượt hạn mức phải ghi audit. PROPOSED.
- DEC-A01 UNRESOLVED (P1): hạn mức hoàn cho thu ngân là bao nhiêu? Đề xuất: 500 nghìn mỗi hóa đơn.
