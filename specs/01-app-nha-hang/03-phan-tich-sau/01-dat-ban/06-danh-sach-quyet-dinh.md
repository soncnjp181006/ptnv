# Danh Sách Quyết Định Đặt Bàn (06-danh-sach-quyet-dinh.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/01-dat-ban/06-danh-sach-quyet-dinh.md`

```text
ID: DEC-R01
Priority: P0
Loại câu hỏi: Đơn chọn
Vấn đề: Giữ bàn bao lâu khi khách đến muộn trước khi auto-release?
Bối cảnh: Chuỗi Tier Lớn, giờ cao điểm pool bàn căng. Giữ quá lâu mất lượt khách vãng lai. Nhả quá sớm gây khiếu nại.
Phương án A: 10 phút.
Phương án B: 15 phút.
Phương án C: 20 phút kèm nhắc tự động ở phút 5.
Phương án D (User đề xuất): 60 phút cho mô hình fine dining.
Đề xuất của Agent: B (15 phút).
Lý do: Cân bằng trải nghiệm và vòng quay bàn cho chuỗi lớn.
Phản biện và chốt: User xác nhận mô hình fine dining cần giữ lâu. Chấp nhận phương án D vì suất ăn dài, giá trị đơn cao, số lượt quay thấp. Bổ sung nhắc ở phút 15, 30, 50 để giảm bàn trống vô ích.
Ảnh hưởng: BR-T01, UC-05, UC-07, L2 nhánh 3.
Trạng thái: CONFIRMED — 60 phút cho fine dining
```

```text
ID: DEC-R02
Priority: P0
Loại câu hỏi: Đa chọn (Multi-select)
Vấn đề: Ngưỡng nào bắt buộc đặt cọc giữ chỗ?
Bối cảnh: Nhóm đông và ngày lễ có tỉ lệ bom bàn cao. Cọc giảm rủi ro nhưng thêm friction và đối soát.
Phương án A: Nhóm từ 6 khách trở lên.
Phương án B: Mọi đặt bàn ngày lễ / cuối tuần.
Phương án C: Miễn cọc toàn bộ ở pha 1.
Đề xuất của Agent: Kết hợp A + B.
Lý do: Phủ rủi ro bom bàn mà không làm khó khách ngày thường.
Ảnh hưởng: BR-F01, UC-03, đối soát cọc.
Trạng thái: CONFIRMED — Nhóm 6+ và Lễ / cuối tuần
```
