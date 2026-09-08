# Ma Trận Truy Vết Đặt Bàn (09-ma-tran-truy-vet.md)

Vị trí: `specs/01-app-nha-hang/03-phan-tich-sau/01-dat-ban/09-ma-tran-truy-vet.md`

| Requirement | Capability | Use Case | Scenario | Rule / State | Data / Effect | Acceptance |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Giữ chỗ fine dining | `reservation` | UC-01, UC-04 | Tạo giữ tạm, xếp bàn | BR-S01, `HOLD` sang `RESERVED` | `reservation`, `table` | Tạo được giữ tạm khi còn bàn |
| Chống bom bàn | `reservation` | UC-03 | Cọc nhóm 6+ và lễ | BR-F01 `CONFIRMED` | Giao dịch cọc | Không cọc thì không `RESERVED` |
| Giữ 60 phút | `reservation` | UC-05, UC-07 | Đến muộn, auto-release | BR-T01 `CONFIRMED` | `RESERVED` sang `RELEASED` | Quá 60 phút tự nhả và xử lý cọc |
| Chống trùng bàn | `table_management` | UC-08 | Concurrent cùng bàn | BR-C01 | `table_id` | Một bên thắng, bên còn lại đề xuất lại |
| Truy vết khiếu nại | `reporting` | UC-06, UC-07 | Hủy, phạt, hoàn | BR-AU01 | `audit` | Mọi phạt / hoàn đều có audit |

Không có orphan rule hoặc scenario lõi.
