# 05 — Bộ Quy Tắc Nghiệp Vụ: Luyện Tập Quiz & Flashcard

> Format: `BR-XXX / WHEN / MUST / OTHERWISE / AFFECTS / SOURCE / STATUS`.

## BR-001 — Quota sinh trước gọi LLM
- WHEN: `Student/Teacher` gọi sinh `QuizSet` hoặc `FlashcardDeck` bằng AI.
- MUST: Hold 1 lượt `AiQuotaLedger` (dùng chung với BV-03, ngày `Asia/Ho_Chi_Minh`) trước khi gọi `LlmProvider`.
- OTHERWISE: Hết quota → `429 QUOTA_EXCEEDED` + paywall, không gọi LLM.
- AFFECTS: UC-01, UC-02, UC-03, `GenerationJob`.
- SOURCE: Quy ước quota Free 20/ngày dùng chung.
- STATUS: APPROVED.

## BR-002 — Làm bài miễn phí quota
- WHEN: `Student` start attempt, nộp bài, xem giải thích, làm lại, ôn flashcard.
- MUST: Không trừ quota; chỉ sinh mới tốn quota.
- OTHERWISE: Cấm mọi code path trừ quota trong chấm/xem giải thích.
- AFFECTS: UC-04→UC-09, `QuizAttempt`.
- SOURCE: AGENT_INFERENCE (khuyến khích luyện tập).
- STATUS: APPROVED.

## BR-003 — Idempotency sinh và nộp
- WHEN: Retry `POST /quiz/generate` hoặc `POST /attempts/submit` do timeout.
- MUST: Bắt buộc `Idempotency-Key`; dedupe 24h; cùng key không trừ quota / không tạo SCORED đôi.
- OTHERWISE: Thiếu key → 400; key khác → tính lượt mới.
- AFFECTS: UC-11, UC-12, SC-UC11-01, SC-UC05-03.
- SOURCE: AGENT_INFERENCE từ yêu cầu idempotency.
- STATUS: APPROVED.

## BR-004 — Validate JSON đề sinh
- WHEN: `LlmProvider` trả JSON Quiz/Flashcard.
- MUST: Mỗi câu có đúng 4 options, đúng 1 `correctOption`, có `explanation` và `SourceRef`.
- OTHERWISE: Fail validate → regenerate đúng 1 lần; vẫn fail → `DISCARDED` + rollback quota.
- AFFECTS: UC-01, UC-02, chống hallucination.
- SOURCE: Yêu cầu sinh Quiz + giải thích + trích dẫn.
- STATUS: APPROVED.

## BR-005 — Đáp án khớp ngữ liệu RAG
- WHEN: Câu sinh xong và có chunk nguồn.
- MUST: `correctOption` phải suy ra được từ `RagChunk`; số liệu phải khớp chuỗi gốc.
- OTHERWISE: Gắn `NEEDS_REVIEW`, cho làm bài kèm cảnh báo hoặc teacher duyệt trước.
- AFFECTS: UC-01, SC-UC01-04.
- SOURCE: Yêu cầu hallucination + báo câu sai.
- STATUS: PROPOSED.

## BR-006 — Retry sinh dở dang
- WHEN: `GenerationJob` timeout 45s / 5xx / partial JSON.
- MUST: Retry đúng 1 lần cùng key (tái dùng hold); local-success thì trả cached.
- OTHERWISE: Cấm retry lỗi 4xx cấu hình; fail cuối rollback quota + fallback.
- AFFECTS: UC-11, `GenerationJob`.
- SOURCE: Yêu cầu retry sau partial completion + timeout.
- STATUS: APPROVED.

## BR-007 — Hoàn quota khi sinh lỗi hệ thống
- WHEN: `GenerationJob → FAILED_FINAL` không do user.
- MUST: Rollback toàn bộ hold (1 lượt) + ghi `quotaRefund`.
- OTHERWISE: Không hoàn khi `SUCCEEDED` dù user chê đề khó/dễ.
- AFFECTS: UC-03, SC-UC11-02.
- SOURCE: AGENT_INFERENCE (công bằng).
- STATUS: PROPOSED.

## BR-008 — Chống double-submit
- WHEN: 2 request nộp cùng `attemptId` (2 tab, double-click).
- MUST: Optimistic `version`; 1 thắng → `SCORED`, còn lại `409 ATTEMPT_ALREADY_SUBMITTED`.
- OTHERWISE: Cấm tạo 2 bản SCORED; cấm sửa đáp án sau SUBMITTED.
- AFFECTS: UC-05, UC-12.
- SOURCE: AGENT_INFERENCE từ concurrent request.
- STATUS: APPROVED.

## BR-009 — Giải thích sau chấm
- WHEN: `QuizAttempt` đạt `SCORED`.
- MUST: Hiện `explanation` + `SourceRef` từng câu; cấm hiện trước khi nộp.
- OTHERWISE: Request sớm → 403; log gian lận xem trước.
- AFFECTS: UC-06, `QuizQuestion`.
- SOURCE: Yêu cầu xem giải thích.
- STATUS: APPROVED.

## BR-010 — Giới hạn cấu hình và báo sai
- WHEN: Cấu hình sinh hoặc báo câu sai.
- MUST: Free tối đa 10 câu/lần, Paid/Teacher 20 câu/lần; `difficulty` thuộc EASY/MEDIUM/HARD; báo sai ≤ 5/ngày.
- OTHERWISE: Vượt → 400/429 + hướng dẫn đúng.
- AFFECTS: UC-01, UC-10.
- SOURCE: Yêu cầu cấu hình số câu/độ khó + báo câu sai.
- STATUS: APPROVED.
