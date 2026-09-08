# 16 — Kế Hoạch Kiểm Thử: Gia Sư AI Hỏi Đáp

> 4 tầng: unit, integration, E2E, phi chức năng. Mock LLM/vector/Billing.

## 1. Chiến lược
- Mock `LlmProvider` (script timeout/5xx/thiếu citation), mock vector DB, fake `BillingService`.
- Fixture: 200 câu giáo dục có đáp án + chunk chuẩn để đo citation coverage.
- Múi giờ test cố định `Asia/Ho_Chi_Minh` (freeze clock quanh 00:00).

## 2. Test case
| Mã | Mục tiêu | Bước chính | Oracle |
|---|---|---|---|
| TC-01 | Hỏi đáp RAG happy path | Gửi hỏi hợp lệ còn quota | Đáp + citation, trừ 1 |
| TC-02 | Hết quota | Dùng đủ 20 rồi gửi thêm | 429 + paywall, không gọi LLM |
| TC-03 | Citation bắt buộc | Mock LLM thiếu citation | Regenerate 1 lần hoặc thú nhận |
| TC-04 | Hallucination số liệu | Câu hỏi số liệu ngoài chunk | Gắn NEEDS_REVIEW, không bịa |
| TC-05 | Vote + report | Vote 2 lần, report 6 lần/ngày | 409 và 429 đúng |
| TC-06 | Kiểm duyệt 2 chiều | Input/output độc hại | 422 / mẫu từ chối + log |
| TC-07 | Concurrent race | 2–5 request đồng thời khi còn 1 quota | 1 thắng, còn lại 429, ledger ≥ 0 |
| TC-08 | Idempotent retry | Timeout rồi retry cùng key (đã lưu) | Trả cached, 1 lần trừ quota |
| TC-09 | Partial completion | Stream đứt 40% rồi retry | 1 retry mới, fail thì hoàn quota + fallback |
| TC-10 | Reset daily | Freeze 23:59→00:01 | Ledger ngày mới reset |
| TC-11 | Duyệt report | Admin CONFIRMED | Chunk gắn cờ + event |
| TC-12 | Lịch sử/phân trang | 50 session, page size 20 | Đúng thứ tự, đủ 3 trang |

## 3. Phi chức năng
- Tải: k6 100 user concurrent hỏi, P95 TTFT < 4s, 0 double-charge.
- Bảo mật: thiếu JWT → 401; sai owner session → 403; thiếu key → 400.
- Bền bỉ: LLM 100% timeout 5 phút → 100% fallback + hoàn quota, alert P1 nổ.

## 4. Dữ liệu và môi trường
- Env `staging` có vector snapshot + LLM sandbox; cấm gọi model production đắt.
- Che PII mọi log; bộ độc hại 300 mẫu tiếng Việt riêng biệt.

## 5. Gate
- Pass khi: 12/12 TC pass, citation ≥ 95%, race 0 lỗi, moderation 0 bypass.

## 6. Lịch chạy kiểm thử
- Unit/integration: mỗi PR qua CI, chặn merge khi fail.
- TC-01→TC-06 chạy nightly trên staging; TC-07→TC-12 chạy trước M2/M3.
- Test tải k6 chạy cuối sprint 2, kết quả đính kèm báo cáo M2.

## 7. Quản lý defect
- P0 (bypass kiểm duyệt, mất quota): fix < 24h, hotfix không chờ sprint.
- P1 (citation sai, race): fix trong sprint hiện tại.
- P2 (UI paywall, lịch sử): dời tối đa 1 sprint, ghi vào debt.

## 8. Vai trò
- QA lead sở hữu bộ 200 câu + 300 mẫu độc hại; BE bổ sung mock khi đổi contract.
