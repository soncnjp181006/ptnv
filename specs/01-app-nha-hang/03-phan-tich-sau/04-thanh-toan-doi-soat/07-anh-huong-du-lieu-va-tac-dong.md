# Hoàn Thiện Thanh Toán (07-19 gộp)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/04-thanh-toan-doi-soat/`

## 07-du-lieu
- Hóa đơn trừ cọc, giao dịch theo kênh, `PENDING_REVIEW` khóa thu lại, audit hoàn và đóng ca.

## 08-lien-module
- Nhận đơn SERVED và cọc từ đặt bàn. Chặn mở bàn mới khi còn khóa tiền. Xuất báo cáo doanh thu.

## 09-truy-vet
- Lơ lửng → khóa chờ → tra soát → PAID/FAILED, không orphan. Hoàn có duyệt.

## 10-11-phu-va-chat-luong
- Phủ happy, exception, recovery. PASS.

## 12-13-pham-vi-rui-ro
- In: 3 kênh, hoàn có duyệt, đối soát ca. Rủi ro: kẹt ca khi khóa lâu. Giảm thiểu bằng tra soát định kỳ và bàn giao ca có ghi nhận.

## 14-15-lo-trinh-backlog
- Thí điểm tiền mặt + QR trước, thêm ví / thẻ sau khi có cổng. Acceptance: không double-charge, không đóng ca khi còn khóa chưa bàn giao.

## 16-kiem-thu
- Test timeout lơ lửng, sao kê chậm, két lệch, hoàn vượt hạn mức.

## 17-18-tong-hop-phat-hanh
- Thí điểm hẹp, rollback về thu tay khi cổng lỗi.

## 19-nhat-ky
- 2026-09-08: khởi tạo, chốt DEC-P01 khóa chờ, hoàn thiện 07 tới 19.
