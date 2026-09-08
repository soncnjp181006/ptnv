# 19 — Nhật Ký Thay Đổi: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Ghi mọi thay đổi phân tích module 05. Mới nhất trên cùng.

| Ngày | Phiên bản | Nội dung | Tác giả | Trạng thái |
| :--- | :--- | :--- | :--- | :--- |
| 08/09/2026 | v1.0 | Khởi tạo đủ 19 tệp: giá Free/99k/990k, webhook idempotent, reconcile UNKNOWN, grace 3 ngày | Agent phân tích | PROPOSED |
| 08/09/2026 | v0.9 | Dự thảo BR-F/BR-T/BR-X/BR-RV, máy trạng thái Order + Subscription | Agent phân tích | PROPOSED |
| 08/09/2026 | v0.5 | Phác thảo L0/L1/L2, chốt quy ước VND + cutoff 00:00 + grace 3 ngày | Agent phân tích | PROPOSED |

## Quy ước cập nhật
- Mỗi lần chốt 1 DEC (đặc biệt DEC-501/502/506) phải thêm dòng mới + cập nhật tệp 05, 06, 15.
- Thay đổi giá/quota là breaking change: tăng `planVersion`, migration dữ liệu `Subscription` cũ.

## Quy ước phiên bản
- `v0.x`: dự thảo phân tích, chưa đủ 19 tệp hoặc còn mục UNRESOLVED.
- `v1.0`: đủ 19 tệp, mọi BR có test trace, chờ User duyệt.
- `v1.x`: sửa sau duyệt (ghi rõ DEC nào đổi, tệp nào ảnh hưởng).
- `v2.0`: đổi phạm vi lớn (thêm cổng, đổi giá, proration) — cần duyệt lại toàn module.

## Mẫu dòng nhật ký mới
- Cột Ngày ghi theo `DD/MM/YYYY Asia/Ho_Chi_Minh`; cột Trạng thái dùng `PROPOSED/CONFIRMED`.
- Nội dung ghi dạng động từ + phạm vi ảnh hưởng, ví dụ: "Chốt DEC-501 dùng VNPay+Stripe, cập nhật tệp 05/08/16".
- Không sửa dòng cũ, chỉ thêm dòng mới trên cùng; ai sửa ghi tên ở cột Tác giả.

## Lịch sử chi tiết vòng v1.0
- Vòng 1: dựng khung 19 tệp + flowchart L0/L1/L2 + máy trạng thái kép (Order/Subscription).
- Vòng 2: bổ sung BR-N/BR-AU, ví dụ số hoàn partial, KPI, runbook reconcile, chaos test.
- Vòng 3 (hiện tại): rà soát độ dài ≥ 40 dòng/tệp, sửa lỗi chính tả, kiểm tra trace BR➔test đủ 100%.
- Việc còn lại: User chốt DEC-501/502/506 ➔ chuyển v1.0 sang CONFIRMED ➔ bàn giao thiết kế.

## Biểu mẫu đề xuất thay đổi (CR) — copy khi cần
- Mã CR: `CR-5xx` | Ngày đề xuất: `DD/MM/YYYY` | Người đề xuất: `...`
- Nội dung đổi: `...` | Lý do: `...` | Tệp ảnh hưởng: `...`
- Tác động tiền/số liệu: `...` | Quyết định: `DUYỆT/TỪ CHỐI` | Người duyệt: `...`

## Danh sách kiểm tra trước khi giao thiết kế (handoff)
- [ ] 19/19 tệp tồn tại, mỗi tệp 40–120 dòng, không placeholder.
- [ ] Tệp 01 có đủ 3 flowchart L0/L1/L2, edge đánh số, branch 3A/3B/6A/6B/6C.
- [ ] Tệp 05 đủ nhóm BR-F/BR-T/BR-X/BR-RV; tệp 06 toàn PROPOSED.
- [ ] Mọi BR trace tới test (tệp 09); không lỗi chính tả nghiêm trọng.
- [ ] DEC-501/502/506 có người + hạn chốt rõ ràng.

## Mẫu biên bản chốt DEC (copy khi họp)
- Ngày họp: `DD/MM/YYYY` | Người tham gia: `PO, ...` | DEC: `DEC-5xx`
- Phương án chốt: `...` | Lý do: `...` | Tệp phải sửa sau chốt: `...`
- Người cập nhật tệp: `...` | Hạn cập nhật: `...`

## Lưu ý lưu trữ
- Tệp này là nguồn sự thật về lịch sử phân tích; cấm force-ghi đè mất dòng cũ.
- Khi module sang thiết kế (design), thêm 1 dòng chốt `HANDOFF ➔ design` kèm ngày.
