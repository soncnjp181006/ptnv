# 11 — Đánh Giá Chất Lượng: Gia Sư AI Hỏi Đáp

> Thang 1–5 cho 8 thuộc tính; ngưỡng release ≥ 3.5, không thuộc tính Must nào < 3.

## 1. Bảng điểm
| Thuộc tính | Điểm | Nhận xét | Cải thiện |
|---|---|---|---|
| Đầy đủ chức năng | 4 | Đủ quota/RAG/citation/vote/report/retry | Bổ sung UC-14 xuất PDF sau |
| Chính xác (citation) | 3 | Ngưỡng 0.72 + regenerate 1 lần, vẫn rủi ro hallucination số liệu | Thêm numeric-check |
| An toàn kiểm duyệt | 4 | Lọc 2 chiều + khóa 1h | Bổ sung allowlist giáo dục |
| Công bằng quota | 4 | Hold/commit/rollback + idempotency | Thêm dashboard khiếu nại |
| Hiệu năng | 3 | P95 RAG 1.2s + LLM 30s, concurrent phải chờ lock | Cache embedding câu hỏi hot |
| Khả dụng (fallback) | 4 | Retry 1 + fallback apology + hoàn quota | Thêm model fallback rẻ |
| Khả kiểm thử | 4 | Mock LLM/vector, TC bao phủ EXC | Thêm contract test Billing |
| Khả truy vết | 5 | SourceRef + LlmCallLog + AuditLog đầy đủ | — |

## 2. Checklist chất lượng giáo dục + AI
- [x] Mọi câu từ tài liệu có citation click được tới trang.
- [x] Câu ngoài tài liệu thú nhận rõ, không bịa.
- [x] Ngôn ngữ phù hợp lứa tuổi, không độc hại.
- [ ] Tỉ lệ `NOT_HELPFUL` > 20% tự tạo cảnh báo (task backlog).
- [x] Không trừ quota oan khi lỗi hệ thống (rollback).

## 3. Nợ phân tích (debt)
- D-01: chưa chốt numeric-check chống hallucination số liệu → owner AI, hạn sprint 2.
- D-02: chưa có đánh giá tiếng Việt chuyên ngành (y khoa/luật) → backlog Could.
- D-03: thiếu test tải concurrent 100 user → bổ sung TC-07b k6.

## 4. Điều kiện Gate
- PASS nếu: citation coverage ≥ 95% trên bộ 200 câu mẫu, quota race test 0 double-charge.
- FAIL nếu: còn hallucination số liệu > 5% hoặc moderation bypass 1 ca P0.

## 5. Kết luận
- Trung bình `3.9/5` → ĐẠT có điều kiện; phải đóng D-01 trước release.

## 6. Hành động cải thiện sau đánh giá
- A-01: POC numeric-check (D-01) trong sprint 2, owner team AI, demo trên 200 câu số liệu.
- A-02: cảnh báo tự động khi `NOT_HELPFUL` > 20%/ngày (task backlog sprint 3).
- A-03: test tải k6 100 user concurrent (D-03) trước M2, tiêu chí 0 double-charge.
- A-04: đánh giá lại sau mỗi sprint; release chỉ khi không còn Must nào < 3.
- Kết quả A-01→A-04 cập nhật vào bảng điểm mục 1, giữ nguyên dòng cũ.
- A-05: dashboard helpfulRate theo môn học để phát hiện môn yếu.
- A-06: retro chất lượng hằng tháng, cập nhật checklist mục 2.
- Điểm dưới 3 ở bất kỳ Must nào → block release, không xin ngoại lệ.
- Owner action báo tiến độ daily; quá hạn 3 ngày escalate lên PO.
- Đóng hết nợ D-01→D-03 mới được gắn cờ release candidate.
- Bằng chứng đóng nợ (link POC, kết quả k6) đính kèm file này.
