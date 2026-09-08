# 18 — Kế Hoạch Phát Hành: Quiz & Flashcard

> Flag, canary, rollback job sinh và attempt.

## 1. Phạm vi release 1.0
- Sinh Quiz/Flashcard, làm bài trọn vòng, giải thích, làm lại, SM-2, history, báo sai, retry.
- Loại trừ: PUBLISHED cho lớp hàng loạt, reminder push SM-2 (patch 1.1).

## 2. Triển khai
- Flag `quiz-v1` 10% → 50% → 100% / 5 ngày; kill-switch dừng sinh mới, giữ làm bài cũ.
- Worker sinh riêng hàng đợi, giới hạn 50 job đồng thời; sweeper job treo > 10 phút.
- Migration N-01→N-04 trước thấp điểm; cache đề trùng bật sau 50% canary.

## 3. Go/No-go
| Tiêu chí | Ngưỡng | Nguồn |
|---|---|---|
| JSON hợp lệ staging | ≥ 98% / 100 lần | TQ-01 |
| Đáp án đúng duyệt tay | ≥ 97% / 300 câu | TQ-11 |
| Double-SCORED | 0 ca | TQ-10 |
| Rollback sinh lỗi | 100% hoàn quota | TQ-09 |
| Chấm P95 | < 1s | k6 |

## 4. Rollback
- Tắt `quiz-v1`: job QUEUED/RUNNING cho hoàn hoặc hoàn quota; attempt IN_PROGRESS giữ làm tiếp.
- Rollback N-04→N-01, giữ SCORED và history; không xóa DRAFT của user.
- LLM lỗi hàng loạt: bật đề mẫu offline + banner, không chặn làm bài cũ.

## 5. Giám sát
- Alert: `genFailRate > 5%`, `quotaRefund` spike, `doubleSubmit409` spike, điểm trung bình rơi > 15%.
- Dashboard: sinh/ngày, pass JSON, điểm phân bố, deck due, report tồn.

## 6. Truyền thông
- Thông báo sinh tốn 1 lượt/ngày, luyện lại miễn phí; giờ reset `Asia/Ho_Chi_Minh`.
- Disclaimer đề AI cần kiểm chứng trước khi dùng thi thật.

## 7. Checklist ngày release
- [ ] Migration N-01→N-04 xong, worker sinh + sweeper chạy.
- [ ] Flag `quiz-v1` ở 10%, dashboard sinh/điểm/quota mở sẵn.
- [ ] Đề mẫu offline + banner dự phòng LLM đã test.
- [ ] Paywall quota chung QA+Quiz hiển thị đúng số lượt còn lại.
- [ ] On-call trực, kênh incident mở.

## 8. Sau 7 ngày canary
- Tổng kết JSON rate, đáp án đúng, double-submit, refund; quyết định 100%.
- P0/P1 tồn đọng đóng hết hoặc có workaround bằng văn bản.
- Lưu release note + Go/No-go vào `19-nhat-ky-thay-doi.md`.

## 9. Mở rộng 100%
- Không P0 mở; JSON ≥ 98%; refund/ngày < 2% tổng lần sinh.
- Patch 1.1 (reminder SM-2) chỉ release khi 1.0 ổn định 14 ngày.
