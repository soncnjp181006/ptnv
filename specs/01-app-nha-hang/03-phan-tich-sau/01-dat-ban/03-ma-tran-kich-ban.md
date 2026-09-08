# Ma Trận Kịch Bản Đặt Bàn (03-ma-tran-kich-ban.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/01-dat-ban/03-ma-tran-kich-ban.md`

| Use Case | Happy path | Alternative | Business exception | Technical / external exception | Edge / Recovery |
| :--- | :--- | :--- | :--- | :--- | :--- |
| UC-01 | Ngày giờ hợp lệ, còn bàn, tạo giữ tạm | Xin bàn lớn, ghép bàn | Ngày quán đóng cửa, giờ ngoài khung | Mất mạng khi xác nhận | Gửi 2 lần trùng, concurrent cùng bàn |
| UC-03 | Cọc thành công, chuyển RESERVED | Miễn cọc nhóm nhỏ | Cọc thiếu, sai nội dung | Cổng ví timeout nhưng trừ tiền | Retry sau partial, đối soát lơ lửng |
| UC-04 | Xếp đúng sức chứa, đúng khu | Đổi bàn tương đương | Bàn hỏng đột xuất | Sơ đồ bàn chưa đồng bộ điểm bán | Bàn biến mất giữa chừng, đề xuất lại |
| UC-05 | Đến đúng giờ, seated | Đến sớm, cho chờ sảnh | Đến muộn trong grace | Thiết bị lễ tân treo | Quá grace thì auto-release |
| UC-06 | Hủy trước cutoff, hoàn cọc | Dời giờ thay vì hủy | Hủy sát giờ, phạt cọc | Thông báo hủy gửi thất bại | Hủy sau khi đã seated thì chuyển sang hủy đơn |
| UC-07 | Nhả đúng giờ, ghi audit | Gia hạn giữ theo duyệt | Khách khiếu nại phạt | Job auto-release chạy trùng | Manual override của quản lý điểm |

Mọi kịch bản retry / reversal cần chính sách `idempotency` ở tầng nghiệp vụ, chưa bàn cơ chế kỹ thuật.
