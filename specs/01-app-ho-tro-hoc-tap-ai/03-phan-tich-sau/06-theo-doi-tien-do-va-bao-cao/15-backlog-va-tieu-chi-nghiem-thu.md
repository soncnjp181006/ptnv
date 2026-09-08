# 15 — Backlog Và Tiêu Chí Nghiệm Thu: Theo Dõi Tiến Độ Và Báo Cáo

> MVP cơ bản: ST-601 → 606 + 608. ST-607/609 là phase 2.

| ID | Story | Acceptance Criteria |
| :--- | :--- | :--- |
| ST-601 | Ingest hỏi AI | 1 Q&A ➔ 1 event AI_ASKED trong 5s; gửi đôi cùng eventId ➔ 1 bản ghi |
| ST-602 | Ingest Quiz + điểm TB | Nộp Quiz 8/10 ➔ DailyProgress quizCount+1, avgScore đúng % |
| ST-603 | Streak | Học 5 ngày liên tiếp ➔ streak 5; ngắt 1 ngày ➔ reset về 1 |
| ST-604 | Dashboard 7 ngày | Mở < 1s, đủ thời gian/streak/điểm TB + chart; user mới thấy empty-state |
| ST-605 | Báo cáo tuần | Chốt 00:00 T2, nhận trước 08:00, có so sánh % tuần trước |
| ST-606 | Bù trễ | Event trễ 2 ngày ➔ cộng ngày gốc + tái bản REVISED; trễ 10 ngày ➔ ngày hiện tại |
| ST-607 | Badge cơ bản (3 cái) | Đạt streak-7/quiz-10/300-phút ➔ nhận badge + thông báo |
| ST-608 | PARTIAL + cache | Job lỗi ➔ PARTIAL + alert; dashboard chậm ➔ cache + nhãn |

## Định nghĩa hoàn thành (DoD)
- Mọi story MVP có test tự động; streak đúng 100% bộ case chuẩn (liên tục/ngắt/bù/quanh 00:00).

## Tiêu chí phi chức năng chung
- Hiệu năng: ingest p95 < 5s từ `occurredAt`; dashboard p95 < 1s; job chốt tuần xong trước 01:00.
- Quan sát được: mỗi event log có `eventId/userId`; dashboard đếm event/ngày để phát hiện mất số.
- Khả năng chạy lại: job chốt idempotent, chạy lại 10 lần vẫn 1 báo cáo FINAL (không nhân bản).
- Riêng tư: API progress yêu cầu đúng chủ tài khoản; Support xem cần audit.

## Thứ tự triển khai khuyến nghị
1. ST-601 ➔ ST-602 (ingest 2 nguồn chính) — có số liệu trước.
2. ST-603 ➔ ST-604 (streak + dashboard) — user thấy giá trị ngay.
3. ST-605 ➔ ST-606 ➔ ST-608 (báo cáo + bù + PARTIAL) — hoàn thiện vòng tuần.
4. ST-607 badge — điểm nhấn động lực cuối cùng trước phát hành.

## Story bổ sung (vận hành + niềm tin)
| ID | Story | Acceptance Criteria |
| :--- | :--- | :--- |
| ST-609 | Tra cứu streak cho Support | Nhập userId + ngày ➔ thấy chi tiết activeDay từng ngày |
| ST-610 | Cộng bù tay có audit | Support cộng ngày thiếu ➔ ghi audit + tái tính streak |
| ST-611 | Tắt/mở báo cáo tuần | User tắt ➔ dừng gửi nhưng vẫn tính số liệu |

## Ước lượng tương đối (story point, 1sp ≈ 0.5 ngày)
- ST-601/602/607/608: 2sp mỗi story (ingest, badge, PARTIAL).
- ST-603/604/606: 3sp mỗi story (streak, dashboard, bù trễ).
- ST-605 (chốt + gửi + template): 5sp (khó nhất, nhiều giờ giấc).
- Tổng MVP ≈ 25sp ≈ 2 sprint cho team 2 dev + 1 QA.

## Ghi chú ước lượng
- 1sp ≈ 0.5 ngày/dev; số trên chưa gồm họp và beta — cộng thêm 20% đệm.
- Story 5sp (ST-605) nên tách đôi nếu 1 dev ôm quá 1 tuần.

## Quy tắc tách story khi tràn sprint
- Template HTML báo cáo được dời (gửi text trước) mà không chặn beta.
- Badge (ST-607) được dời 1 sprint mà không ảnh hưởng số liệu lõi.
- Không được dời: ingest, streak, chốt tuần — dời là mất toàn bộ giá trị P1.
