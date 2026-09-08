# Use Case, Kịch Bản, Trạng Thái, Quy Tắc Thanh Toán (02-05 gộp khởi tạo)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/04-thanh-toan-doi-soat/`

## 02-danh-sach-use-case.md
- UC-01: Lập hóa đơn từ đơn, trừ cọc. Actor `cashier`. PROPOSED.
- UC-02: Thu tiền mặt và đóng két. PROPOSED.
- UC-03: Thu QR chuyển khoản và đối soát báo có. PROPOSED.
- UC-04: Thu ví / thẻ qua cổng ngoài. PROPOSED.
- UC-05: Xử lý giao dịch lơ lửng. PROPOSED.
- UC-06: Hoàn tiền sau PAID. PROPOSED.
- UC-07: Đóng ca và đối soát chênh lệch. PROPOSED.

## 03-ma-tran-kich-ban.md
- Thu trùng 2 lần, timeout nhưng trừ tiền, sao kê về chậm, két lệch, hoàn vượt hạn mức, đóng ca khi còn giao dịch khóa.

## 04-mo-hinh-trang-thai.md
- `UNPAID` → `PAYING` → `PAID` / `FAILED` / `PENDING_REVIEW` → `PAID` / `FAILED`. `PAID` → `REFUNDED` có duyệt.
- Cấm tạo hóa đơn mới khi còn `PENDING_REVIEW` chưa đối soát xong.

## 05-bo-quy-tac-nghiep-vu.md
- BR-F01: WHEN ví / thẻ timeout không rõ kết quả, THE `system` MUST khóa chờ đối soát, cấm thu lại. SOURCE DEC-P01. STATUS CONFIRMED.
- BR-F02: WHEN hoàn sau PAID, THE `branch_manager` MUST duyệt vượt hạn mức. STATUS PROPOSED.
- BR-D01: WHEN đóng ca còn `PENDING_REVIEW`, THE `system` MUST chặn đóng ca hoặc bàn giao có ghi nhận. STATUS PROPOSED.
