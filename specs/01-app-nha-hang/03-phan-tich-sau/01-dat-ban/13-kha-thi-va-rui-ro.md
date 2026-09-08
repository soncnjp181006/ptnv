# Khả Thi Và Rủi Ro Đặt Bàn (13-kha-thi-va-rui-ro.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/01-dat-ban/13-kha-thi-va-rui-ro.md`

- Kỹ thuật: khả thi. Khó nhất là concurrent giữ bàn và job auto-release chạy trùng. Giảm thiểu bằng arbitration first-confirm-wins và audit.
- Vận hành: rủi ro giữ 60 phút làm nghẽn giờ cao điểm fine dining. Giảm thiểu bằng nhắc 15 / 30 / 50 và cho quản lý gia hạn / thu hồi thủ công.
- Dữ liệu: cần đồng bộ sơ đồ bàn theo điểm theo thời gian thực.
- Tích hợp: cọc qua QR / ví cần đối soát giao dịch lơ lửng.
