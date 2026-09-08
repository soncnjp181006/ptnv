# 04 — Mô Hình Trạng Thái: Luyện Tập Quiz & Flashcard

> Thực thể: `QuizSet`, `GenerationJob`, `QuizAttempt` (DRAFT→IN_PROGRESS→SUBMITTED→SCORED), `FlashcardCard`.

## 1. QuizSet
| Trạng thái | Ý nghĩa |
|---|---|
| `GENERATING` | Đang gọi LLM sinh |
| `DRAFT` | Sinh xong, sẵn sàng làm bài |
| `PUBLISHED` | Teacher phát hành cho lớp (optional) |
| `ARCHIVED` | Lưu trữ, vẫn làm lại được |
| `DISCARDED` | Hủy do JSON lỗi/hallucination nặng |

- `GENERATING --> DRAFT` (pass validate) hoặc `--> DISCARDED` (fail sau retry).
- `DRAFT --> PUBLISHED --> ARCHIVED`; `ARCHIVED` không quay lại `GENERATING`.

## 2. GenerationJob (sinh AI)
| Trạng thái | Ý nghĩa |
|---|---|
| `QUEUED` | Vừa hold quota, chờ worker |
| `RUNNING` | Đang gọi RAG + LLM |
| `PARTIAL` | LLM trả thiếu câu (JSON thiếu) |
| `SUCCEEDED` | Đủ câu, validate pass, đã lưu DRAFT |
| `FAILED_RETRYABLE` | Timeout/5xx, được retry 1 lần |
| `FAILED_FINAL` | Hết retry, đã rollback quota |

- `RUNNING --> PARTIAL --> RUNNING` (1 vòng retry) hoặc `--> SUCCEEDED`.
- Timeout-nhưng-local-success: job đã `SUCCEEDED` ngầm → retry trả cached.

## 3. QuizAttempt — trục chuẩn quy ước chung
| Trạng thái | Ý nghĩa | Chuyển tiếp |
|---|---|---|
| `DRAFT` | Tạo nháp, chưa bấm bắt đầu | → `IN_PROGRESS` (start) |
| `IN_PROGRESS` | Đang làm, có `deadlineAt` | → `SUBMITTED` (nộp tay/hết giờ) |
| `SUBMITTED` | Đã nộp, chờ chấm (chấm local ~ms) | → `SCORED` tự động |
| `SCORED` | Đã có điểm + giải thích | Kết thúc; làm lại tạo attempt mới |
| `EXPIRED` | Quá hạn chưa nộp | Auto-submit phần đã làm → `SCORED` |
| `ABANDONED` | Thoát giữa chừng (user hủy) | Không chấm, giữ history dở |

- Guard: chỉ xem `explanation` khi `SCORED`; `SUBMITTED` không sửa đáp án.
- Concurrent: `version` optimistic; submit 2 lần → 1 thắng `SCORED`, 1 nhận 409.

## 4. FlashcardCard (SM-2)
| Trạng thái | Ý nghĩa |
|---|---|
| `NEW` | Mới sinh, chưa ôn |
| `LEARNING` | Đang ôn (< 3 lần GOOD) |
| `REVIEW` | Thuộc, chờ lịch `nextReviewAt` |
| `RELEARNING` | Trả lời AGAIN, quay lại ôn dày |

## 5. Audit và khóa
- Mọi chuyển `QuizAttempt` ghi `AuditLog {from,to,at}` giờ `Asia/Ho_Chi_Minh`.
- Cấm `SCORED --> IN_PROGRESS`; cấm sửa `correctOption` sau khi có attempt SCORED (phải tạo bản `QuizSet` mới).
