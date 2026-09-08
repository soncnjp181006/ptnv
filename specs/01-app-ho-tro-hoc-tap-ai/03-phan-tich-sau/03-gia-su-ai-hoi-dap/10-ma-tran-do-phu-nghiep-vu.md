# 10 — Ma Trận Độ Phủ Nghiệp Vụ: Gia Sư AI Hỏi Đáp

> Đo bao phủ UC × SC × BR; mục tiêu Must 100%.

## 1. Phủ theo Use Case
| Use Case | Tổng SC | Đã phân tích | BR gắn | TC dự kiến | Tỉ lệ |
|---|---|---|---|---|---|
| UC-01 Gửi hỏi | 3 | 3 | BR-001/004/005 | TC-01 | 100% |
| UC-02 Quota | 4 | 4 | BR-001/002/007/009 | TC-02,07,10 | 100% |
| UC-03 RAG | 3 | 3 | BR-004/010 | TC-03 | 100% |
| UC-04 Trả lời + citation | 3 | 3 | BR-004/010 | TC-03,04 | 100% |
| UC-05 Vote | 2 | 2 | BR-008 | TC-05 | 100% |
| UC-06 Báo sai | 2 | 2 | BR-008 | TC-05 | 100% |
| UC-09 Kiểm duyệt | 2 | 2 | BR-005 | TC-06 | 100% |
| UC-10 Retry | 3 | 3 | BR-003/006/007 | TC-08,09 | 100% |
| UC-13 Concurrent | 1 | 1 | BR-002 | TC-07 | 100% |
| UC-12 Quota view | 1 | 1 | BR-009 | TC-10 | 100% |
| UC-11 Duyệt report | 1 | 1 | BR-008 | TC-11 | 100% |
| UC-07/08 Lịch sử/phiên | 0 | 0 | — | TC-12 | 60% (thiếu EXC) |

## 2. Phủ theo Business Rule
| BR | UC phủ | SC phủ | Trạng thái |
|---|---|---|---|
| BR-001 → BR-010 | Đủ 10/10 có UC | Đủ 10/10 có SC | PASS (trừ UC-07/08) |

## 3. Phủ rủi ro đặc thù AI
| Rủi ro | Bao phủ bởi | Đánh giá |
|---|---|---|
| Hallucination | BR-004/010, SC-UC04-03, TC-04 | Đủ |
| Hết quota giữa chừng | SC-UC02-03, TC-07 | Đủ |
| LLM timeout + local success | SC-UC10-01, TC-08 | Đủ |
| Partial completion | SC-UC10-02, TC-09 | Đủ |
| Concurrent vượt quota | SC-UC13-01, TC-07 | Đủ |
| Độc hại in/out | SC-UC01-03/UC09-01, TC-06 | Đủ |

## 4. Lỗ hổng và biện pháp khắc phục
- UC-07/08 thiếu kịch bản EXC (tìm kiếm quá tải, xóa nhầm) → bổ sung 2 SC trước sprint 2.
- UC-14 (xuất PDF Paid) chưa phân tích sâu → dời sang backlog Could.

## 5. Kết luận
- Độ phủ Must: `100%`; Should: `92%`; mục tiêu release: Should ≥ 95% sau khi bổ sung SC lịch sử.

## 6. Kế hoạch đóng lỗ hổng
- W-01: bổ sung SC-UC07-01 (tìm kiếm quá tải) và SC-UC08-01 (xóa nhầm phiên) trước sprint 2.
- W-02: SC mới phải gắn BR và TC tương ứng, cập nhật file 09 ngay trong cùng PR.
- W-03: UC-14 xuất PDF giữ ở Could, không tính vào gate release 1.0.
- Sau W-01/W-02: đo lại Should, kỳ vọng ≥ 95% để đủ điều kiện release.
