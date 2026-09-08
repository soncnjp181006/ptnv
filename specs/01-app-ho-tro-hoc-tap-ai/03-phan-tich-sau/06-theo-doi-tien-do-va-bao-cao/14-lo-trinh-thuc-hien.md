# 14 — Lộ Trình Thực Hiện: Theo Dõi Tiến Độ Và Báo Cáo

> 2 sprint × 2 tuần, song song module 05. Phụ thuộc schema event module 03/04.

| Sprint | Mục tiêu | Đầu ra |
| :--- | :--- | :--- |
| S1: Ingest + Daily/Streak | Consumer event bus, `DailyProgress`, streak, API dashboard 7 ngày | Event v1, dashboard API + UI cơ bản |
| S2: Báo cáo tuần + Badge | Job chốt T2 00:00, gửi 08:00, 3 badge, bù trễ ≤ 7 ngày | Email/in-app report, REVISED flow |

## Cột mốc
- M1: giả lập 1000 event/phút, không trùng số, dashboard < 1s.
- M2: chạy chốt tuần thử (đồng hồ giả), streak/bù trễ đúng 100% case.
- Phát hành: bật cho 100% user ngay (rủi ro thấp), theo dõi tỉ lệ gửi báo cáo 2 tuần.

## Công việc chi tiết từng sprint
- S1-W1: chốt schema event `v1` với module 03/04; dựng consumer + `LearningEvent` + idempotency.
- S1-W2: `DailyProgress` + streak + API dashboard 7 ngày + UI dashboard cơ bản; test tải 1000 event/phút.
- S2-W1: job chốt ngày 00:15 + chốt tuần 00:00 T2 + template báo cáo tuần; test đồng hồ giả.
- S2-W2: 3 badge + bù trễ ≤ 7 ngày + tái bản REVISED; beta nội bộ 1 tuần rồi mở 100%.

## Phụ thuộc và đường găng
- Phụ thuộc duy nhất: schema event của module 03/04 — cần trước S1-W1, trễ thì consumer chờ.
- Không chặn module 05 (monetization); có thể giao 1 dev riêng làm song song.
- Rủi ro trượt: template email duyệt muộn ➔ mitigation dùng bản text trước, HTML sau.

## Định nghĩa sẵn sàng (ready) cho mỗi sprint
- Schema event `v1` đã ký giữa 3 module (03/04/06) hoặc có bản dự thảo khóa.
- API contract dashboard/báo cáo đã review với client (web/app).
- Seed 30 ngày dữ liệu mẫu + đồng hồ giả cho cron staging sẵn sàng.
- Template báo cáo tuần (text) đã có nội dung chốt với PO.

## Nghi thức demo cuối sprint
- S1: bắn 1000 event giả/phút ➔ dashboard cập nhật, streak tăng đúng, không trùng số.
- S2: chạy đồng hồ giả qua 00:00 thứ Hai ➔ báo cáo FINAL gửi đúng, bù trễ tái bản REVISED.
- Beta: 20 user nội bộ dùng 1 tuần ➔ thu thập tỉ lệ mở báo cáo + lỗi số liệu.
- Mỗi demo ghi biên bản + go/no-go mở toàn sàn.

## Phân công team gợi ý
| Vai trò | S1 | S2 |
| :--- | :--- | :--- |
| Backend | Consumer + LearningEvent + DailyProgress/streak | Job chốt + REVISED + badge |
| Frontend | Dashboard 7 ngày + empty-state | Trang báo cáo tuần + huy hiệu |
| QA | TC-601/602/603/609/610 | TC-604/605/606/607/608 + beta |
| PO | Chốt DEC-601/602 + schema v1 | Duyệt template báo cáo + beta |

## Rủi ro tiến độ và dự phòng
- Schema event trễ: consumer code trước với event giả lập (đã tính trong S1-W1).
- Template duyệt muộn: gửi bản text trước, HTML sau (không chặn beta).
- Dự phòng 20% effort S2 cho chỉnh badge + bù trễ theo feedback beta nội bộ.
