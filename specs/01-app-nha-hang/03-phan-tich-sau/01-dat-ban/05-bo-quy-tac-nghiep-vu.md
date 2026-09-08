# Bộ Quy Tắc Nghiệp Vụ Đặt Bàn (05-bo-quy-tac-nghiep-vu.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/01-dat-ban/05-bo-quy-tac-nghiep-vu.md`
Định dạng: WHEN / MUST / OTHERWISE / AFFECTS / SOURCE / STATUS.

- BR-V01: WHEN ngày đặt ở quá khứ hoặc điểm đóng cửa, THE `system` MUST từ chối và gợi ý ngày khác. OTHERWISE không tạo `reservation`. AFFECTS `reservation`. SOURCE `DERIVED` từ UC-01. STATUS `PROPOSED`.
- BR-S01: WHEN bàn ở `HOLD` quá hạn giữ tạm, THE `system` MUST chuyển `RELEASED` và nhả bàn. OTHERWISE bàn bị treo. AFFECTS `table_id`. SOURCE `AGENT_INFERENCE`. STATUS `PROPOSED`.
- BR-T01: WHEN khách đến muộn, THE `system` MUST giữ bàn 60 phút cho mô hình fine dining rồi mới auto-release, kèm nhắc khách ở phút 15, 30, 50. OTHERWISE mất trải nghiệm phân khúc cao cấp. AFFECTS `RESERVED` sang `RELEASED`. SOURCE DEC-R01. STATUS `CONFIRMED`.
- BR-P01: WHEN đổi / hủy đặt bàn có cọc, THE `receptionist` MUST có quyền xác nhận, `branch_manager` duyệt phạt cọc vượt ngưỡng. OTHERWISE không cho xử lý cọc. AFFECTS `payment`. SOURCE `AGENT_INFERENCE`. STATUS `PROPOSED`.
- BR-C01: WHEN hai thao tác cùng giữ một `table_id` trùng giờ, THE `system` MUST chỉ chấp nhận một bên tới trước và đề xuất lại bên còn lại. OTHERWISE trùng bàn. AFFECTS `table_id`. SOURCE `AGENT_INFERENCE`. STATUS `PROPOSED`.
- BR-F01: WHEN nhóm từ 6 khách trở lên hoặc đặt bàn ngày lễ / cuối tuần, THE `system` MUST yêu cầu cọc trước khi `RESERVED`. OTHERWISE không cho giữ chính thức. AFFECTS `reservation`. SOURCE DEC-R02. STATUS `CONFIRMED`.
- BR-N01: WHEN tạo / hủy / sắp tới giờ, THE `system` MUST gửi thông báo theo template và kênh đã cấu hình. OTHERWISE khách không biết trạng thái. AFFECTS `notification`. SOURCE `AGENT_INFERENCE`. STATUS `PROPOSED`.
- BR-AU01: WHEN auto-release / phạt / hoàn cọc xảy ra, THE `system` MUST ghi audit gồm actor, thời điểm, lý do. OTHERWISE không truy vết khiếu nại. AFFECTS `audit`. SOURCE `AGENT_INFERENCE`. STATUS `PROPOSED`.
