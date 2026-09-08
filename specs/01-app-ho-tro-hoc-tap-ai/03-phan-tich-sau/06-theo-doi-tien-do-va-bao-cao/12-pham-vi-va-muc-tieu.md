# 12 — Phạm Vi Và Mục Tiêu: Theo Dõi Tiến Độ Và Báo Cáo

> Mục tiêu P1: đo được, động viên được, không làm chậm học.

## 12.1 Mục tiêu đo được
- 100% Q&A và Quiz nộp bài có event trong 5s; trùng eventId không nhân đôi số liệu.
- Dashboard mở p95 < 1s (cache); báo cáo tuần gửi trước 08:00 thứ Hai đạt ≥ 99%.
- Streak tính đúng 100% case kiểm (liên tục/ngắt/bù trễ) theo chuẩn 00:00.

## 12.2 Trong phạm vi MVP cơ bản (IN)
- Ingest 3 loại event, `DailyProgress`, streak, điểm TB, tổng thời gian.
- Dashboard 7 ngày + báo cáo tuần email/in-app + 3 badge cơ bản + cộng bù ≤ 7 ngày.

## 12.3 Ngoài phạm vi (OUT — phase 2)
- Mục tiêu tuần tùy biến, leaderboard/bạn bè, so sánh cohort, AI gợi ý lộ trình.
- Xuất PDF đẹp, chia sẻ mạng xã hội, phân tích chi tiết theo môn/chủ đề.

## 12.4 Giả định & ràng buộc
- Module 03/04 phát event đúng schema `v1`; sai format ➔ dead-letter, không chặn học.
- Múi giờ hiển thị `Asia/Ho_Chi_Minh`; lưu DB UTC + trường tz.

## 12.5 Chỉ số thành công chi tiết (KPI)
| KPI | Mục tiêu | Cách đo |
| :--- | :--- | :--- |
| Event có mặt ≤ 5s | ≥ 99.5% | `receivedAt - occurredAt` |
| Trùng event gây sai số | = 0 | kiểm tra định kỳ `COUNT(eventId)` |
| Dashboard p95 | < 1s | APM frontend |
| Báo cáo tuần đúng giờ | ≥ 99%/4 tuần | log job + Notify |
| Streak đúng case chuẩn | 100% | bộ TC-603/609 |
| Tỉ lệ mở báo cáo tuần | ≥ 30% | log email/in-app |

## 12.6 Ràng buộc phi chức năng
- Hiệu năng: ingest chịu 1000 event/phút giờ cao điểm tối; dashboard đọc cache, không quét event thô.
- Sẵn sàng: job chốt ngày/tuần idempotent, chạy lại an toàn sau deploy lỗi.
- Riêng tư: số liệu học tập chỉ chủ tài khoản + Support được ủy quyền mới thấy.

## 12.7 Tuyên bố giá trị (để viết thông báo tính năng)
- Dashboard: "Nhìn một mắt biết ngay tuần này mình học ra sao."
- Streak: "Giữ lửa mỗi ngày — nghỉ một hôm là tính lại từ đầu, cố nhé!"
- Báo cáo tuần: "Sáng thứ Hai nhận tóm tắt tuần + mục tiêu tuần mới."
- Cam kết số liệu: "Thiếu ngày nào do lỗi hệ thống, tụi mình cộng bù đúng ngày gốc."

## 12.8 Ngoài phạm vi tuyệt đối (không làm cả phase 2 nếu chưa duyệt)
- Chấm điểm so sánh giữa các học viên (xếp hạng công khai) — nhạy cảm, cần duyệt riêng.
- Bán dữ liệu học tập cho bên thứ ba — cấm tuyệt đối.
- Tự động đăng thành tích lên mạng xã hội khi chưa có đồng ý rõ ràng.

## 12.9 Giả định về hành vi user (để hiệu chỉnh sau số liệu)
- User mở dashboard 2–3 lần/tuần; báo cáo tuần là điểm chạm chính với user lười mở app.
- Ngưỡng activeDay hiện tại là giả định tốt nhất; hiệu chỉnh sau 1 tháng tỉ lệ streak.
- Tuần trắng chủ yếu do quên — tin động viên + mục tiêu mini 15 phút/ngày là đủ.
