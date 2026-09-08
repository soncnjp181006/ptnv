# 11 — Đánh Giá Chất Lượng: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Thang 1–5 cho `Đầy đủ / Nhất quán / Khả kiểm / Khả thi`. Ngưỡng đạt ≥ 3.5.

| Tiêu chí | Điểm | Nhận xét |
| :--- | :--- | :--- |
| Đầy đủ use case | 4 | Đủ mua/gia hạn/hủy/hoàn/webhook/đối soát/quota/grace |
| Nhất quán trạng thái | 4 | 2 máy trạng thái tách bạch, không chồng lấn |
| Khả kiểm (testable) | 4 | Mọi BR có test tương ứng, ngưỡng số rõ (30p, 3 ngày, 80%) |
| Khả thi kỹ thuật | 3.5 | Webhook + reconcile chuẩn; cần chốt cổng (DEC-501) |
| An toàn tiền | 4 | Idempotency 2 lớp, UNKNOWN không tự kích hoạt |
| Rõ ràng ngôn ngữ | 4 | Thuật ngữ capture/void/refund/partial thống nhất |

## Điểm yếu và hành động
- W1: Chưa chốt cổng thanh toán ➔ Action: chốt DEC-501 trong tuần 1.
- W2: Chính sách phí hoàn chưa rõ ai chịu ➔ Action: chốt cùng DEC-506.
- W3: Thiếu proration đổi gói ➔ Chấp nhận dời phase 2, ghi rõ trên trang giá.
- Điểm trung bình: `3.9/5` — ĐẠT, đủ điều kiện sang thiết kế chi tiết sau khi chốt 3 DEC.

## Kế hoạch cải thiện chất lượng
| Vấn đề | Hành động | Owner | Hạn |
| :--- | :--- | :--- | :--- |
| DEC-501/502/506 chưa chốt | Họp chốt cổng + giá + phí hoàn | PO + Kế toán | Tuần 1 |
| Thiếu ví dụ số hoàn partial | Bổ sung bảng tính mẫu vào BR-F04 | Agent phân tích | Cùng vòng này (đã bổ sung) |
| Chưa có test tải webhook | Thêm TC tải 100 req/s vào tệp 16 | QA | Sprint 2 |
| Trang giá thiếu ghi chú proration | Thêm dòng "chưa hỗ trợ đổi giữa chu kỳ" | UI/PO | Trước phát hành |

## Tiêu chí cổng chất lượng (quality gate)
- Không sang thiết kế nếu còn BR nào thiếu test trace (hiện tại: đủ).
- Không phát hành nếu test TC-506/508/509 chưa xanh (nhóm tiền + phục hồi).
- Review chéo với module 03/04 về hợp đồng `checkQuota` trước khi đóng phân tích.

## Chi tiết điểm theo nhóm use case
| Nhóm | Điểm | Lý do |
| :--- | :--- | :--- |
| Mua–webhook–kích hoạt | 4.5 | Idempotency 2 lớp, ví dụ rõ, test đầy đủ |
| Gia hạn–grace–hủy | 4.0 | Quy tắc 3 ngày rõ, còn chờ chốt nhắc T-7/T-3/T-1 |
| Hoàn–void–partial | 3.5 | Công thức có, nhưng ngưỡng duyệt + phí còn PROPOSED |
| Quota–thông báo | 4.0 | Ngưỡng 80%/100% rõ, cần test tải checkQuota |
| Đối soát–phục hồi | 4.0 | SLA 24h + runbook, cần diễn tập chaos 1 lần |

## Rủi ro chất lượng còn lại
- RQ-1: phân tích tốt nhưng chốt DEC muộn ➔ mitigation họp chốt tuần 1 (đã lên lịch trong tệp 14).
- RQ-2: hiểu sai phí gateway ➔ mitigation mời kế toán review BR-F03/F04 trước sprint 2.
- RQ-3: test concurrent thiếu ➔ mitigation TC-506 bắt buộc xanh trước M2.

## Lịch sử chấm điểm
| Vòng | Điểm | Thay đổi chính |
| :--- | :--- | :--- |
| v0.5 | 3.2 | Mới có use case + flowchart, thiếu BR hoàn và reconcile |
| v0.9 | 3.6 | Đủ BR-F/T/X/RV + máy trạng thái kép |
| v1.0 | 3.9 | Đủ trace test + KPI + runbook; còn 3 DEC chờ chốt |
