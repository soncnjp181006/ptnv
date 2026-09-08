# Phạm Vi, Khả Thi, Lộ Trình, Backlog, Kiểm Thử, Tổng Hợp, Phát Hành, Nhật Ký (12-19)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/02-goi-mon-tai-ban/`

## 12-pham-vi-va-muc-tieu.md
- In: ghi nháp, gửi bếp, hủy chặt sau DRAFT, báo hết. Out: trừ kho tự động, tự gợi ý món bằng AI.

## 13-kha-thi-va-rui-ro.md
- Rủi ro: phục vụ thấy chặt quá sẽ xin duyệt nhiều, kẹt quản lý. Giảm thiểu bằng phân quyền bếp xác nhận nhanh cho món chưa làm.

## 14-lo-trinh-thuc-hien.md
- Thí điểm: nháp + gửi + hủy chặt. Sau đó: hết món theo điểm, báo cáo hủy.

## 15-backlog-va-tieu-chi-nghiem-thu.md
- Story hủy sau DRAFT bắt buộc có phê duyệt và audit mới PASS.

## 16-ke-hoach-kiem-thu.md
- Test: chặn đơn khi chưa seated, hủy DRAFT tự do, hủy sau DRAFT cần duyệt, gửi trùng idempotent.

## 17-tong_hop.md
- Đã chốt hủy chỉ ở DRAFT. Hồ sơ đủ để sang bếp và thanh toán.

## 18-ke-hoach-phat-hanh.md
- Thí điểm 1 tới 2 điểm, rollback về ghi tay nếu bếp quá tải duyệt.

## 19-nhat-ky-thay-doi.md
- 2026-09-08: khởi tạo 01 tới 06. Chốt DEC-O01 phương án A. Hoàn thiện 07 tới 19.
