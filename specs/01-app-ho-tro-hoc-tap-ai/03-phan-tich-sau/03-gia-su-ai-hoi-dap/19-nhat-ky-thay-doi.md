# 19 — Nhật Ký Thay Đổi: Gia Sư AI Hỏi Đáp

> Ghi mọi thay đổi phân tích; giờ `Asia/Ho_Chi_Minh`.

## 1. Bảng thay đổi
| Ngày | Phiên bản | Tác giả | Nội dung thay đổi | Lý do | Ảnh hưởng |
|---|---|---|---|---|---|
| 08/09/2026 | 0.1 | Agent phân tích | Khởi tạo 19 tệp BV-03 | Yêu cầu phân tích sâu | Baseline |
| 08/09/2026 | 0.2 | Agent phân tích | Chốt quota Free 20/ngày, reset 00:00 `Asia/Ho_Chi_Minh` | Quy ước chung | BR-001/009, UC-02 |
| 08/09/2026 | 0.3 | Agent phân tích | Thêm hold→commit/rollback + atomic decrement | Chống concurrent vượt quota | BR-002, SC-UC02-03 |
| 08/09/2026 | 0.4 | Agent phân tích | Thêm Idempotency-Key 24h + xử lý local-success | Chống double-charge retry | BR-003, SC-UC10-01 |
| 08/09/2026 | 0.5 | Agent phân tích | Thêm retry 1 lần + fallback + hoàn quota | Partial completion/timeout | BR-006/007 |
| 08/09/2026 | 0.6 | Agent phân tích | Thêm citation bắt buộc + thú nhận thiếu ngữ cảnh | Chống hallucination | BR-004/010 |
| 08/09/2026 | 1.0 | Agent phân tích | Phát hành baseline phân tích sâu | Đủ 19 tệp, Gate đạt | Toàn module |

## 2. Thay đổi đang đề xuất (chưa duyệt)
- P-01: numeric-check số liệu tự động (chờ POC sprint 3).
- P-02: cache câu hỏi hot giảm chi phí (chờ số liệu staging).
- P-03: xuất PDF Paid UC-14 (dời phase 2).

## 3. Quy ước ghi nhật ký
- Mỗi sửa BR/DEC/UC phải thêm 1 dòng mới, không sửa dòng cũ.
- Phiên bản baseline `1.0`; thay đổi nhỏ tăng patch (`1.1`), đổi luồng tăng minor (`1.2`).

## 4. Liên kết
- Nguồn sự thật: `17-tong_hop.md`; truy vết: `09-ma-tran-truy-vet.md`.
- Review tiếp theo: cuối sprint 2, rà soát P-01→P-03.

## 5. Mẫu dòng thay đổi chuẩn
- Cột Ngày dùng định dạng DD/MM/YYYY theo `Asia/Ho_Chi_Minh`.
- Cột Phiên bản: baseline `1.0`, sửa nhỏ patch, đổi luồng minor.
- Cột Ảnh hưởng liệt kê đúng mã UC/BR/DEC/SC bị tác động.
- Người sửa ngoài Agent phải ghi rõ tên và vai trò (PO/QA/BE).

## 6. Quy trình đề xuất thay đổi
1. Tạo dòng P-XX ở mục 2, mô tả đề xuất và lý do.
2. Đánh giá ảnh hưởng lên BR/DEC/TC trong file 09 trước khi duyệt.
3. PO duyệt → chuyển thành dòng phiên bản mới ở mục 1.
4. Từ chối cũng ghi 1 dòng, nêu lý do để tránh đề xuất lặp.

## 7. Lưu trữ
- File này là append-only; cấm sửa/xóa dòng đã phát hành baseline.
- Mỗi release đính kèm snapshot 19 tệp vào thư mục `versions/`.
- Cuối mỗi sprint, QA lead đối chiếu log với code đã merge.
- Thiếu log → task debt ưu tiên P2 cho sprint sau.
- Snapshot đặt tên `v<major>.<minor>_<yyyymmdd>` theo giờ `Asia/Ho_Chi_Minh`.
- Giữ 5 snapshot gần nhất, cũ hơn nén lưu trữ lạnh 12 tháng.
- Khôi phục từ snapshot phải test 1 lần/quý để chắc chắn dùng được.
