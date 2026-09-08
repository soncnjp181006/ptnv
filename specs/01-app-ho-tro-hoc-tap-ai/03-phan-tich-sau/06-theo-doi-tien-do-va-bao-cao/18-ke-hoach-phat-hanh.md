# 18 — Kế Hoạch Phát Hành: Theo Dõi Tiến Độ Và Báo Cáo

> Rủi ro thấp: phát hành thẳng 100%, giám sát số liệu 2 tuần.

## 18.1 Chiến lược
- Bật ingest + dashboard cho 100% user ngay (không chặn học nếu lỗi: fail-open).
- Báo cáo tuần gửi thử nội bộ 1 tuần trước khi gửi toàn sàn.
- Feature flag `progressEnabled`, `weeklyReportEnabled` để tắt riêng từng phần.

## 18.2 Checklist Go-Live
- [ ] Consumer event bus v1 chạy, dead-letter + alert bật.
- [ ] Job chốt ngày 00:15 + chốt tuần 00:00 T2 + gửi 08:00 đã lên lịch.
- [ ] Dashboard cache + nhãn `dang cap nhat` khi job chậm.
- [ ] Backfill script cho dữ liệu cũ (nếu có) + dry-run tái tính.
- [ ] Email/in-app template báo cáo tuần đã duyệt nội dung.

## 18.3 Rollback
- Tắt `weeklyReportEnabled` nếu gửi sai giờ/sai số; dashboard giữ cache cũ.
- Tái tính sai ➔ restore từ `LearningEvent` thô (nguồn sự thật), phát hành bản REVISED.

## 18.4 Giám sát sau phát hành (2 tuần đầu)
| Chỉ số | Tần suất xem | Ngưỡng báo động |
| :--- | :--- | :--- |
| Event trễ > 5s | Mỗi giờ | > 5% ➔ xem consumer |
| Job chốt chưa chạy sau 00:30 | Mỗi ngày | 1 lần ➔ paging |
| Báo cáo gửi sau 08:00 | Mỗi thứ Hai | 1 lần ➔ cảnh báo |
| Dead-letter tồn đọng | Mỗi ngày | > 100 ➔ báo producer |
| Tỉ lệ mở báo cáo tuần | Mỗi tuần | < 15% ➔ xem lại tiêu đề/nội dung |

## 18.5 Truyền thông phát hành
- Thông báo tính năng: dashboard ở tab mới + email giới thiệu báo cáo tuần (gửi 1 lần).
- Giải thích streak: tooltip ngưỡng activeDay ngay trên dashboard để user hiểu vì sao mất/giữ lửa.
- Kênh góp ý số liệu sai (gắn nhãn `PROGRESS-DATA`), SLA xác minh 2 ngày làm việc.

## 18.6 Khôi phục dữ liệu (khi chốt sai hàng loạt)
1. Tắt job chốt/gửi (`weeklyReportEnabled = false`) để chặn lan rộng.
2. Xác định phạm vi tuần/user bị sai từ log job + `RecalcLog`.
3. Tái tính từ `LearningEvent` thô (nguồn sự thật), dry-run trước khi ghi.
4. Phát hành bản REVISED + thông báo đính chính gọn trong báo cáo kế tiếp.

## 18.7 Lịch beta và mở rộng
| Giai đoạn | Phạm vi | Tiêu chí qua |
| :--- | :--- | :--- |
| Beta nội bộ 1 tuần | 20 user | 0 bug P0 số liệu |
| Mở 50% 1 tuần | 50% user | Gửi đúng giờ ≥ 99% |
| Mở 100% | Toàn sàn | Tỉ lệ mở ≥ 20%, defect P0 = 0 |

## 18.8 Sau phát hành 30 ngày (đóng vòng beta)
- [ ] Tỉ lệ gửi đúng giờ 4 tuần ≥ 99%; tỉ lệ mở báo cáo ≥ 20%.
- [ ] Ticket mất streak oan < 5/tuần; mọi ca đều có kết luận tra cứu.
- [ ] Chốt backlog phase 2 (mục tiêu, leaderboard, PDF) theo số liệu thực tế.
