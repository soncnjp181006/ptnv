# 03 — Ma Trận Kịch Bản: Luyện Tập Quiz & Flashcard

> Bao phủ: quota sinh, idempotency, partial JSON, concurrent nộp, timeout-local-success.

## 1. Nguyên tắc
- Mã `SC-<UC>-<NN>`; loại MAIN/ALT/EXC; sinh đề tốn quota, làm bài miễn phí.

## 2. Ma trận
| Mã SC | Use Case | Loại | Kịch bản | Kết quả |
|---|---|---|---|---|
| SC-UC01-01 | UC-01 | MAIN | Sinh 10 câu MEDIUM còn quota | `QuizSet DRAFT` 10 câu, trừ 1 |
| SC-UC01-02 | UC-01 | ALT | Free đòi 20 câu/lần | Giới hạn 10, gợi ý nâng cấp |
| SC-UC01-03 | UC-01 | EXC | JSON LLM có 2 đáp án đúng | Validate fail → regenerate 1 lần |
| SC-UC01-04 | UC-01 | EXC | Hallucination: đáp án không có trong chunk | Gắn NEEDS_REVIEW, vẫn lưu DRAFT cảnh báo |
| SC-UC02-01 | UC-02 | MAIN | Sinh deck 20 thẻ còn quota | `FlashcardDeck` đủ trước/sau, trừ 1 |
| SC-UC03-01 | UC-03 | MAIN | Hold 1 quota thành công | Commit sau SUCCEEDED |
| SC-UC03-02 | UC-03 | EXC | Hết quota sinh (0/20) | 429 + paywall, không gọi LLM |
| SC-UC03-03 | UC-03 | EXC | Hết quota giữa chừng (QA + Quiz concurrent) | Ledger chung atomic, 1 thắng 1 thua |
| SC-UC04-01 | UC-04 | MAIN | Start attempt từ DRAFT | `IN_PROGRESS` + `deadlineAt` |
| SC-UC05-01 | UC-05 | MAIN | Nộp đủ 10/10 đúng giờ | `SUBMITTED → SCORED` + điểm |
| SC-UC05-02 | UC-05 | ALT | Nộp thiếu câu (bỏ trống 2) | Chấm câu trống = sai, vẫn SCORED |
| SC-UC05-03 | UC-05 | EXC | Double-submit concurrent 2 tab | Dedupe version, 1 SCORED duy nhất |
| SC-UC06-01 | UC-06 | MAIN | Xem giải thích sau SCORED | Hiện explanation + SourceRef |
| SC-UC06-02 | UC-06 | ALT | Xem giải thích khi IN_PROGRESS | 403, phải nộp mới xem |
| SC-UC07-01 | UC-07 | MAIN | Làm lại toàn bộ | Attempt mới, giữ history cũ |
| SC-UC07-02 | UC-07 | ALT | Ôn chỉ câu sai | Attempt con lọc `wrongOnly=true` |
| SC-UC08-01 | UC-08 | MAIN | Ôn SM-2 đánh GOOD | Tính `nextReviewAt` + streak |
| SC-UC10-01 | UC-10 | MAIN | Báo câu sai hợp lệ | Tạo report PENDING |
| SC-UC11-01 | UC-11 | MAIN | Timeout 45s nhưng đã lưu nháp | Retry cùng key trả cached, 1 quota |
| SC-UC11-02 | UC-11 | ALT | Partial JSON thiếu 3/10 câu | Retry 1; vẫn thiếu → hoàn quota + báo lỗi |
| SC-UC11-03 | UC-11 | EXC | Retry khác key | Tính lần sinh mới, trừ quota mới |
| SC-UC13-01 | UC-13 | MAIN | Xem quota chung QA+Quiz | `used/remaining/resetAt` đúng ngày VN |

## 3. Retry sau partial completion (sinh đề)
1. POST generate key `k2` → hold quota → LLM trả 7/10 câu rồi timeout.
2. Retry `k2` sau 3s: server thấy job `RUNNING` → chờ 40s.
3. Job đầu hồi phục và lưu đủ 10 → trả cached DRAFT.
4. Job đầu fail hẳn → 1 lần gọi LLM mới tái dùng hold.
5. Cả 2 fail → rollback hold, `503 + fallback`, nút "Tạo lại" (key mới).

## 4. Concurrent nộp bài
- Mỗi attempt có `version`; submit mang `version`; server `UPDATE ... WHERE version=X` rồi tăng.
- Tab thua nhận `409 ATTEMPT_ALREADY_SUBMITTED` + link kết quả đã chấm.

## 5. Độ phủ
- 23 kịch bản: 8 MAIN, 7 ALT, 8 EXC; 100% UC Must có EXC.

## 6. Bảo trì ma trận
- Thêm SC mới khi phát hiện EXC ngoài thực tế (incident → SC hồi tố).
- Mỗi SC EXC phải có TQ tương ứng trong file 16 trước khi đóng sprint.
- Review SC dư thừa mỗi quý: SC 3 sprint không xảy ra được gắn cờ `RARE`.
