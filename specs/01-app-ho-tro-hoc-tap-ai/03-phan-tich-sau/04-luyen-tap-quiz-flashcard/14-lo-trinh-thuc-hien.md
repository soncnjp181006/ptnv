# 14 — Lộ Trình Thực Hiện: Quiz & Flashcard

> 3 sprint × 2 tuần, song song được với BV-03 sau khi ledger chung xong.

## 1. Sprint 1 — Sinh đề + quota chung (tuần 1–2)
- `QuizSet/QuizQuestion/GenerationJob`, API sinh + validate JSON 4 options.
- Tái dùng ledger BV-03 (hold/commit/rollback), job bất đồng bộ + progress.
- Exit: sinh 10 câu MEDIUM E2E, hết quota 429 đúng.

## 2. Sprint 2 — Làm bài + chấm + giải thích (tuần 3–4)
- `QuizAttempt` DRAFT→IN_PROGRESS→SUBMITTED→SCORED, chấm local, version chống đôi.
- Guard giải thích sau SCORED, làm lại/ôn sai, history + streak.
- Flashcard deck + ôn SM-2 cơ bản.
- Exit: nộp đôi 1 SCORED; xem sớm 403; demo làm lại.

## 3. Sprint 3 — Retry hardening + báo sai + release (tuần 5–6)
- Retry partial/timeout, local-success cached, sweeper job treo.
- `QuestionReport`, admin duyệt, NEEDS_REVIEW, xáo trộn options.
- Reminder SM-2, cache đề trùng, dashboard điểm/quota.
- Exit: JSON ≥ 98%, đáp án đúng ≥ 97%/300 câu, UAT không P0.

## 4. Cột mốc
| Mốc | Thời điểm | Điều kiện |
|---|---|---|
| M1 Sinh được đề | Cuối sprint 1 | 20 lần sinh mẫu pass |
| M2 Làm bài trọn vòng | Cuối sprint 2 | Nộp→điểm→giải thích |
| M3 Sẵn sàng release | Cuối sprint 3 | Gate file 11 PASS |

## 5. Phụ thuộc
- Ledger + RAG từ BV-03; `Document` ingest xong; LLM JSON mode bật.
- Quyết định DEC-01/02/10 chốt trước sprint 1.

## 6. Rủi ro lộ trình
- JSON lỗi nhiều → giảm 20→10 câu mặc định để kịp M1.
- SM-2 trễ → giữ ôn cơ bản, reminder dời patch.

## 7. Nhân sự và vai trò
- 1 BE (sinh + job + quota chung), 1 BE (attempt + chấm + SM-2), 1 FE, 1 QA.
- AI owner: prompt sinh JSON + khớp RAG; DBA review N-02/N-04.
- Daily 15 phút sprint 1 để xử lý nghẽn JSON/partial sớm.

## 8. Tiêu chí đóng sprint
- Đóng sprint khi 100% exit đạt + demo staging chạy được.
- Task dở ghi rõ lý do, chuyển sprint sau có review, không tự lăn.

## 9. Đồng bộ với BV-03
- Ledger chung khóa trước: BV-04 chờ contract hold/commit của BV-03.
- Hai module demo chung M2: hỏi QA từ câu sai Quiz và ngược lại.
- Demo M2 mời 3 teacher dùng thật, thu feedback độ khó đề.
- Feedback teacher tổng hợp vào D-02 trước sprint 3.
