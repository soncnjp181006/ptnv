# 15 — Backlog và Tiêu Chí Nghiệm Thu: Quiz & Flashcard

> Story + Given/When/Then; sinh tốn quota, luyện miễn phí.

## 1. Backlog
| Mã | Story | Ưu tiên | Điểm |
|---|---|---|---|
| US-01 | Là học viên, tôi sinh Quiz 10 câu MEDIUM từ tài liệu để luyện | Must | 8 |
| US-02 | Là học viên, tôi sinh deck flashcard để ôn hằng ngày | Must | 5 |
| US-03 | Là học viên, tôi làm bài và nộp để có điểm tức thì | Must | 5 |
| US-04 | Là học viên, tôi xem giải thích + nguồn sau chấm để hiểu sai | Must | 3 |
| US-05 | Là học viên, tôi làm lại/ôn câu sai mà không tốn quota | Should | 3 |
| US-06 | Là học viên, tôi ôn SM-2 và giữ streak | Should | 5 |
| US-07 | Là học viên, tôi báo câu sai để được sửa | Should | 2 |
| US-08 | Là hệ thống, tôi retry sinh dở mà không tính phí thêm | Must | 5 |

## 2. Nghiệm thu
### US-01
- Given còn quota, doc ingest xong; When sinh 10 MEDIUM; Then `DRAFT` đủ 10 câu, mỗi câu 4 options + 1 correct + explanation, trừ 1 quota.

### US-02
- Given còn quota; When sinh deck 20 thẻ; Then đủ trước/sau, trừ 1 quota; hết quota thì 429.

### US-03
- Given attempt IN_PROGRESS; When nộp đủ; Then SUBMITTED→SCORED < 1s, điểm đúng đáp án.
- Given double-click nộp; When 2 submit cùng version; Then 1 SCORED, 1 nhận 409.

### US-04
- Given SCORED; When mở giải thích; Then thấy explanation + SourceRef.
- Given IN_PROGRESS; When gọi giải thích; Then 403.

### US-05
- Given 1 SCORED 6/10; When ôn câu sai; Then attempt con 4 câu, nộp chấm bình thường, không trừ quota.

### US-06
- Given thẻ NEW; When đánh GOOD 3 lần; Then state REVIEW + `nextReviewAt` tương lai + streak +1.

### US-07
- Given SCORED; When báo sai lần 6/ngày; Then 429, 5 report trước giữ nguyên.

### US-08
- Given timeout 45s đã lưu nháp; When retry cùng key; Then trả cached DRAFT, 1 quota.
- Given partial 7/10; When retry vẫn thiếu; Then rollback quota + 503 + nút tạo lại.

## 3. DoD
- Pass TQ Must, JSON ≥ 98%, đáp án đúng ≥ 97%/300 mẫu, docs API xong.

## 4. Ràng buộc story
- US Must xong trước M2; Should trước M3; Could không chặn release.
- US sinh đề phải có AC kiểm tra ledger; US nộp phải có AC version.
- Story trên 8 điểm tách nhỏ trước khi đưa vào sprint.

## 5. Quy trình nghiệm thu
- QA demo trên staging, PO tick AC; fail → bug P1/P2 fix trong sprint.
- Biên bản nghiệm thu đính kèm release note 1.0.
