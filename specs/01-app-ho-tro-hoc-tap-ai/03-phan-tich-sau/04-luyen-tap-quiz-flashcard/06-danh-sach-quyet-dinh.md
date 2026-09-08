# 06 — Danh Sách Quyết Định: Luyện Tập Quiz & Flashcard

> Mọi DEC có `Status: PROPOSED`, `Source: AGENT_INFERENCE`.

## 1. Bảng quyết định
| Mã | Quyết định | Phương án chọn | Loại bỏ | Lý do | Status | Source |
|---|---|---|---|---|---|---|
| DEC-01 | Quota tính theo lần sinh | `1` lượt/lần sinh | Tính theo số câu | Đơn giản, dễ hiểu freemium | PROPOSED | AGENT_INFERENCE |
| DEC-02 | Ledger quota chung QA+Quiz | Chung `AiQuotaLedger` | Tách riêng | Minh bạch, chống lách | PROPOSED | AGENT_INFERENCE |
| DEC-03 | Timeout sinh LLM | `45s` (dài hơn QA 30s) | 30s | Sinh 10–20 câu JSON cần dài hơn | PROPOSED | AGENT_INFERENCE |
| DEC-04 | Retry sinh | `1` lần cùng key | 2–3 lần | Đủ chống transient, giữ chi phí | PROPOSED | AGENT_INFERENCE |
| DEC-05 | Partial JSON | Retry 1, fail thì hoàn quota | Lưu partial tính phí | Công bằng, tránh đề què | PROPOSED | AGENT_INFERENCE |
| DEC-06 | Timeout-nhưng-local-success | Trả cached DRAFT | Sinh mới | Tiết kiệm, idempotent | PROPOSED | AGENT_INFERENCE |
| DEC-07 | Chấm bài | Local so đáp án, miễn phí | Gọi LLM chấm | Nhanh, rẻ, chính xác trắc nghiệm | PROPOSED | AGENT_INFERENCE |
| DEC-08 | Double-submit | Optimistic `version` | DB lock nặng | Nhẹ, đủ cho nộp bài | PROPOSED | AGENT_INFERENCE |
| DEC-09 | Giải thích | LLM sinh sẵn lúc tạo đề | Sinh lúc xem (tốn quota) | Xem lại miễn phí, nhanh | PROPOSED | AGENT_INFERENCE |
| DEC-10 | Giới hạn Free | `10` câu/lần sinh | 20 như Paid | Tạo khác biệt upsell | PROPOSED | AGENT_INFERENCE |
| DEC-11 | Ôn flashcard | Thuật toán `SM-2` | Tùy chỉnh riêng | Chuẩn, đã kiểm chứng giáo dục | PROPOSED | AGENT_INFERENCE |
| DEC-12 | Số options | Cố định `4` | 3–5 linh hoạt | Đồng nhất UX chấm + validate | PROPOSED | AGENT_INFERENCE |

## 2. Cần Human Gate
- DEC-01/02 (quota chung): ảnh hưởng doanh thu freemium → Product Owner.
- DEC-07 (chấm local): ảnh hưởng niềm tin điểm số → Academic reviewer.
- DEC-10 (giới hạn Free): chính sách upsell → Product Owner.

## 3. Rủi ro nếu đảo
- Tính quota theo câu: user confused, khiếu nại tăng.
- Chấm bằng LLM: tốn quota, latency cao, điểm thiếu ổn định.
- Lưu partial tính phí: user trả tiền cho đề lỗi → mất niềm tin.

## 4. Tiếp theo
- POC sinh 10 câu/45s đo P95 và tỉ lệ JSON hợp lệ ≥ 98%.
- Chốt DEC-01/02/10 trước sprint 1 để khóa Billing.

## 5. Phụ lục: câu hỏi mở trước implement
- Q-01: Timeout 45s có đủ cho 20 câu HARD kèm giải thích dài không?
- Q-02: Ledger chung QA+Quiz có gây tranh chấp UX khi cạn quota vì chat không?
- Q-03: Chấm local có cần chữ ký chống sửa điểm phía client không?
- Q-04: Cache đề trùng có làm lộ đề giữa các user khác lớp không?
- Tiêu chí đóng: trả lời Q-01→Q-04 bằng số liệu POC sprint 1.

## 6. Lịch sử chốt quyết định
- 08/09/2026: khởi tạo DEC-01→DEC-12 ở trạng thái PROPOSED.
- Sprint planning sprint 1: chốt DEC-01/02/10; còn lại trước sprint 2.
- Mọi thay đổi DEC ghi dòng mới, không sửa dòng cũ.
- DEC cần dữ liệu POC được gắn cờ `NEEDS_DATA`, không chốt bằng cảm tính.
- Biên bản chốt DEC lưu cùng sprint planning note.
