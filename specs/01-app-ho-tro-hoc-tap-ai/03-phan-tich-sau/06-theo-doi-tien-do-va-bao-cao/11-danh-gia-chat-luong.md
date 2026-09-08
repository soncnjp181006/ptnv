# 11 — Đánh Giá Chất Lượng: Theo Dõi Tiến Độ Và Báo Cáo

> Thang 1–5. Ngưỡng đạt ≥ 3.5. Mức MVP cơ bản nên tiêu chí nới cho tính năng P2.

| Tiêu chí | Điểm | Nhận xét |
| :--- | :--- | :--- |
| Đầy đủ sự kiện | 4 | Đủ AI/Quiz/session; đọc tài liệu optional |
| Nhất quán thời gian | 4 | Cutoff 00:00 + tuần T2–CN rõ, có bù trễ |
| Khả kiểm | 4 | Ngưỡng activeDay, streak, PARTIAL đều test được |
| Khả thi (P1 scope) | 4.5 | Event bus + cron đơn giản, không AI thêm |
| Không chặn học | 4 | Ingest async, dashboard cache — đúng nguyên tắc |
| Rõ ràng | 3.5 | Cần chốt DEC-601/602 để số liệu thống nhất |

## Điểm yếu và hành động
- W1: Ngưỡng activeDay chưa chốt ➔ chốt DEC-601 trước khi code streak.
- W2: Điểm TB Quiz khác thang điểm ➔ chuẩn hóa `score/maxScore` ở producer (module 04).
- Điểm trung bình: `4.0/5` — ĐẠT ở mức MVP cơ bản.

## Kế hoạch cải thiện chất lượng
| Vấn đề | Hành động | Owner | Hạn |
| :--- | :--- | :--- | :--- |
| DEC-601/602 chưa chốt | Họp 30 phút chốt ngưỡng + định nghĩa tuần | PO | Trước sprint 1 |
| Schema event chưa đóng băng | Họp 3 module 03/04/06 chốt schema `v1` | Tech lead | Tuần 1 |
| Chưa có test quanh 00:00 | Thêm TC-609 vào suite bắt buộc | QA | Sprint 1 |
| Template báo cáo tuần chưa duyệt | PO duyệt nội dung + subject email | PO | Trước sprint 2 |

## Tiêu chí cổng chất lượng
- Không code streak nếu DEC-601/602 chưa CONFIRMED (tránh migration tái tính).
- Không phát hành nếu TC-601/603/605/606 chưa xanh (ingest, streak, báo cáo, bù trễ).
- Review chéo schema event với module 03/04 trước khi đóng phân tích.

## Chi tiết điểm theo nhóm use case
| Nhóm | Điểm | Lý do |
| :--- | :--- | :--- |
| Ingest event | 4.5 | Async + idempotent chuẩn, test trùng rõ |
| Streak + cắt ngày | 4.0 | Quy tắc rõ, còn chờ chốt ngưỡng |
| Dashboard | 4.0 | Cache + empty-state, cần test tải 30 ngày |
| Báo cáo tuần | 4.0 | Giờ giấc rõ, cần duyệt template |
| Bù trễ + PARTIAL | 4.0 | Ngưỡng 7 ngày hợp lý, có REVISED |

## Rủi ro chất lượng còn lại
- RQ-1: schema event đổi sau khi consumer xong ➔ mitigation đóng băng `v1` tuần 1 (tệp 14).
- RQ-2: template báo cáo xấu làm tỉ lệ mở thấp ➔ mitigation PO duyệt trước sprint 2 + A/B subject.
- RQ-3: test thiếu case vắt qua 00:00 ➔ mitigation TC-609 bắt buộc trong suite.

## Lịch sử chấm điểm
| Vòng | Điểm | Thay đổi chính |
| :--- | :--- | :--- |
| v0.5 | 3.4 | Mới có event + flowchart, thiếu bù trễ và PARTIAL |
| v0.9 | 3.8 | Đủ BR-T/BR-X/BR-RV + máy trạng thái Daily/Weekly |
| v1.0 | 4.0 | Đủ trace test + KPI + beta plan; còn 2 DEC chờ chốt |
