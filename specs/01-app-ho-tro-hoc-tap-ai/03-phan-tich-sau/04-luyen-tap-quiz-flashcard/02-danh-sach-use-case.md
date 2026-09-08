# 02 — Danh Sách Use Case: Luyện Tập Quiz & Flashcard

> Module: `quiz-flashcard` | Mã: `BV-04` | Múi giờ: `Asia/Ho_Chi_Minh`

## 1. Tổng quan
- Actor: `Student` (Free/Paid), `Teacher` (tạo đề từ tài liệu), `Admin`, System `LlmProvider`.
- Quota AI dùng chung `AiQuotaLedger` với BV-03: Free 20 lượt/ngày; mỗi lần sinh Quiz/Flashcard tốn 1 lượt.
- Làm bài, xem giải thích, làm lại, ôn flashcard không tốn quota.

## 2. Danh sách Use Case
| Mã | Tên Use Case | Actor | Mô tả | Ưu tiên |
|---|---|---|---|---|
| UC-01 | Cấu hình và sinh Quiz AI | `Student`/`Teacher` | Chọn `Document`, `questionCount` 5/10/20, `difficulty` EASY/MEDIUM/HARD | Must |
| UC-02 | Sinh Flashcard AI | `Student` | Sinh `FlashcardDeck` 10–50 thẻ từ tài liệu, mặt trước/sau | Must |
| UC-03 | Kiểm tra quota sinh đề | System | Hold 1 quota trước gọi LLM sinh | Must |
| UC-04 | Bắt đầu làm bài | `Student` | Tạo `QuizAttempt IN_PROGRESS` từ `QuizSet` | Must |
| UC-05 | Nộp bài và chấm điểm | `Student` | `IN_PROGRESS → SUBMITTED → SCORED`, chấm local tức thì | Must |
| UC-06 | Xem giải thích từng câu | `Student` | Hiện `explanation` + `SourceRef` sau khi SCORED | Must |
| UC-07 | Làm lại / ôn sai | `Student` | Tạo attempt mới từ cùng `QuizSet` hoặc chỉ câu sai | Should |
| UC-08 | Ôn Flashcard SM-2 | `Student` | Lật thẻ, đánh giá `AGAIN/HARD/GOOD/EASY`, tính lịch ôn | Should |
| UC-09 | Lưu lịch sử luyện tập | System | Ghi `AttemptHistory`, thống kê điểm, streak | Should |
| UC-10 | Báo câu hỏi sai | `Student` | Báo `QuizQuestion` sai đáp án/giải thích, tối đa 5/ngày | Should |
| UC-11 | Retry sinh dở dang | System | `GenerationJob` fail → retry 1 lần cùng key, không trừ thêm | Must |
| UC-12 | Chặn concurrent nộp đôi | System | `attemptVersion` + idempotency nộp bài | Must |
| UC-13 | Xem quota sinh còn lại | `Student` | Hiển thị quota dùng chung QA+Quiz | Must |
| UC-14 | Xóa/lưu trữ bộ đề | `Student`/`Teacher` | Archive `QuizSet`, xóa mềm deck | Could |

## 3. Đặc tả trọng yếu
### UC-01 — Cấu hình và sinh Quiz
- Tiền điều kiện: `Document` đã ingest xong, còn quota.
- Luồng: cấu hình → validate → hold → RAG → LLM JSON → validate đáp án duy nhất → lưu `DRAFT`.
- Hậu điều kiện: `QuizSet DRAFT` + `GenerationJob SUCCEEDED`, trừ 1 quota.

### UC-05 — Nộp bài và chấm điểm
- Chỉ chấm khi `Attempt IN_PROGRESS` và hết giờ hoặc user nộp tay.
- Chấm local so `selectedOption` với `correctOption`; không gọi LLM nên không tốn quota.
- Double-submit cùng `attemptId + version` → dedupe, giữ 1 `SCORED`.

### UC-11 — Retry sinh dở dang
- LLM timeout 45s nhưng đã lưu nháp (`local success`) → retry cùng key trả cached.
- Partial JSON (thiếu câu) → retry 1 lần; vẫn thiếu → lưu bản partial gắn cờ + hoàn quota 50%? Không — hoàn toàn bộ (BR-007).

## 4. Ma trận Actor ↔ Use Case
| Actor | Use Case |
|---|---|
| `Student` Free | UC-01 (giới hạn 10 câu/lần), UC-02, UC-04→UC-10, UC-13 |
| `Student` Paid / Teacher | + UC-01 tới 20 câu/lần, UC-14 |
| System | UC-03, UC-09, UC-11, UC-12 |
| `Admin` | Duyệt báo câu sai |

## 5. Định danh
- `QuizSet`: `quiz_{ulid}`; `QuizAttempt`: `att_{ulid}`; `FlashcardDeck`: `deck_{ulid}`.
- `Idempotency-Key` cho POST generate và POST submit.

## 6. Ngoài phạm vi
- Thi giám sát (proctoring), trộn đề thi chính thức, chấm tự luận AI.
