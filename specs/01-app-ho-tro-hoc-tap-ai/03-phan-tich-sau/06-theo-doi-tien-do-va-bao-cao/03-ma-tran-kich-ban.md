# 03 — Ma Trận Kịch Bản: Theo Dõi Tiến Độ Và Báo Cáo

> Mỗi use case: `Happy / Alternative / Exception`.

## UC-601/602 Ghi sự kiện học
- Happy: hỏi AI / nộp Quiz ➔ event hợp lệ ➔ `LearningEvent` + cập nhật `DailyProgress` trong 5s.
- Alternative (trùng): cùng `eventId` gửi 2 lần ➔ ghi 1 lần, lần 2 trả 200-bỏ qua.
- Exception (sai format): thiếu `userId/type` ➔ dead-letter, alert, không chặn luồng học.

## UC-603 Streak
- Happy: học 5 ngày liên tiếp ➔ `streak = 5`, hiển thị lửa + số ngày.
- Alternative: học cách ngày nhưng trong cùng logic `activeDay` ➔ streak tiếp tục nếu không có ngày trống.
- Exception: ngắt 1 ngày ➔ reset về 1 khi học lại; gửi động viên (không phạt).

## UC-604 Dashboard
- Happy: mở dashboard ➔ thấy thời gian, streak, điểm TB, chart 7 ngày < 1s.
- Alternative: user mới chưa có dữ liệu ➔ empty-state + gợi ý bài học đầu tiên.
- Exception: job tổng hợp chậm ➔ hiển thị cache hôm qua + nhãn `dang cap nhat`.

## UC-605 Báo cáo tuần
- Happy: 00:00 thứ Hai chốt tuần ➔ gửi báo cáo trước 08:00 (so sánh +/- % tuần trước).
- Alternative (tuần trắng): không học ngày nào ➔ gửi bản động viên + mục tiêu mini 15 phút/ngày.
- Exception: thiếu 1–2 ngày dữ liệu ➔ báo cáo gắn nhãn `PARTIAL`, không bịa số.

## UC-608 Cộng bù trễ
- Happy: event trễ 2 ngày (≤ 7) ➔ cộng vào đúng ngày gốc, tính lại streak/báo cáo liên quan.
- Alternative: trễ nhưng tuần đã chốt ➔ tái bản `WeeklyReport v2`, ghi chú `dieu chinh bo sung`.
- Exception: trễ > 7 ngày ➔ ghi nhận vào ngày hiện tại, không sửa quá khứ (chống méo lịch sử).

## UC-606/607 Mục tiêu và huy hiệu (P2, phân tích trước)
- Happy: user đặt mục tiêu 300 phút/tuần ➔ thanh tiến trình cập nhật mỗi ngày, đạt 100% nhận badge.
- Alternative: không đạt mục tiêu ➔ báo cáo tuần gợi ý mục tiêu nhỏ hơn (giảm 30%).
- Exception: user tắt thông báo động lực ➔ vẫn tính số liệu, chỉ dừng gửi tin.

## Bảng tổng hợp bao phủ kịch bản
| Use case | Happy | Alternative | Exception |
| :--- | :--- | :--- | :--- |
| UC-601/602 | Có | Có (trùng event) | Có (sai format) |
| UC-603 | Có | Có (cách ngày) | Có (reset + động viên) |
| UC-604 | Có | Có (empty-state) | Có (cache + nhãn) |
| UC-605 | Có | Có (tuần trắng) | Có (PARTIAL) |
| UC-608 | Có | Có (tái bản v2) | Có (> 7 ngày) |

## Quy tắc ưu tiên
- Không bao giờ chặn luồng học vì lỗi đo lường: ingest fail ➔ log + tiếp tục, học vẫn mượt.
- Không bịa số: thiếu dữ liệu thì gắn nhãn `PARTIAL` thay vì nội suy.
- Trùng event luôn resolve idempotent: bản đầu thắng, bản sau bỏ qua.

## Kịch bản quanh mốc 00:00 (biên ngày)
- Happy: học 23:50–23:59 ➔ toàn bộ tính cho ngày cũ; học 00:00–00:10 ➔ ngày mới.
- Alternative: phiên học vắt qua 00:00 ➔ tách thời lượng theo từng phía của mốc.
- Exception: đồng hồ client sai ➔ dùng `occurredAt` server khi nhận để chốt ngày.
