# 13 — Khả Thi và Rủi Ro: Quiz & Flashcard

> Kỹ thuật sinh JSON, quota chung, chấm local, SM-2.

## 1. Khả thi kỹ thuật
| Hạng mục | Đánh giá | Ghi chú |
|---|---|---|
| LLM sinh JSON Quiz | Cao | JSON mode + schema validate |
| RAG ngữ liệu sinh đề | Cao | Tái dùng BV-03 |
| Chấm local trắc nghiệm | Cao | So sánh option O(1) |
| SM-2 flashcard | Cao | Thuật toán chuẩn |
| Retry partial JSON | Trung bình | Cần parser chịu lỗi + resume |
| Kiểm đáp án khớp RAG | Trung bình | So khớp số liệu custom |

## 2. Khả thi chi phí
- Sinh tốn 1 quota/lần (~1 LLM call dài + 1 retry tối đa); làm/chấm/xem miễn phí.
- Ledger chung QA+Quiz giữ trần Free 20/ngày toàn app, dễ dự toán LLM.
- Cache đề theo `(docHash, count, difficulty)` giảm sinh trùng ~20%.

## 3. Ma trận rủi ro
| Mã | Rủi ro | P | I | Giảm thiểu | Dự phòng |
|---|---|---|---|---|---|
| R-01 | JSON thiếu câu (partial) | Cao | TB | Schema validate + retry 1 | Hoàn quota + báo lỗi |
| R-02 | 2 đáp án đúng / không có đáp án đúng | TB | Cao | Check duy nhất correctOption | Regenerate 1 + NEEDS_REVIEW |
| R-03 | Hallucination đáp án ngoài tài liệu | Cao | Cao | Khớp RAG + duyệt mẫu | Cảnh báo + báo sai |
| R-04 | Timeout 45s nhưng đã lưu nháp | TB | TB | Dedupe trả cached | Không trừ thêm |
| R-05 | Double-submit 2 tab | Cao | TB | Version optimistic | 409 + link kết quả |
| R-06 | Hết quota giữa chừng (QA+Quiz race) | Cao | TB | Atomic ledger chung | 1 thắng 1 rollback 429 |
| R-07 | Xem giải thích sớm gian lận | Thấp | TB | Guard SCORED + 403 | Log + cảnh cáo |
| R-08 | Đề khó/dễ sai độ khó yêu cầu | TB | Thấp | Prompt difficulty + sampling | Cho làm lại miễn phí |

## 4. Pháp lý/giáo dục
- Disclaimer đáp án AI cần teacher kiểm trước khi dùng thi thật.
- PII trong đề sinh (tên trong tài liệu) → lọc trước khi lưu.

## 5. Kết luận
- KHẢ THI; No-go nếu JSON hợp lệ < 95% trên 100 lần sinh mẫu staging.

## 6. Kế hoạch POC và ngân sách
- POC-01 (tuần 1): sinh 100 lần mẫu, đo JSON hợp lệ và P95 thời gian.
- POC-02 (tuần 2): khớp đáp án-RAG trên 300 câu duyệt tay, pass ≥ 97%.
- Ngân sách: dự phòng 20% quota LLM staging cho retry/partial trong UAT.
- Nếu POC-01 < 95%: giảm mặc định 20→10 câu, siết schema, làm lại POC.
- Go/No-go chốt tại review sprint 1, có biên bản và owner ký.
- Rủi ro R-02/R-03 có dashboard theo dõi hằng ngày trong sprint 2–3.
- Mọi rủi ro I=Cao phải drill giả lập trước M3.
- Drill R-05 (provider sập) chạy 1 lần trước M3, ghi kết quả vào file 16.
