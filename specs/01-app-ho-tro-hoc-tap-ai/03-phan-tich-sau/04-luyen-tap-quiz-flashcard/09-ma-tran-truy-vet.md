# 09 — Ma Trận Truy Vết: Quiz & Flashcard

> Yêu cầu → UC → SC → BR → TC.

## 1. Ma trận chính
| Yêu cầu | Use Case | Kịch bản | Business Rule | Kiểm thử |
|---|---|---|---|---|
| REQ-QZ-01 Sinh Quiz AI có cấu hình | UC-01 | SC-UC01-01/02 | BR-001/010 | TQ-01 |
| REQ-QZ-02 Sinh Flashcard AI | UC-02 | SC-UC02-01 | BR-001/004 | TQ-02 |
| REQ-QZ-03 Quota sinh trước LLM | UC-03 | SC-UC03-01/02/03 | BR-001/007 | TQ-03,07 |
| REQ-QZ-04 Làm bài DRAFT→…→SCORED | UC-04/05 | SC-UC04-01, SC-UC05-01/02 | BR-002/008 | TQ-04 |
| REQ-QZ-05 Giải thích sau chấm | UC-06 | SC-UC06-01/02 | BR-009 | TQ-05 |
| REQ-QZ-06 Làm lại/ôn sai | UC-07 | SC-UC07-01/02 | BR-002 | TQ-06 |
| REQ-QZ-07 Ôn SM-2 + history | UC-08/09 | SC-UC08-01 | BR-002 | TQ-06 |
| REQ-QZ-08 Báo câu sai | UC-10 | SC-UC10-01 | BR-010 | TQ-08 |
| REQ-QZ-09 Retry partial/timeout | UC-11 | SC-UC11-01/02/03 | BR-003/006/007 | TQ-09 |
| REQ-QZ-10 Chống double-submit | UC-12 | SC-UC05-03 | BR-003/008 | TQ-10 |
| REQ-QZ-11 Quota chung QA+Quiz | UC-13 | SC-UC03-03, SC-UC13-01 | BR-001 | TQ-07 |
| REQ-QZ-12 Validate đáp án duy nhất | UC-01 | SC-UC01-03/04 | BR-004/005 | TQ-11 |

## 2. Bao phủ hai chiều
- Mọi REQ có UC+BR+TC; không BR mồ côi; TC tham chiếu 1 REQ chính.

## 3. Gap
- GAP-01: PUBLISHED cho lớp (teacher) chưa có TC → sprint 3.
- GAP-02: reminder SM-2 push chưa có TC E2E → backlog notification.

## 4. Duy trì
- Thêm REQ phải thêm hàng trước code; đổi BR gắn cờ STALE cho TQ liên quan.
- Review cuối sprint, mục tiêu Must 100%.

## 5. Liên kết
- Nguồn: `01`, `05`, `16` cùng thư mục; check CI "UC Must có TQ".

## 6. Ví dụ truy vết mẫu end-to-end
- REQ-QZ-09 (retry sinh) → UC-11 → SC-UC11-02 (partial JSON 7/10 câu).
- SC-UC11-02 → BR-006 (retry 1 lần) + BR-007 (hoàn quota).
- BR-006/007 → TQ-09 (timeout/partial) + TQ-03 (hoàn ledger).
- TQ-09 pass staging → REQ-QZ-09 đóng (QA lead ký duyệt).
- Đổi BR-006 phải chạy lại TQ-09 trước khi merge.

## 7. Định kỳ rà soát
- Cuối mỗi sprint quét BR mồ côi và TQ STALE trong sheet `trace-quiz`.
- GAP-01/02 đóng trước M3, nếu không phải ghi debt P2.
- Sheet `trace-quiz` có cột trạng thái: COVERED / STALE / GAP.
- Báo cáo bao phủ gửi PO trước sprint review 1 ngày.
- REQ mới từ incident production gắn cờ `HOTFIX` ưu tiên xử lý.
- Không đóng sprint khi còn REQ Must ở trạng thái GAP.
