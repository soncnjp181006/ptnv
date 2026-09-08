# 19 — Nhật Ký Thay Đổi: Theo Dõi Tiến Độ Và Báo Cáo

> Mới nhất trên cùng. Mức MVP cơ bản (P1).

| Ngày | Phiên bản | Nội dung | Tác giả | Trạng thái |
| :--- | :--- | :--- | :--- | :--- |
| 08/09/2026 | v1.0 | Khởi tạo đủ 19 tệp MVP cơ bản: event, streak, dashboard 7 ngày, báo cáo tuần, bù trễ 7 ngày | Agent phân tích | PROPOSED |
| 08/09/2026 | v0.9 | Dự thảo BR-P/BR-T/BR-X/BR-RV, máy trạng thái Daily/Weekly | Agent phân tích | PROPOSED |
| 08/09/2026 | v0.5 | Phác thảo L0/L1/L2, chốt tuần T2–CN + cutoff 00:00 | Agent phân tích | PROPOSED |

## Quy ước cập nhật
- Chốt DEC-601/602 phải thêm dòng mới + sửa tệp 05, 11, 15, 16.
- Mở rộng phase 2 (mục tiêu, leaderboard, PDF) ghi dòng mới, không sửa số liệu MVP đã chốt.

## Quy ước phiên bản
- `v0.x`: dự thảo, còn mục UNRESOLVED hoặc thiếu trace test.
- `v1.0`: đủ 19 tệp MVP cơ bản, chờ User duyệt.
- `v1.x`: sửa sau duyệt (ghi rõ DEC nào đổi).
- `v2.0`: thêm phạm vi phase 2 — duyệt lại phần mới, giữ nguyên số liệu MVP.

## Mẫu dòng nhật ký mới
- Ngày theo `DD/MM/YYYY Asia/Ho_Chi_Minh`; Trạng thái `PROPOSED/CONFIRMED`.
- Ví dụ: "Chốt DEC-601 ngưỡng 5 phút, cập nhật tệp 05/15/16".
- Không sửa dòng cũ, chỉ thêm dòng mới trên cùng; ai sửa ghi tên.

## Lịch sử chi tiết vòng v1.0
- Vòng 1: dựng khung 19 tệp + flowchart L0/L1/L2 + máy trạng thái Daily/Weekly.
- Vòng 2: bổ sung BR-D, ví dụ số, KPI, thứ tự triển khai, giám sát sau phát hành.
- Vòng 3 (hiện tại): rà soát độ dài ≥ 40 dòng/tệp, kiểm tra trace BR➔test.
- Việc còn lại: User chốt DEC-601/602 ➔ CONFIRMED ➔ bàn giao song song module 05.

## Danh sách kiểm tra trước khi giao triển khai (handoff)
- [ ] 19/19 tệp tồn tại, mỗi tệp 40–120 dòng, không placeholder.
- [ ] Tệp 01 đủ 3 flowchart L0/L1/L2, edge đánh số, branch 2A/2B/2C/6A/6B.
- [ ] Tệp 05 đủ nhóm BR-P/BR-T/BR-X/BR-RV/BR-D; tệp 06 toàn PROPOSED.
- [ ] Schema event `v1` đã ký với module 03/04; DEC-601/602 có hạn chốt.

## Quy ước phiên bản
- `v0.x`: dự thảo, còn mục UNRESOLVED hoặc thiếu trace test.
- `v1.0`: đủ 19 tệp MVP cơ bản, chờ User duyệt.
- `v1.x`: sửa sau duyệt (ghi rõ DEC nào đổi).
- `v2.0`: thêm phạm vi phase 2 (mục tiêu, leaderboard, PDF) — duyệt lại phần mới.

## Mẫu dòng nhật ký mới
- Ngày theo `DD/MM/YYYY Asia/Ho_Chi_Minh`; Trạng thái `PROPOSED/CONFIRMED`.
- Ví dụ: "Chốt DEC-601 ngưỡng 5 phút, cập nhật tệp 05/15/16".
- Không sửa dòng cũ, chỉ thêm dòng mới trên cùng.

## Lịch sử chi tiết vòng v1.0
- Vòng 1: dựng khung 19 tệp + flowchart L0/L1/L2 + máy trạng thái Daily/Weekly.
- Vòng 2: bổ sung BR-D, ví dụ số, KPI, thứ tự triển khai, giám sát sau phát hành.
- Vòng 3 (hiện tại): rà soát độ dài ≥ 40 dòng/tệp, kiểm tra trace BR➔test.
- Việc còn lại: User chốt DEC-601/602 ➔ CONFIRMED ➔ bàn giao song song module 05.
