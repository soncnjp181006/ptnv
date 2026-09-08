# 11 — Đánh Giá Chất Lượng: Quiz & Flashcard

> Thang 1–5; release ≥ 3.5, không Must nào < 3.

## 1. Bảng điểm
| Thuộc tính | Điểm | Nhận xét | Cải thiện |
|---|---|---|---|
| Đầy đủ chức năng | 4 | Đủ sinh/làm/chấm/giải thích/làm lại/SM-2 | Bổ sung PUBLISHED lớp sau |
| Chính xác đáp án | 3 | Validate 4 options + khớp RAG, vẫn rủi ro hallucination | Thêm duyệt teacher mẫu |
| Công bằng quota | 4 | Ledger chung + hoàn khi lỗi | Dashboard khiếu nại |
| Hiệu năng | 4 | Sinh 45s bất đồng bộ + chấm local < 200ms | Thêm progress SSE sinh đề |
| Khả dụng retry | 4 | Retry 1 + cached + fallback | Mẫu đề offline dự phòng |
| Chống gian lận | 3 | Chặn xem sớm + version nộp | Thêm xáo trộn options |
| Khả kiểm thử | 4 | Mock LLM JSON lỗi/timeout/partial | Contract test Billing |
| Truy vết học tập | 5 | History + wrongIds + streak đầy đủ | — |

## 2. Checklist giáo dục + AI
- [x] Mỗi câu có đúng 1 đáp án + giải thích + nguồn trang.
- [x] Độ khó EASY/MEDIUM/HARD phân biệt được qua review mẫu.
- [x] Làm bài/xem lại miễn phí, chỉ sinh tốn quota.
- [ ] Xáo trộn thứ tự options mỗi attempt (backlog chống học vẹt).
- [x] Không mất bài khi double-click nộp.

## 3. Nợ phân tích
- D-01: chưa chốt xáo trộn options → sprint 3.
- D-02: chưa có rubric duyệt teacher → backlog Could.
- D-03: thiếu test tải sinh 50 job đồng thời → TQ-09b k6.

## 4. Gate
- PASS nếu: JSON hợp lệ ≥ 98%/100 lần sinh mẫu, 0 double-SCORED, 0 xem sớm lọt.
- FAIL nếu: đáp án sai > 3% trên bộ 300 câu duyệt tay.

## 5. Kết luận
- Trung bình `3.9/5` → ĐẠT có điều kiện; đóng D-01 trước release.

## 6. Hành động cải thiện sau đánh giá
- A-01: chốt xáo trộn options (D-01) trong sprint 3, demo 2 attempt cùng đề khác thứ tự.
- A-02: rubric duyệt teacher cho đề HARD (D-02) vào backlog Could có mẫu.
- A-03: test tải k6 50 job sinh + 200 submit (D-03) trước M3.
- A-04: đánh giá lại cuối mỗi sprint; release chỉ khi không Must nào < 3.
- A-05: tỉ lệ báo sai/ngày > 5% tự tạo incident P2 cho team nội dung.
- A-06: điểm trung bình rơi > 15% sau release phải rollback prompt sinh.
- Mọi action có owner và hạn; quá hạn tự chuyển debt P1.
- Kết quả A-01→A-06 cập nhật bảng điểm mục 1, giữ nguyên dòng cũ.
- A-07: kiểm tra ngẫu nhiên 20 đề/tuần để giữ chất lượng liên tục.
- Mọi incident đáp án sai P1 đều sinh SC hồi tố trong file 03.
- Chuẩn đáp án đúng đo trên mẫu độc lập, không đo trên dữ liệu train prompt.
