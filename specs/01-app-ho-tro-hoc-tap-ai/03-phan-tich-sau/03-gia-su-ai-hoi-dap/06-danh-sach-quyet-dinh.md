# 06 — Danh Sách Quyết Định: Gia Sư AI Hỏi Đáp

> Mọi DEC dưới đây có `Status: PROPOSED` và `Source: AGENT_INFERENCE` theo yêu cầu.

## 1. Bảng quyết định
| Mã | Quyết định (Decision) | Phương án chọn | Phương án loại bỏ | Lý do | Status | Source |
|---|---|---|---|---|---|---|
| DEC-01 | Ngưỡng similarity RAG | `0.72` Top-K=5 | 0.6 Top-K=10 | Giảm nhiễu, citation chính xác hơn cho giáo dục | PROPOSED | AGENT_INFERENCE |
| DEC-02 | Timeout LLM | `30s` stream | 60s | Giữ UX chờ đợi chấp nhận được, giảm treo quota hold | PROPOSED | AGENT_INFERENCE |
| DEC-03 | Số lần retry | Tối đa `1` lần | 3 lần | Tránh đốt chi phí LLM và quota race | PROPOSED | AGENT_INFERENCE |
| DEC-04 | TTL Idempotency-Key | `24h` scope `(userId,key)` | 7 ngày | Đủ chống retry mạng, tiết kiệm storage | PROPOSED | AGENT_INFERENCE |
| DEC-05 | Chiến lược trừ quota | Hold → commit/rollback | Trừ trực tiếp sau LLM | Tránh vượt quota khi concurrent, hoàn được khi lỗi | PROPOSED | AGENT_INFERENCE |
| DEC-06 | Xử lý LLM timeout nhưng local success | Trả cached, không trừ thêm | Gọi LLM mới | Tiết kiệm chi phí, đúng ngữ nghĩa idempotent | PROPOSED | AGENT_INFERENCE |
| DEC-07 | Partial completion (stream đứt) | Retry 1 lần cùng key, backoff 2s | Nối stream cũ | Đơn giản, tránh trạng thái nửa vời | PROPOSED | AGENT_INFERENCE |
| DEC-08 | Concurrent per user | `Redis lock` TTL 35s + chờ 5s | Hàng đợi toàn cục | Cô lập theo user, không nghẽn chéo | PROPOSED | AGENT_INFERENCE |
| DEC-09 | Kiểm duyệt độc hại | 2 chiều in/out + khóa 1h sau 3 vi phạm | Chỉ lọc input | Output LLM cũng có thể độc hại | PROPOSED | AGENT_INFERENCE |
| DEC-10 | Thiếu ngữ cảnh RAG | Thú nhận + vẫn tính quota | Miễn phí khi không chắc | Tránh lạm dụng hỏi ngoài tài liệu để né quota | PROPOSED | AGENT_INFERENCE |
| DEC-11 | Hoàn quota lỗi hệ thống | Rollback khi `FAILED_FINAL` hệ thống | Không hoàn | Công bằng freemium, giảm khiếu nại | PROPOSED | AGENT_INFERENCE |
| DEC-12 | Model LLM mặc định | 1 model chính + 1 fallback rẻ hơn | Multi-model routing phức tạp | Ổn định phase 1, fallback khi provider sập | PROPOSED | AGENT_INFERENCE |

## 2. Quyết định cần con người phê duyệt (Human Gate)
- DEC-02 (timeout 30s): ảnh hưởng chi phí và UX, cần Product Owner chốt.
- DEC-05 (hold/commit): ảnh hưởng logic thanh toán/quota, cần Tech Lead duyệt migration.
- DEC-09 (khóa 1h): ảnh hưởng chính sách cộng đồng, cần duyệt pháp chế nội dung giáo dục.

## 3. Rủi ro nếu đảo quyết định
- Retry > 1: chi phí LLM tăng ~2–3x khi provider chập chờn.
- Không hold quota: race concurrent gây vượt quota Free, mất niềm tin freemium.
- TTL key quá ngắn (< 1h): retry muộn sau mất mạng bị double-charge.

## 4. Hành động tiếp theo
- POC đo latency P95 LLM và similarity tối ưu trên 200 câu hỏi giáo dục mẫu.
- Chốt DEC-01/02/05 trong sprint planning trước khi implement `AiTutorService`.

## 5. Phụ lục: câu hỏi mở trước implement
- Q-01: Ngưỡng similarity 0.72 có giữ được trên môn văn (ngữ nghĩa mở) không?
- Q-02: Timeout 30s có đủ cho câu trả lời dài kèm 5 citation không?
- Q-03: Redis lock per user có cần thay bằng queue khi scale 10k concurrent không?
- Q-04: Model fallback rẻ hơn có đảm bảo chất lượng citation tối thiểu không?
- Tiêu chí đóng: trả lời đủ Q-01→Q-04 bằng số liệu POC sprint 1.

## 6. Lịch sử chốt quyết định
- 08/09/2026: khởi tạo DEC-01→DEC-12 ở trạng thái PROPOSED.
- Sprint planning sprint 1: chốt DEC-01/02/05; các DEC còn lại trước sprint 2.
- Mọi thay đổi DEC ghi thêm dòng mới, không sửa dòng cũ.
- DEC cần dữ liệu POC được gắn cờ `NEEDS_DATA`, không chốt bằng cảm tính.
- Biên bản chốt DEC lưu cùng sprint planning note.
