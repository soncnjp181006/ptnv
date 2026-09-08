# 09 — Ma Trận Truy Vết: Theo Dõi Tiến Độ Và Báo Cáo

> Truy vết `Yêu cầu ➔ Use case ➔ BR ➔ Thực thể ➔ Test`.

| Yêu cầu gốc | Use case | Quy tắc (BR) | Thực thể | Test dự kiến |
| :--- | :--- | :--- | :--- | :--- |
| Ghi hỏi AI | UC-601 | BR-P01, BR-P02, BR-X01 | `LearningEvent` | TC-601 ingest + idempotent |
| Ghi hoàn thành Quiz + điểm | UC-602 | BR-P01, BR-P02 | `LearningEvent`, `DailyProgress` | TC-602 điểm TB đúng |
| Streak liên tục | UC-603 | BR-P03, BR-T01 | `DailyProgress.streak` | TC-603 streak/reset |
| Dashboard + biểu đồ | UC-604 | BR-X03, BR-RV03 | `DailyProgress` | TC-604 render < 1s |
| Báo cáo tuần T2 | UC-605 | BR-T02 | `WeeklyReport` | TC-605 chốt + gửi 08:00 |
| Cộng bù trễ ≤ 7 ngày | UC-608 | BR-T03, BR-T04, BR-RV02 | `WeeklyReport REVISED` | TC-606 bù + tái bản |
| Event lỗi format | UC-601 | BR-X02 | `DeadLetter` | TC-607 dead-letter + alert |
| Job lỗi retry | UC-605 | BR-RV01 | `Scheduler` | TC-608 retry/PARTIAL |
| Mục tiêu tuần (P2) | UC-606 | — (sau MVP) | `Goal` | TC-609 (phase 2) |
| Huy hiệu (P2) | UC-607 | — (sau MVP) | `Badge` | TC-610 (phase 2) |

## Độ bao phủ
- 8 yêu cầu MVP ➔ 6 use case ➔ 12 BR ➔ 8 test MVP; UC-606/607/609 dời phase 2 nhưng giữ trace.

## Phân tích khoảng trống
- Mọi BR-P/BR-T/BR-X/BR-RV đều có test: BR-P01➔TC-601/602, BR-P02➔TC-601, BR-P03➔TC-603.
- BR-T01➔TC-609, BR-T02➔TC-605, BR-T03/T04➔TC-606; BR-X01/X02➔TC-601/607, BR-X03➔TC-604.
- BR-RV01➔TC-608, BR-RV02➔TC-606, BR-RV03➔TC-604; BR-D01➔TC-604, BR-D02➔checklist phát hành, BR-D03➔TC-602.
- UC-606/607/609 (P2) trace đến TC-609/610 phase 2 — không code MVP nhưng không mất dấu.

## Ma trận chiều ngược (test ➔ yêu cầu)
| Test | Phủ yêu cầu | Ghi chú |
| :--- | :--- | :--- |
| TC-601/602 | Ghi event AI/Quiz | Nhóm ingest lõi |
| TC-603/609 | Streak + cắt ngày | Nhóm thời gian |
| TC-604 | Dashboard | Nhóm hiển thị |
| TC-605/608 | Báo cáo tuần + retry | Nhóm tổng hợp |
| TC-606 | Bù trễ | Nhóm công bằng số liệu |
| TC-607/610 | Lỗi + tải | Nhóm vận hành |

## Quy tắc duy trì truy vết
- Thêm loại event mới ➔ thêm dòng trace + BR-P + TC ingest trong cùng thay đổi.
- Đổi ngưỡng activeDay ➔ cập nhật toàn bộ dòng TC-603/609 và tái chạy bộ case streak.

## Checklist rà soát truy vết định kỳ (mỗi sprint)
- [ ] Không use case nào thiếu BR; không BR nào thiếu test.
- [ ] UC phase 2 vẫn có dòng trace (đánh dấu P2) để không mất dấu.
- [ ] DEC-601/602 CONFIRMED đã phản ánh vào BR-P03/BR-T01/T02.
- [ ] Tệp 19 ghi nhận mọi thay đổi trace trong sprint.

## Ví dụ đọc truy vết 1 dòng (mẫu)
- Yêu cầu "streak liên tục" ➔ UC-603 ➔ BR-P03 + BR-T01 ➔ `DailyProgress.streak` ➔ TC-603/609.
- Ai đổi ngưỡng activeDay chỉ cần lần theo dòng này để biết phải sửa test nào, migration gì.
- Quy ước: mỗi dòng trace có exactly 1 use case để không rối nhiều-nhiều.
