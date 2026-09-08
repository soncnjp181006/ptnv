# Kế Hoạch Kiểm Thử Đặt Bàn (16-ke-hoach-kiem-thu.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/01-dat-ban/16-ke-hoach-kiem-thu.md`

- Unit / Integration: tạo giữ tạm, chuyển `RESERVED`, chặn thiếu cọc, seated, hủy trước / sau cutoff, auto-release 60 phút.
- Contract: cọc QR / ví timeout nhưng trừ tiền thì đối soát đúng.
- E2E: đặt nhóm 6 người ngày lễ tới seated và hoàn / phạt đúng.
- Security: lễ tân không tự phạt cọc vượt ngưỡng, cần quản lý duyệt.
- Performance: concurrent giữ cùng bàn giờ cao điểm không tạo trùng.
- Regression: mọi thay đổi giữ bàn / cọc đều chạy lại ma trận kịch bản.
