# 10 — Ma Trận Độ Phủ Nghiệp Vụ: Quiz & Flashcard

> Mục tiêu Must 100%, Should ≥ 95%.

## 1. Phủ theo Use Case
| Use Case | SC | BR gắn | TQ dự kiến | Tỉ lệ |
|---|---|---|---|---|
| UC-01 Sinh Quiz | 4 | BR-001/004/005/010 | TQ-01,11 | 100% |
| UC-02 Flashcard | 1 | BR-001/004 | TQ-02 | 100% |
| UC-03 Quota sinh | 3 | BR-001/007 | TQ-03,07 | 100% |
| UC-04 Start | 1 | BR-002 | TQ-04 | 100% |
| UC-05 Nộp/chấm | 3 | BR-002/008 | TQ-04,10 | 100% |
| UC-06 Giải thích | 2 | BR-009 | TQ-05 | 100% |
| UC-07 Làm lại | 2 | BR-002 | TQ-06 | 100% |
| UC-08 SM-2 | 1 | BR-002 | TQ-06 | 100% |
| UC-10 Báo sai | 1 | BR-010 | TQ-08 | 100% |
| UC-11 Retry sinh | 3 | BR-003/006/007 | TQ-09 | 100% |
| UC-12 Double-submit | 1 | BR-003/008 | TQ-10 | 100% |
| UC-13 Quota view | 1 | BR-001 | TQ-07 | 100% |
| UC-09 History | 0 | BR-002 | TQ-06 | 70% (thiếu EXC) |
| UC-14 Archive | 0 | — | — | 0% (Could) |

## 2. Phủ theo BR
| BR | UC phủ | SC phủ | Trạng thái |
|---|---|---|---|
| BR-001→BR-010 | 10/10 có UC | 10/10 có SC | PASS (trừ UC-09/14) |

## 3. Phủ rủi ro AI
| Rủi ro | Bao phủ | Đánh giá |
|---|---|---|
| Hallucination đáp án | BR-004/005, SC-UC01-04, TQ-11 | Đủ |
| Partial JSON | SC-UC11-02, TQ-09 | Đủ |
| Timeout + local success | SC-UC11-01, TQ-09 | Đủ |
| Hết quota giữa chừng QA+Quiz | SC-UC03-03, TQ-07 | Đủ |
| Double-submit | SC-UC05-03, TQ-10 | Đủ |
| Xem giải thích sớm (gian lận) | SC-UC06-02, TQ-05 | Đủ |

## 4. Lỗ hổng
- UC-09 thiếu EXC mất history; UC-14 Could dời phase 2.

## 5. Kết luận
- Must 100%, Should 94%; đóng thêm 1 SC history để đạt 95%+.

## 6. Kế hoạch đóng lỗ hổng
- W-01: bổ sung SC-UC09-01 (mất history giữa chừng) trước sprint 2.
- W-02: SC mới gắn BR-002 + TQ-06, cập nhật file 09 ngay trong cùng PR.
- W-03: UC-14 Archive giữ Could, không tính gate release 1.0.
- Sau W-01/W-02 đo lại Should, kỳ vọng ≥ 95% để Go release.
