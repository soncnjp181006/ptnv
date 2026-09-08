# Ma Trận Kịch Bản Gọi Món (03-ma-tran-kich-ban.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/02-goi-mon-tai-ban/03-ma-tran-kich-ban.md`

| Use Case | Happy | Alternative | Business exception | Technical exception | Recovery |
| :--- | :--- | :--- | :--- | :--- | :--- |
| UC-01 | Món còn, thêm nháp | Tách món theo người | Món ngừng phục vụ theo điểm | Mất mạng khi ghi | Lưu nháp lokale, đồng bộ lại |
| UC-02 | Gửi bếp thành công | Gửi từng đợt theo món khai vị / chính | Bếp quá tải, xếp hàng | Tablet treo giữa chừng | Gửi lại idempotent, không trùng |
| UC-04 | Bếp đồng ý hủy | Đổi món khác thay vì hủy | Bếp đã làm xong, từ chối | Thông báo bếp thất bại | Quản lý override có audit |
| UC-06 | Báo hết kịp thời | Gợi ý món tương đương | Hết giữa chừng khi đang làm | Màn hình bếp trễ | Thu hồi món khỏi thực đơn điểm tạm thời |
