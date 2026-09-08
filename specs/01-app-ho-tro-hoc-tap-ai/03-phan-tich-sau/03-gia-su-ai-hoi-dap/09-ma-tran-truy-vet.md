# 09 — Ma Trận Truy Vết: Gia Sư AI Hỏi Đáp

> Truy vết: Yêu cầu → Use Case → Kịch bản → BR → Kiểm thử.

## 1. Ma trận chính
| Yêu cầu gốc | Use Case | Kịch bản | Business Rule | Kiểm thử |
|---|---|---|---|---|
| REQ-QA-01 Hỏi đáp theo ngữ cảnh RAG | UC-01, UC-03 | SC-UC01-01, SC-UC03-01 | BR-004 | TC-01, TC-03 |
| REQ-QA-02 Kiểm tra quota trước LLM | UC-02 | SC-UC02-01/02/03 | BR-001, BR-002 | TC-02, TC-07 |
| REQ-QA-03 Trích dẫn nguồn | UC-04 | SC-UC04-01/02 | BR-004, BR-010 | TC-03, TC-04 |
| REQ-QA-04 Đánh giá hữu ích | UC-05 | SC-UC05-01/02 | BR-008 | TC-05 |
| REQ-QA-05 Báo cáo câu sai | UC-06, UC-11 | SC-UC06-01, SC-UC11-01 | BR-008 | TC-05, TC-11 |
| REQ-QA-06 Lọc độc hại | UC-09 | SC-UC01-03, SC-UC09-01 | BR-005 | TC-06 |
| REQ-QA-07 Retry/timeout/fallback | UC-10 | SC-UC10-01/02/03 | BR-003, BR-006 | TC-08, TC-09 |
| REQ-QA-08 Idempotency quota | UC-10 | SC-UC10-01 | BR-003 | TC-08 |
| REQ-QA-09 Concurrent an toàn | UC-13 | SC-UC13-01, SC-UC02-03 | BR-002 | TC-07 |
| REQ-QA-10 Hallucination guard | UC-04 | SC-UC04-03 | BR-004, BR-010 | TC-04 |
| REQ-QA-11 Hoàn quota lỗi hệ thống | UC-02 | SC-UC10-02 | BR-007 | TC-09 |
| REQ-QA-12 Reset quota daily | UC-12 | SC-UC12-01 | BR-009 | TC-10 |

## 2. Bao phủ hai chiều
- Mọi REQ có ít nhất 1 UC + 1 BR + 1 TC; không có BR mồ côi.
- Mọi TC tham chiếu ngược đúng 1 REQ chính (cột đầu).

## 3. Khoảng trống (gap)
- GAP-01: xuất hội thoại Paid (UC-14) chưa có TC E2E → bổ sung ở sprint 3.
- GAP-02: metric hallucination rate chưa có dashboard → tạo task observability.

## 4. Quy tắc duy trì
- Thêm REQ mới phải cập nhật hàng mới trong bảng trước khi code.
- Đổi BR phải rà soát cột Kiểm thử, gắn cờ `STALE` cho TC liên quan.
- Review truy vết mỗi cuối sprint; tỉ lệ bao phủ mục tiêu 100% Must.

## 5. Liên kết tài liệu
- Nguồn: `01-mo-hinh-quy-trinh-nghiep-vu.md`, `05-bo-quy-tac-nghiep-vu.md`, `16-ke-hoach-kiem-thu.md`.
- Công cụ: sheet `trace-qa` + check CI "mọi UC Must có TC".

## 6. Ví dụ truy vết mẫu end-to-end
- REQ-QA-07 (retry/timeout) → UC-10 → SC-UC10-01 (local success trả cached).
- SC-UC10-01 → BR-003 (idempotency) + BR-006 (retry 1 lần).
- BR-003/006 → TC-08 (dedupe key) + TC-09 (partial completion).
- TC-08/09 pass trên staging → REQ-QA-07 coi như đóng (QA lead ký duyệt).
- Mọi thay đổi BR-003 phải chạy lại TC-08/09 trước khi merge.

## 7. Định kỳ rà soát
- Cuối mỗi sprint: quét BR mồ côi và TC STALE, cập nhật sheet `trace-qa`.
- Sheet `trace-qa` có cột trạng thái: COVERED / STALE / GAP.
- Báo cáo bao phủ gửi PO trước sprint review 1 ngày.
