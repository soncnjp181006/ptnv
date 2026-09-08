# 07 — Ảnh Hưởng Dữ Liệu và Tác Động: Quiz & Flashcard

> Entity, migration, ledger chung, history, SM-2.

## 1. Mô hình dữ liệu
| Entity | Khóa | Trường chính | Ghi chú |
|---|---|---|---|
| `QuizSet` | `quizId` | `docIds, count, difficulty, status, version` | DRAFT→… |
| `QuizQuestion` | `qId` | `quizId, stem, options[4], correctOption, explanation, sourceRef` | Immutable sau SCORED |
| `QuizAttempt` | `attId` | `quizId, userId, status, version, score, answers[]` | DRAFT→…→SCORED |
| `AttemptHistory` | `histId` | `attId, score, durationMs, wrongIds` | Thống kê |
| `FlashcardDeck` | `deckId` | `docIds, status` | 10–50 thẻ |
| `FlashcardCard` | `cardId` | `front, back, ease, interval, nextReviewAt, state` | SM-2 |
| `GenerationJob` | `jobId` | `type QUIZ/DECK, status, idemKey, quotaHoldId` | Retry 1 lần |
| `AiQuotaLedger` | `(userId,date)` | dùng chung BV-03 | Atomic |
| `QuestionReport` | `qrepId` | `qId, reason, status` | Như AnswerReport |

## 2. CRUD theo Use Case
| UC | Create | Read | Update | Delete |
|---|---|---|---|---|
| UC-01/02 sinh | `QuizSet/Deck` + `GenerationJob` | `RagChunk`, ledger | hold→commit | — |
| UC-04 start | `QuizAttempt DRAFT→IN_PROGRESS` | `QuizSet DRAFT` | — | — |
| UC-05 nộp | — | `QuizQuestion` đáp án | SUBMITTED→SCORED | — |
| UC-08 ôn | — | `FlashcardCard` | ease/interval | — |
| UC-10 báo sai | `QuestionReport` | `QuizQuestion` | — | — |

## 3. Migration
- `N-01`: `quiz_set`, `quiz_question` (check 4 options, 1 correct).
- `N-02`: `quiz_attempt` + `version` + index `(userId, createdAt)`.
- `N-03`: `flashcard_deck/card` + index `nextReviewAt`.
- `N-04`: `generation_job` + unique `(userId, idemKey)` + TTL 24h.
- Dùng chung ledger BV-03, không tạo bảng quota mới.

## 4. Tác động concurrent/quota
- Hold chung ledger: race QA+Quiz cùng lúc → atomic 1 thắng.
- Submit: `UPDATE attempts SET status WHERE attId AND version` chống đôi.
- Job treo `RUNNING > 10 phút → FAILED_FINAL + rollback (sweeper).

## 5. Hallucination/validate
- Check JSON schema + đáp án duy nhất + số liệu khớp chunk trước khi DRAFT.
- `NEEDS_REVIEW` không block làm bài nhưng gắn cảnh báo.

## 6. Dung lượng/hiệu năng
- Quiz 20 câu JSON < 64KB; chấm local < 200ms; lịch sử phân trang 20/trang.
- Giữ `AttemptHistory` 12 tháng hot, sau đó archive lạnh.

## 7. Backfill/rollback
- Backfill streak từ history cũ; rollback N-04→N-01, giữ attempt SCORED.

## 8. Đối soát ledger chung
- Nightly job đối chiếu hold/commit/refund với log gọi LLM phía BV-03.
- Lệch > 0 phải alert trước 08:00 `Asia/Ho_Chi_Minh` hôm sau.
- Báo cáo đối soát lưu 90 ngày phục vụ khiếu nại quota Free.
- Khiếu nại trừ oan: hoàn thủ công 1 lượt + ghi log `manualRefund`.
