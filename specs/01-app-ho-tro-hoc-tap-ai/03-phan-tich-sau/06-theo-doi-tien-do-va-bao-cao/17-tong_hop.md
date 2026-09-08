# 17 — Tổng Hợp: Theo Dõi Tiến Độ Và Báo Cáo

> 1 trang cho stakeholder: đo gì, báo cáo sao, động lực nào (P1, MVP cơ bản).

## Đo gì
- Mọi hoạt động học thành event: hỏi AI, nộp Quiz (kèm điểm), phiên học (kèm phút).
- Ngày `activeDay` khi đủ ngưỡng (5 phút / 1 Quiz / 3 câu AI); streak đếm ngày liên tục.
- Tổng hợp: tổng thời gian, điểm TB %, streak tốt nhất, so sánh % với tuần trước.

## Báo cáo sao
- Dashboard 7 ngày mở < 1s; báo cáo tuần chốt 00:00 thứ Hai, gửi trước 08:00.
- Thiếu dữ liệu gắn `PARTIAL`, event trễ ≤ 7 ngày cộng bù + tái bản `REVISED`.
- Event bus async + idempotent nên không làm chậm học, không nhân đôi số.

## Động lực và bước tiếp
- 3 badge MVP (`streak-7`, `quiz-10`, `300-phút`) + tin động viên tuần trắng.
- Phase 2: mục tiêu tuần, leaderboard, xuất PDF, gợi ý AI.
- Cần chốt DEC-601 (ngưỡng activeDay) và DEC-602 (tuần T2–CN) trước khi code.

## Ví dụ trải nghiệm user mới (tuần đầu)
- Ngày 1: học 20 phút + 1 Quiz ➔ dashboard hiện streak 1, thời gian 20 phút.
- Ngày 2–5: duy trì ➔ streak 5, biểu đồ 5 cột tăng dần — cảm giác tiến bộ rõ.
- Thứ Hai: nhận báo cáo "Tuần đầu: 120 phút, 6 Quiz, điểm TB 72% — tăng tốc nào!".
- Ngày 7 liên tục ➔ nhận badge `streak-7` + tin chúc mừng — móc giữ chân đầu tiên.

## Rủi ro lớn nhất trong 1 câu
- Ngưỡng activeDay chốt muộn hoặc đổi sau phát hành ➔ toàn bộ streak lịch sử phải tái tính; vì vậy DEC-601/602 phải CONFIRMED trước dòng code streak đầu tiên.

## FAQ cho stakeholder
- "Vì sao chỉ MVP cơ bản mà cần 2 sprint?" ➔ Vì đúng số khó hơn đủ số: idempotent, cắt ngày, bù trễ, tái bản đều cần test biên kỹ.
- "Vì sao chưa có leaderboard?" ➔ Cần thêm module xã hội + kiểm duyệt + chống gian lận; để phase 2 sau khi số liệu ổn.
- "Báo cáo tuần có làm phiền user?" ➔ 1 tin/tuần + tuần trắng chỉ động viên + cho tắt (ST-611) — mức tối thiểu.
- "Streak mất oan thì sao?" ➔ Tra cứu theo ngày + cộng bù tay có audit (ST-609/610), SLA 2 ngày.
- "Khi nào thấy hiệu quả giữ chân?" ➔ Sau 4 báo cáo tuần (~1 tháng) mới đủ dữ liệu so sánh retention.

## Phụ lục thuật ngữ (dùng chung toàn module)
| Thuật ngữ | Nghĩa |
| :--- | :--- |
| `activeDay` | Ngày đủ ngưỡng học (5 phút / 1 Quiz / 3 câu AI) |
| `streak` | Số ngày active liên tục — ngắt 1 ngày là reset |
| `idempotent` | Gửi lại event không nhân đôi số liệu (khóa `eventId`) |
| `PARTIAL` | Báo cáo thiếu dữ liệu — gắn nhãn thay vì bịa số |
| `REVISED` | Bản tái bản sau bù trễ — bản cũ giữ nguyên để đối chiếu |
| `dead-letter` | Kho event lỗi format chờ xử lý tay, không retry vô hạn |

## Đọc tiếp ở đâu (bản đồ tệp)
- Muốn hiểu luồng số liệu: đọc tệp 01 ➔ 04 ➔ 05 theo thứ tự.
- Muốn chốt scope: đọc tệp 10 ➔ 12 ➔ 06 (hỏi PO 2 DEC).
- Muốn bắt tay code/test: đọc tệp 15 ➔ 16 ➔ 14 ➔ 18.
