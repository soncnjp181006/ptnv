# 16 — Kế Hoạch Kiểm Thử: Theo Dõi Tiến Độ Và Báo Cáo

> Trọng tâm: đúng số, đúng giờ, không nhân đôi.

| ID | Kịch bản | Bước | Kết quả mong đợi |
| :--- | :--- | :--- | :--- |
| TC-601 | Ingest + idempotent | Gửi 1 event, gửi lại cùng eventId | 1 LearningEvent, lần 2 bỏ qua |
| TC-602 | Điểm TB | Nộp Quiz 6/10 rồi 8/10 | avgScore = 70%, quizCount = 2 |
| TC-603 | Streak chuẩn | Học T2–T6, nghỉ T7, học CN | T6 streak 5, CN reset về 1 |
| TC-604 | Dashboard | Seed 7 ngày, mở dashboard | Render < 1s, chart khớp số |
| TC-605 | Báo cáo tuần | Giả đồng hồ 00:00 T2 | FINAL trước 08:00, % so sánh đúng |
| TC-606 | Bù trễ | Gửi event trễ 2 ngày / 10 ngày | Cộng ngày gốc + REVISED / cộng ngày hiện tại |
| TC-607 | Event lỗi | Gửi thiếu userId 3 lần | Dead-letter + alert, app học vẫn mượt |
| TC-608 | Job lỗi | Ép job chốt fail | Retry 3 lần rồi PARTIAL + alert |
| TC-609 | Quanh 00:00 | Học 23:55–00:05 | Tách đúng 2 ngày theo tz |
| TC-610 | Tải | 1000 event/phút dồn | Không mất, không trùng, consumer kịp |

## Phi chức năng
- Dashboard p95 < 1s với 30 ngày dữ liệu; báo cáo tuần đúng giờ ≥ 99% trong 4 tuần thử.

## Môi trường và dữ liệu kiểm thử
- Môi trường: `staging` + đồng hồ giả để test chốt ngày/tuần, streak dài, bù trễ mà không chờ thật.
- Dữ liệu: user mẫu có 30 ngày học đủ pattern (liên tục, ngắt, tuần trắng, event trễ).
- Không cần tiền thật; test tải dùng producer giả bắn 1000 event/phút.

## Tiêu chí vào/ra (entry/exit)
- Vào: schema event `v1` đóng băng; seed 30 ngày xong; cron staging chạy.
- Ra: 100% TC MVP xanh; bộ case streak 20/20 đúng; báo cáo thử 2 tuần liền đúng giờ.
- Bug sai số (nhân đôi, mất event, streak sai) luôn là P0, chặn phát hành.

## Test case bổ sung
| ID | Kịch bản | Kết quả mong đợi |
| :--- | :--- | :--- |
| TC-611 | Tuần trắng (không học) | Nhận bản động viên, không nhận báo cáo số 0 khô khan |
| TC-612 | 2 event cùng giây | Cả 2 được ghi, mỗi `eventId` riêng, số cộng đúng |
| TC-613 | Đổi thang điểm Quiz giữa chừng | Điểm cũ giữ %, điểm mới quy % rồi mới trung bình |

## Quản lý defect
- Defect số liệu gắn nhãn `PROGRESS-DATA`, SLA sửa P0 trong 48h.
- Mọi defect P0 phải có test hồi quy + case bổ sung vào bộ streak chuẩn.
- Thống kê defect theo tuần beta; > 5 P0/tuần ➔ lùi mở toàn sàn, sửa gốc.

## Lịch chạy kiểm thử
| Giai đoạn | Suite chạy | Tần suất |
| :--- | :--- | :--- |
| Dev đẩy code | TC-601/602/603 (ingest + streak) | Mỗi commit |
| Cuối sprint | Toàn bộ TC-601 ➔ 613 | 1 lần + hồi quy |
| Trước beta | Đồng hồ giả qua 2 mốc 00:00 + 1 mốc thứ Hai | 1 buổi diễn tập |
| Trong beta | Đối chiếu event thô vs DailyProgress mỗi ngày | Hằng ngày 09:00 |
