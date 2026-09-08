# 06 — Danh Sách Quyết Định: Theo Dõi Tiến Độ Và Báo Cáo

> Tất cả `Status = PROPOSED`. ID `DEC-6xx`.

| ID | Quyết định | Phương án đề xuất | Phương án loại bỏ | Lý do | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| DEC-601 | Ngưỡng activeDay | `5 phút hoặc 1 Quiz hoặc 3 câu AI` | Mọi login đều tính | Chống streak ảo từ mở app thoáng qua | PROPOSED |
| DEC-602 | Định nghĩa tuần | `T2–CN, chốt 00:00 T2` | Tuần CN–T7 | Hợp lịch học/school Việt Nam | PROPOSED |
| DEC-603 | Ingest event | Async event bus + idempotent | Ghi đồng bộ | Không làm chậm luồng học | PROPOSED |
| DEC-604 | Cộng bù trễ | `≤ 7 ngày` cộng ngày gốc, quá thì ngày hiện tại | Cộng vô thời hạn | Cân bằng công bằng và toàn vẹn lịch sử | PROPOSED |
| DEC-605 | Báo cáo thiếu dữ liệu | Gắn nhãn `PARTIAL`, không bịa số | Ẩn báo cáo | Minh bạch, giữ niềm tin | PROPOSED |
| DEC-606 | Badge MVP | Chỉ 3 badge cơ bản (`streak-7`, `quiz-10`, `300-phút`) | Hệ badge phức tạp | Giảm scope P1, đủ động lực | PROPOSED |
| DEC-607 | Biểu đồ MVP | Line/bar 7 ngày, không so sánh cohort | So sánh với bạn bè | Tránh áp lực xã hội + scope lớn | PROPOSED |
| DEC-608 | Xuất báo cáo | Dời sau MVP (chỉ xem in-app + email) | Xuất PDF ngay | Tiết kiệm 1 sprint | PROPOSED |

## Cần User chốt
- DEC-601 (ngưỡng activeDay) và DEC-602 (định nghĩa tuần) ảnh hưởng mọi số liệu streak.

## Phân tích chi tiết từng quyết định
- DEC-601: ngưỡng `5 phút/1 Quiz/3 câu AI` đủ cao để loại mở app thoáng qua, đủ thấp để buổi học ngắn vẫn được ghi nhận. Thay ngưỡng sau này sẽ làm streak cũ/mới không so được — phải chốt sớm.
- DEC-602: tuần T2–CN khớp lịch học Việt Nam và giờ làm việc của job (chốt đêm Chủ Nhật); tuần CN–T7 chỉ hợp thị trường Mỹ.
- DEC-603: async + idempotent là chuẩn đo lường; ghi đồng bộ sẽ cộng 50–200ms vào mỗi câu trả lời AI — vi phạm nguyên tắc "không chặn học".
- DEC-604: 7 ngày che được cuối tuần + nghỉ lễ ngắn; dài hơn thì báo cáo chốt rồi phải tái bản quá nhiều, tốn tài nguyên và gây rối.
- DEC-605: gắn nhãn PARTIAL minh bạch hơn ẩn báo cáo (user tưởng mất dữ liệu) và hơn bịa số (mất niềm tin khi phát hiện).
- DEC-606: 3 badge đủ tạo khoảnh khắc "aha" đầu tiên; hệ badge 20+ loại để sau khi có số liệu mở badge nào hiệu quả.
- DEC-607: chart 7 ngày trả lời câu hỏi "tuần này mình học sao"; so sánh bạn bè dễ gây áp lực và cần thêm module xã hội.
- DEC-608: xuất PDF ít người dùng ở giai đoạn đầu (giả định từ sản phẩm tương tự); dời để tiết kiệm ~1 sprint.

## Quy trình chốt
1. PO chốt DEC-601/602 trước khi code streak (ảnh hưởng mọi công thức).
2. Các DEC còn lại chốt trước sprint 2; chưa chốt thì code theo đề xuất và ghi nợ.
3. Đổi ngưỡng activeDay sau phát hành = migration tái tính toàn bộ streak — cần duyệt riêng.

## DEC liên quan tới module khác (cần phối hợp)
| DEC | Module phối hợp | Nội dung phối hợp |
| :--- | :--- | :--- |
| DEC-601 | 03/04 | Ngưỡng activeDay quyết định event nào "đáng streak" |
| DEC-602 | Notify | Giờ chốt/gửi báo cáo tuần (00:00 chốt, 08:00 gửi) |
| DEC-603 | 03/04 | Schema event `v1` + cam kết async không chặn học |

## Nhật ký chốt (mẫu, điền khi họp)
| Ngày | DEC | Kết quả | Người chốt |
| :--- | :--- | :--- | :--- |
| _chờ_ | DEC-601 | _giữ 5 phút/1 Quiz/3 câu hay đổi_ | PO |
| _chờ_ | DEC-602 | _giữ T2–CN hay đổi_ | PO |
| _chờ_ | DEC-603 | _event bus dùng chung hay queue riêng_ | Tech lead |

## Nguyên tắc bổ sung DEC sau này
- Mọi DEC mới đánh số tiếp `DEC-609+`, không chèn số giữa.
- DEC đổi ngưỡng/số liệu phải kèm kế hoạch migration dữ liệu cũ.
- DEC bị thay thế giữ lại dòng cũ, thêm dòng mới ghi `THAY THE DEC-6xx`.
