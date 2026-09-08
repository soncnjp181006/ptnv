# 19 — Nhật Ký Thay Đổi: Quiz & Flashcard

> Giờ `Asia/Ho_Chi_Minh`; mỗi sửa BR/DEC/UC thêm dòng mới.

## 1. Bảng thay đổi
| Ngày | Phiên bản | Tác giả | Nội dung | Lý do | Ảnh hưởng |
|---|---|---|---|---|---|
| 08/09/2026 | 0.1 | Agent phân tích | Khởi tạo 19 tệp BV-04 | Yêu cầu phân tích sâu | Baseline |
| 08/09/2026 | 0.2 | Agent phân tích | Chốt ledger quota chung với BV-03, 1 lượt/lần sinh | Quy ước + minh bạch | BR-001, DEC-01/02 |
| 08/09/2026 | 0.3 | Agent phân tích | Chuẩn attempt DRAFT→IN_PROGRESS→SUBMITTED→SCORED | Quy ước chung | UC-04/05, file 04 |
| 08/09/2026 | 0.4 | Agent phân tích | Thêm validate JSON 4 options + khớp RAG | Chống hallucination | BR-004/005 |
| 08/09/2026 | 0.5 | Agent phân tích | Thêm retry partial/timeout + hoàn quota | Partial completion | BR-006/007 |
| 08/09/2026 | 0.6 | Agent phân tích | Thêm version chống nộp đôi + guard giải thích | Concurrent/gian lận | BR-008/009 |
| 08/09/2026 | 1.0 | Agent phân tích | Baseline phân tích sâu | Đủ 19 tệp, Gate đạt | Toàn module |

## 2. Đề xuất chưa duyệt
- P-01: xáo trộn options mỗi attempt (sprint 3).
- P-02: cache đề theo `(docHash,count,difficulty)` (patch 1.1).
- P-03: PUBLISHED + reminder SM-2 push (phase 2).

## 3. Quy ước
- Baseline `1.0`; patch nhỏ `1.1`, đổi luồng minor `1.2`; không sửa dòng cũ.

## 4. Liên kết
- Sự thật: `17-tong_hop.md`; truy vết: `09-ma-tran-truy-vet.md`.
- Review tiếp: cuối sprint 2 xét P-01→P-03.

## 5. Mẫu dòng thay đổi chuẩn
- Ngày DD/MM/YYYY theo `Asia/Ho_Chi_Minh`; bản baseline `1.0`.
- Sửa nhỏ patch, đổi luồng minor; cột Ảnh hưởng ghi mã UC/BR/DEC.
- Người sửa ngoài Agent ghi tên + vai trò (PO/QA/BE).
- Không sửa/xóa dòng đã phát hành, sai thì ghi dòng đính chính mới.

## 6. Quy trình đề xuất
1. Thêm dòng P-XX ở mục 2 kèm lý do và ảnh hưởng dự kiến.
2. Đối chiếu file 09 (truy vết) trước khi PO duyệt.
3. Duyệt → dòng phiên bản mới ở mục 1; từ chối → ghi lý do.
4. Mọi P-XX quá 2 sprint chưa xử lý phải review lại hoặc đóng.

## 7. Lưu trữ
- Append-only; snapshot 19 tệp vào `versions/` mỗi release.
- Template dòng mới copy từ dòng 08/09/2026 để giữ đúng cột.
- File log được review cùng lúc với review truy vết cuối sprint.
- Cuối sprint QA lead đối chiếu log với code đã merge.
- Mọi release minor đều có ít nhất 1 dòng log, không release câm.
- Snapshot đặt tên `v<major>.<minor>_<yyyymmdd>` theo giờ `Asia/Ho_Chi_Minh`.
- Giữ 5 snapshot gần nhất, cũ hơn nén lạnh 12 tháng.
- Khôi phục từ snapshot test 1 lần/quý để chắc chắn dùng được.
