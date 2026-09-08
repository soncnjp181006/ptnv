# 13 — Khả Thi Và Rủi Ro: Theo Dõi Tiến Độ Và Báo Cáo

> Module rủi ro thấp, chủ yếu là đúng số liệu và đúng giờ.

| ID | Rủi ro | Xác suất | Tác động | Giảm thiểu |
| :--- | :--- | :--- | :--- | :--- |
| R-601 | Streak sai do lệch múi giờ | T.Bình | Cao | Chuẩn UTC + tz, test quanh 00:00 |
| R-602 | Event trùng gây phình số | T.Bình | T.Bình | Idempotent `eventId`, test gửi đôi |
| R-603 | Job chốt tuần lỗi/mất | Thấp | Cao | Retry 3 lần + PARTIAL + alert |
| R-604 | Producer đổi schema | T.Bình | T.Bình | Version `v1`, validate + dead-letter |
| R-605 | Event dồn giờ cao điểm | T.Bình | T.Bình | Consumer scale ngang + cache dashboard |
| R-606 | Điểm TB sai thang điểm | Thấp | T.Bình | Chuẩn `score/maxScore`, quy về % |
| R-607 | Spam thông báo tuần | Thấp | Thấp | 1 báo cáo/tuần, tuần trắng chỉ 1 tin động viên |

## Khả thi
- Kỹ thuật: KHẢ THI CAO — CRUD + cron + chart cơ bản, 1–2 sprint.
- Dữ liệu: cần module 03/04 cam kết schema event trước S1.
- Kết luận: làm song song với module 05, không chặn đường găng monetization.

## Kế hoạch ứng phó chi tiết (top 3 rủi ro)
- R-601 lệch múi giờ: mọi test thời gian dùng đồng hồ giả + case biên 23:59/00:00/00:01; hiển thị ngày luôn kèm tz.
- R-603 job mất: ngoài retry, đặt monitor "job chưa chạy sau 00:30 ➔ paging"; chạy bù thủ công bằng 1 lệnh có sẵn.
- R-604 đổi schema: consumer validate version, event `v2` lạ ➔ dead-letter + báo producer trong ngày; giữ tương thích `v1` tối thiểu 6 tháng.

## Ma trận phụ thuộc quyết định
| Quyết định chưa chốt | Rủi ro bị chặn | Phương án tạm thời |
| :--- | :--- | :--- |
| DEC-601 ngưỡng activeDay | Streak tính sai phải migration | Code ngưỡng dạng cấu hình, chốt trước khi bật streak |
| DEC-602 định nghĩa tuần | Báo cáo chốt sai ngày | Mặc định T2–CN, đổi bằng cấu hình cron |
| Schema event v1 | Không ingest được | Dựng consumer với schema dự thảo, khóa trước S1 |

## Rủi ro dữ liệu và vận hành bổ sung
| ID | Rủi ro | Giảm thiểu |
| :--- | :--- | :--- |
| R-608 | Backfill dữ liệu cũ sai ngày | Dry-run trên staging + đối chiếu tổng trước khi ghi production |
| R-609 | User khiếu nại mất streak oan | Tra cứu `DailyProgress` theo ngày + `RecalcLog`; cộng bù tay có audit |
| R-610 | Báo cáo tuần gửi trùng 2 lần | Job chốt idempotent theo `(userId, weekStart)` + dedupe hàng Notify |
| R-611 | Dashboard hiện số cũ sau bù trễ | Xóa cache dashboard khi phát hành REVISED |

## Dự phòng ngân sách và thời gian
- Dự phòng 20% effort sprint 2 cho chỉnh template báo cáo + badge theo feedback beta.
- Nếu schema event trễ > 1 tuần: consumer làm trước với event giả lập, cắm producer thật sau.
- Beta nội bộ 1 tuần bắt buộc trước khi mở báo cáo tuần toàn sàn.

## Tín hiệu cảnh báo sớm
- Dead-letter tăng đột biến sau deploy module 03/04 ➔ schema đổi không báo, kiểm tra ngay.
- Tỉ lệ event trễ > 5s tăng giờ tối ➔ consumer đuối, scale thêm partition.
- Ticket "mất streak" ≥ 3/tuần ➔ ngưỡng activeDay hoặc job chốt có vấn đề.
