# 08 — Ảnh Hưởng Liên Module: Theo Dõi Tiến Độ Và Báo Cáo

> Module 06 chỉ đọc/nghe, không chặn luồng học của module khác.

| Module liên quan | Chiều tác động | Nội dung | Cơ chế |
| :--- | :--- | :--- | :--- |
| 01 Người dùng | Nhận | `userId`, xóa dữ liệu khi user xóa tài khoản | Event `USER_DELETED` ➔ xóa/anonymize |
| 02 Tài liệu | Nghe | Mở/đọc tài liệu tính `timeSpent` (optional MVP) | Event `DOC_STUDIED` |
| 03 Gia sư AI | Nghe | Mỗi Q&A ➔ `AI_ASKED` + thời lượng | Event bus async |
| 04 Quiz | Nghe | Nộp bài ➔ `QUIZ_COMPLETED(score, duration)` | Event bus async |
| 05 Gói/quota | Đọc | Hiển thị gợi ý nâng cấp khi hết quota; sự kiện Paid cho báo cáo | Snapshot cache 5 phút |
| Thông báo | 06 ➔ Notify | Báo cáo tuần, streak milestone, huy hiệu | `notifyQueue` 08:00 |

## Hợp đồng event (MVP)
- `ai.asked {eventId, userId, occurredAt, durationSec}`.
- `quiz.completed {eventId, userId, occurredAt, score, maxScore, durationSec}`.
- `study.session {eventId, userId, occurredAt, durationSec, source}`.
- Quy tắc: producer gửi async, retry 5 lần; consumer idempotent theo `eventId`.

## Rủi ro liên module
- Producer đổi schema ➔ consumer vỡ: mitigation version event `v1`, validate + dead-letter.
- Event dồn giờ cao điểm ➔ trễ dashboard: mitigation consumer scale ngang + cache dashboard.

## Trình tự phối hợp điển hình (hỏi AI ➔ lên streak)
1. User hỏi AI (module 03) ➔ trả lời xong, module 03 bắn event `ai.asked` async, không chờ module 06.
2. Module 06 consumer nhận event ➔ ghi `LearningEvent` (idempotent) ➔ cộng `DailyProgress` hôm nay.
3. 00:15 job chốt ngày ➔ tính `activeDay` + cập nhật `streak` ➔ cache dashboard mới.
4. 00:00 thứ Hai job chốt tuần ➔ sinh `WeeklyReport` ➔ đẩy Notify gửi 08:00.
5. Module 05 chỉ được đọc (hiển thị gói trong báo cáo), không bao giờ bị module 06 ghi.

## Hợp đồng API cho client
- `GET /progress/me?days=7` — tổng thời gian, streak, điểm TB, mảng theo ngày cho chart.
- `GET /progress/weekly?week=2026-W36` — báo cáo tuần + version + status.
- `GET /badges/me` — danh sách huy hiệu đã đạt (MVP 3 mã).
- Quy tắc: API đọc cache tổng hợp; cấm client tự tính streak ở frontend (nguồn sự thật ở server).

## Nguyên tắc chống vòng lặp phụ thuộc
- Module 06 không gọi đồng bộ vào module 03/04/05 trong luồng ingest; chỉ đọc snapshot khi render.
- Producer (03/04) không cần biết consumer có sống không — hàng đợi đệm giữa, mất consumer thì event chờ.

## Ma trận trách nhiệm (RACI rút gọn)
| Công việc | Module 06 | Module 03/04 | Notify | Module 05 |
| :--- | :--- | :--- | :--- | :--- |
| Phát event học | — | R (phát) | — | — |
| Tính streak/báo cáo | R/A | — | — | — |
| Gửi báo cáo tuần | A | — | R (gửi) | — |
| Hiển thị gợi ý nâng cấp | C (cung cấp số liệu) | — | — | A |

## Thứ tự khởi động hệ thống (boot order)
1. Module 01 (user) và hàng đợi event lên trước.
2. Module 03/04 (producer) lên, event được đệm dù consumer chưa xong.
3. Module 06 consumer + job chốt lên sau; replay event tồn đọng khi khởi động.
