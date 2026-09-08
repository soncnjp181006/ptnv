# 05 — Bộ Quy Tắc Nghiệp Vụ: Theo Dõi Tiến Độ Và Báo Cáo

> Format `BR-XXX / WHEN / MUST / OTHERWISE / AFFECTS / SOURCE / STATUS`. Ngày học theo `Asia/Ho_Chi_Minh`.

## Nhóm ghi nhận (BR-P)
- BR-P01 | WHEN phát sinh học (hỏi AI, nộp Quiz, login học) | MUST sinh `LearningEvent` có `eventId` duy nhất trong 5s | OTHERWISE ngày đó thiếu số liệu | AFFECTS `LearningEvent` | SOURCE PROPOSED | STATUS PROPOSED
- BR-P02 | WHEN event trùng `eventId` | MUST ingest idempotent, chỉ ghi 1 lần | OTHERWISE số liệu phình gấp đôi | AFFECTS `LearningEvent` | SOURCE PROPOSED | STATUS PROPOSED
- BR-P03 | WHEN tính `activeDay` | MUST `timeSpent >= 5 phút OR >= 1 Quiz OR >= 3 câu AI` | OTHERWISE streak sai | AFFECTS `DailyProgress` | SOURCE PROPOSED | STATUS PROPOSED

## Nhóm thời gian (BR-T)
- BR-T01 | WHEN cắt ngày học | MUST ranh giới `00:00 Asia/Ho_Chi_Minh` | OTHERWISE lệch streak | AFFECTS `DailyProgress` | SOURCE FACT | STATUS PROPOSED
- BR-T02 | WHEN chốt báo cáo tuần | MUST job `00:00 thứ Hai`, gửi trước `08:00`, tuần T2–CN | OTHERWISE user nhận trễ | AFFECTS `WeeklyReport` | SOURCE PROPOSED | STATUS PROPOSED
- BR-T03 | WHEN event đến muộn | MUST cộng bù vào ngày gốc nếu trễ ≤ 7 ngày, tái bản báo cáo | OTHERWISE mất công user | AFFECTS `WeeklyReport` | SOURCE PROPOSED | STATUS PROPOSED
- BR-T04 | WHEN event trễ > 7 ngày | MUST ghi vào ngày hiện tại, không sửa quá khứ | OTHERWISE méo lịch sử | AFFECTS `DailyProgress` | SOURCE PROPOSED | STATUS PROPOSED

## Nhóm tích hợp (BR-X)
- BR-X01 | WHEN module AI/Quiz phát event | MUST gửi async qua event bus, không chặn luồng học (>300ms là lỗi) | OTHERWISE học giật lag | AFFECTS `EventBus` | SOURCE PROPOSED | STATUS PROPOSED
- BR-X02 | WHEN event sai format | MUST đẩy dead-letter + alert, không retry vô hạn (tối đa 5 lần) | OTHERWISE tắc hàng đợi | AFFECTS `DeadLetter` | SOURCE PROPOSED | STATUS PROPOSED
- BR-X03 | WHEN dashboard cần quota/gói | MUST đọc snapshot module 05, cho phép hiển thị khi 05 chậm (cache 5 phút) | OTHERWISE dashboard trắng | AFFECTS `Dashboard` | SOURCE PROPOSED | STATUS PROPOSED

## Nhóm khôi phục (BR-RV)
- BR-RV01 | WHEN job chốt ngày/tuần lỗi | MUST retry 3 lần cách 10 phút, rồi đánh dấu `PARTIAL` + alert | OTHERWISE mất báo cáo | AFFECTS `Scheduler` | SOURCE PROPOSED | STATUS PROPOSED
- BR-RV02 | WHEN tái tính sau bù trễ | MUST ghi `RecalcLog`, giữ bản báo cáo cũ, phát hành bản `REVISED` mới | OTHERWISE không truy vết được | AFFECTS `WeeklyReport` | SOURCE PROPOSED | STATUS PROPOSED
- BR-RV03 | WHEN dashboard job chậm | MUST hiển thị cache hôm qua + nhãn `dang cap nhat` thay vì lỗi trắng | AFFECTS `Dashboard` | SOURCE PROPOSED | STATUS PROPOSED

## Nhóm hiển thị và quyền riêng tư (BR-D)
- BR-D01 | WHEN dashboard thiếu dữ liệu | MUST hiển thị empty-state + gợi ý hành động, không để trắng | OTHERWISE user tưởng lỗi | AFFECTS `Dashboard` | SOURCE PROPOSED | STATUS PROPOSED
- BR-D02 | WHEN user xóa tài khoản | MUST xóa/anonymize `LearningEvent` + `DailyProgress` trong 30 ngày theo yêu cầu | OTHERWISE vi phạm quyền riêng tư | AFFECTS `LearningEvent` | SOURCE PROPOSED | STATUS PROPOSED
- BR-D03 | WHEN tính điểm TB | MUST chuẩn hóa về % (`score/maxScore*100`) trước khi trung bình | OTHERWISE sai khi thang điểm khác nhau | AFFECTS `DailyProgress` | SOURCE PROPOSED | STATUS PROPOSED

## Ví dụ minh họa
- Ví dụ BR-P03: ngày có 1 Quiz + 2 câu AI + 3 phút đọc ➔ activeDay = true (đủ điều kiện Quiz).
- Ví dụ BR-T03: event ngày 05/09 đến ngày 07/09 ➔ cộng vào 05/09, tái tính streak 05→07, tái bản báo cáo nếu tuần đã chốt.
- Ví dụ BR-D03: Quiz 8/10 (= 80%) và 15/20 (= 75%) ➔ avgScore = 77.5%; cấm trung bình điểm thô (8+15)/2.
- Ví dụ BR-X01: consumer xử lý > 300ms ➔ producer không chờ; event nằm queue, dashboard cập nhật chậm vài giây là chấp nhận được.

## Bảng tổng hợp số lượng quy tắc
| Nhóm | Số BR | Phủ use case |
| :--- | :--- | :--- |
| BR-P (ghi nhận) | 3 | UC-601, 602, 603 |
| BR-T (thời gian) | 4 | UC-603, 604, 605, 608 |
| BR-X (tích hợp) | 3 | UC-601, 602, 604 |
| BR-RV (khôi phục) | 3 | UC-604, 605, 608 |
| BR-D (hiển thị, riêng tư) | 3 | UC-602, 604 + xóa tài khoản |

## Quy tắc đặt mã và vòng đời BR
- Mã `BR-<nhóm><số>` tăng dần; không tái dùng mã đã xóa.
- `STATUS` giữ `PROPOSED` đến khi DEC-601/602 CONFIRMED mới chuyển tương ứng.
- Thêm loại event mới (ví dụ `VIDEO_WATCHED`) ➔ thêm BR-P + cập nhật trace ngay.
