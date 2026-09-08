# 09 — Ma Trận Truy Vết: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Truy vết `Yêu cầu ➔ Use case ➔ BR ➔ Thực thể ➔ Test`.

| Yêu cầu gốc | Use case | Quy tắc (BR) | Thực thể | Test dự kiến |
| :--- | :--- | :--- | :--- | :--- |
| Giá Free/99k/990k VND | UC-501 | BR-P01, BR-P02 | `PlanCatalog` | TC-501 hiển thị bảng giá |
| Mua gói lần đầu | UC-502 | BR-F01, BR-F06 | `SubscriptionOrder` | TC-502 + TC-506 concurrent |
| Webhook capture | UC-506 | BR-X01, BR-X02, BR-F02 | `WebhookEvent`, `Receipt` | TC-507 verify + idempotent |
| Webhook retry/trùng | UC-506 | BR-X03 | `WebhookEvent` | TC-508 retry 3 lần trùng eventId |
| UNKNOWN + đối soát | UC-507 | BR-X04, BR-RV01, BR-RV02 | `SubscriptionOrder.reconcileStatus` | TC-509 reconcile bù |
| Gia hạn + grace 3 ngày | UC-503, UC-510 | BR-T02, BR-RV03 | `Subscription` | TC-503 grace D+2/D+4 |
| Hủy auto-renew | UC-504 | BR-T02 | `Subscription.autoRenew` | TC-504 giữ quyền đến expiresAt |
| Hoàn toàn phần/một phần/void | UC-505 | BR-F03, BR-F04, BR-F05 | `RefundRequest` | TC-505 full/partial/void |
| Quota AI/tài liệu/Quiz | UC-508 | BR-P03, BR-T01 | `QuotaLedger` | TC-510 trừ/chặn/reset 00:00 |
| Thông báo quota/hết hạn | UC-509 | BR-T04 | `Notification` | TC-511 ngưỡng 80%/100%, T-7/T-3/T-1 |
| Đơn TIMEOUT 30 phút | UC-502 | BR-T03 | `SubscriptionOrder` | TC-512 timeout giải phóng khóa |
| Lịch sử đơn/receipt | UC-511 | BR-F02 | `Receipt` | TC-513 danh sách + tải receipt |

## Độ bao phủ
- 12 yêu cầu ➔ 12 use case ➔ 21 BR ➔ 13 test case, không yêu cầu mồ côi.
- Mọi BR-F/BR-T/BR-X/BR-RV đều có ít nhất 1 test tương ứng (xem tệp 16).

## Phân tích khoảng trống (gap analysis)
- Không BR nào thiếu test: BR-F01➔TC-502/506, BR-F02➔TC-502/513, BR-F03/F04/F05➔TC-505, BR-F06➔TC-506.
- BR-T01➔TC-510, BR-T02➔TC-503, BR-T03➔TC-512, BR-T04➔TC-511.
- BR-X01➔TC-507, BR-X02/X03➔TC-508, BR-X04➔TC-509; BR-RV01/RV02➔TC-509, BR-RV03➔TC-503.
- BR-P01/P02/P03➔TC-501/510; BR-N01/N02/N03➔TC-511; BR-AU01/AU02➔TC-513.
- Không test nào thừa: mỗi TC trace về đúng 1–3 BR, không có TC mồ côi.

## Ma trận chiều ngược (test ➔ yêu cầu)
| Test | Phủ yêu cầu | Ghi chú |
| :--- | :--- | :--- |
| TC-502/506/507/508 | Mua, webhook, concurrent | Nhóm thu tiền lõi |
| TC-503/504/512 | Gia hạn, grace, timeout | Nhóm vòng đời |
| TC-505/513 | Hoàn tiền, receipt | Nhóm tài chính |
| TC-509 | Đối soát UNKNOWN | Nhóm phục hồi |
| TC-510/511 | Quota, thông báo | Nhóm kiểm soát và nhắc nhở |

## Quy tắc duy trì truy vết
- Thêm BR mới ➔ bắt buộc thêm dòng trace + ít nhất 1 TC trong cùng thay đổi.
- Đổi trạng thái DEC (tệp 06) ➔ rà soát lại các dòng trace liên quan trong 1 ngày.

## Checklist rà soát truy vết định kỳ (mỗi sprint)
- [ ] Không yêu cầu nào thiếu use case; không use case nào thiếu BR.
- [ ] Không BR nào thiếu test; không test nào không trace về BR.
- [ ] Mọi DEC CONFIRMED đã phản ánh vào BR/test tương ứng.
- [ ] Tệp 19 ghi nhận mọi thay đổi trace trong sprint.
