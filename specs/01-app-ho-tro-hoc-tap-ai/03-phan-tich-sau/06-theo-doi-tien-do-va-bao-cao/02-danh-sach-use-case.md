# 02 — Danh Sách Use Case: Theo Dõi Tiến Độ Và Báo Cáo

> Module `06-theo-doi-tien-do-va-bao-cao` (P1, MVP cơ bản). Actor: `Student`, `System`.

| ID | Use case | Actor | Mô tả | Ưu tiên |
| :--- | :--- | :--- | :--- | :--- |
| UC-601 | Ghi sự kiện hỏi AI | `System` | Mỗi câu hỏi/trả lời AI sinh `LearningEvent AI_ASKED` | P1 |
| UC-602 | Ghi sự kiện hoàn thành Quiz | `System` | Nộp Quiz sinh `QUIZ_COMPLETED` kèm `score`, `duration` | P1 |
| UC-603 | Tính streak ngày liên tục | `System` | Chuỗi `activeDay` liên tục, reset khi ngắt quãng | P1 |
| UC-604 | Xem dashboard tiến độ | `Student` | Tổng thời gian, streak, điểm TB, biểu đồ 7 ngày | P1 |
| UC-605 | Nhận báo cáo tuần | `Student` | Email/in-app: tóm tắt tuần + so với tuần trước | P1 |
| UC-606 | Đặt và theo dõi mục tiêu tuần | `Student` | Mục tiêu `phút học/tuần`, % hoàn thành | P2 |
| UC-607 | Nhận huy hiệu động lực | `System` | Badge `streak-7`, `quiz-10`, `marathon-300p` | P2 |
| UC-608 | Cộng bù sự kiện đến muộn | `System` | Event trễ ≤ 7 ngày vẫn cộng vào đúng ngày gốc | P1 |
| UC-609 | Xuất tiến độ (cơ bản) | `Student` | Tải báo cáo tuần dạng PDF/ảnh chia sẻ | P2 |

## Phạm vi MVP cơ bản
- MVP: UC-601 → 605 + 608 (ghi nhận, streak, dashboard, báo cáo tuần, cộng bù).
- Sau MVP: UC-606 mục tiêu linh hoạt, UC-607 badge nâng cao, UC-609 xuất đẹp + chia sẻ mạng xã hội.

## Mô tả actor
- `Student`: xem dashboard, nhận báo cáo tuần, đặt mục tiêu (P2), nhận badge.
- `System`: consumer event bus, job chốt ngày/tuần, tính streak, phát badge — chạy nền không cần người.

## Điều kiện đầu vào / đầu ra chính
| Use case | Tiền điều kiện | Hậu điều kiện |
| :--- | :--- | :--- |
| UC-601/602 | Module 03/04 phát event đúng schema `v1` | `LearningEvent` + `DailyProgress` cập nhật ≤ 5s |
| UC-603 | Có `DailyProgress` ngày hôm qua và hôm nay | `streak` tăng hoặc reset đúng quy tắc |
| UC-604 | User đăng nhập | Dashboard 7 ngày render p95 < 1s |
| UC-605 | Job chốt 00:00 thứ Hai chạy xong | Báo cáo tuần gửi trước 08:00 |
| UC-608 | Event trễ có `occurredAt` gốc | Cộng đúng ngày gốc (≤ 7 ngày) hoặc ngày hiện tại |

## Loại sự kiện MVP (event catalog)
| Loại event | Nguồn | Trường chính |
| :--- | :--- | :--- |
| `AI_ASKED` | Module 03 | `eventId, userId, occurredAt, durationSec` |
| `QUIZ_COMPLETED` | Module 04 | `+ score, maxScore` |
| `STUDY_SESSION` | Client/app | `+ source (doc/video/note)` |

## Ánh xạ use case tới flowchart (tệp 01)
| Use case | Bước L1 | Bước L2 |
| :--- | :--- | :--- |
| UC-601/602 | C1 (ingest) | B1 ➔ B3 |
| UC-603 | C2 (cập nhật ngày) | B6 ➔ B7 |
| UC-604 | Đọc DailyProgress | Dashboard API |
| UC-605 | C3 ➔ C4/C5 | B8 ➔ B9/B10 |
| UC-608 | Bù trễ | B3 (ngày gốc) ➔ tái bản |

## Tần suất sử dụng dự kiến
- UC-601/602 chạy theo mỗi hành động học (cao điểm tối) nên ingest phải async và chịu 1000 event/phút.
- UC-604 mở vài lần/ngày/user nên cache dashboard 5 phút là đủ, giảm tải DB.
