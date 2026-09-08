# Backlog Và Tiêu Chí Nghiệm Thu Đặt Bàn (15-backlog-va-tieu-chi-nghiem-thu.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/01-dat-ban/15-backlog-va-tieu-chi-nghiem-thu.md`

## Epic: Đặt bàn fine dining chuỗi

- Feature tạo đặt bàn:
  - Story: khách tạo yêu cầu, hệ thống giữ tạm khi còn bàn.
  - Acceptance: hết bàn thì đề xuất giờ khác hoặc Waitlist, không tạo giữ treo.
- Feature cọc:
  - Story: nhóm 6+ và lễ bắt buộc cọc trước `RESERVED`.
  - Acceptance: thiếu cọc thì không chuyển `RESERVED`, cọc lơ lửng có đối soát.
- Feature seated và auto-release:
  - Story: lễ tân seated khi đến, hệ thống nhả sau 60 phút và xử lý cọc.
  - Acceptance: quá 60 phút tự `RELEASED`, có audit và thông báo.
- Feature chống trùng:
  - Story: hai thao tác cùng bàn thì một bên thắng.
  - Acceptance: không có hai `RESERVED` trùng `table_id` cùng giờ.
