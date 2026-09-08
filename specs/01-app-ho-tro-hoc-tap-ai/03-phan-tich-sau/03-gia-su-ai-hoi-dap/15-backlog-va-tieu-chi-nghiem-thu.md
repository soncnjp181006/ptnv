# 15 — Backlog và Tiêu Chí Nghiệm Thu: Gia Sư AI Hỏi Đáp

> User story + acceptance criteria (Given/When/Then).

## 1. Backlog
| Mã | Story | Ưu tiên | Điểm |
|---|---|---|---|
| US-01 | Là học viên, tôi gửi câu hỏi và nhận đáp kèm trích dẫn để tin tưởng nguồn | Must | 8 |
| US-02 | Là học viên Free, tôi bị chặn lịch sự khi hết 20 câu/ngày và được mời nâng cấp | Must | 3 |
| US-03 | Là học viên, tôi retry khi timeout mà không mất thêm quota | Must | 5 |
| US-04 | Là học viên, tôi vote hữu ích và báo câu sai để được cải thiện | Should | 3 |
| US-05 | Là học viên, tôi xem lịch sử và quota còn lại theo giờ Việt Nam | Should | 3 |
| US-06 | Là admin, tôi duyệt báo cáo sai để gắn cờ chunk xấu | Should | 5 |
| US-07 | Là hệ thống, tôi chặn nội dung độc hại 2 chiều để an toàn giáo dục | Must | 5 |
| US-08 | Là hệ thống, tôi chống concurrent vượt quota khi user mở 2 tab | Must | 5 |

## 2. Tiêu chí nghiệm thu
### US-01
- Given còn quota và RAG đủ ngữ cảnh; When gửi "Định lý Pythagore là gì?"; Then trả đáp + ≥1 `SourceRef` trang hợp lệ trong 30s.

### US-02
- Given Free đã dùng 20/20; When gửi thêm; Then 429 `QUOTA_EXCEEDED` + paywall, không gọi LLM (check `LlmCallLog` trống).

### US-03
- Given LLM timeout lần đầu nhưng server chưa lưu; When retry cùng key; Then tối đa 1 gọi mới, chỉ trừ 1 quota, kết quả đầy đủ.
- Given timeout nhưng server đã lưu; When retry cùng key; Then trả cached, không gọi LLM mới.

### US-04
- Given 1 ASSISTANT message; When vote HELPFUL rồi vote lại; Then lần 2 trả 409 giữ nguyên.
- Given đã báo 5 lần hôm nay; When báo lần 6; Then 429.

### US-05
- Given truy cập 23:55 và 00:05 `Asia/Ho_Chi_Minh`; Then số liệu reset đúng ngày mới.

### US-06
- Given report PENDING; When admin CONFIRMED; Then chunk gắn cờ và event `report.confirmed` phát ra.

### US-07
- Given input độc hại; When gửi; Then 422, không trừ quota, có log kiểm duyệt.
- Given output độc hại; When LLM trả về; Then thay mẫu từ chối, không hiển thị gốc.

### US-08
- Given còn 1 quota và 2 tab gửi đồng thời; When race; Then đúng 1 thắng, 1 nhận 429, ledger không âm.

## 3. Definition of Done
- Code + unit/integration pass, citation coverage ≥ 95%, không double-charge, docs API cập nhật.

## 4. Ràng buộc story
- US Must phải xong trước M2; US Should trước M3; Could không chặn release.
- Mọi US quota-sensitive phải có AC kiểm tra ledger (không chỉ kiểm tra UI).
- Story trên 8 điểm phải tách nhỏ trước khi đưa vào sprint.

## 5. Quy trình nghiệm thu
- QA demo từng US trên staging, Product Owner tick AC trực tiếp.
- AC fail → bug P1/P2, fix trong sprint, không dời quá 1 sprint.
- Biên bản nghiệm thu lưu cùng release note 1.0.
