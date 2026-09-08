# 04 — Mô Hình Trạng Thái: Gia Sư AI Hỏi Đáp

> Thực thể: `ChatSession`, `ChatMessage`, `AnswerReport`, `IdempotencyRecord`. Múi giờ `Asia/Ho_Chi_Minh`.

## 1. ChatSession
| Trạng thái | Ý nghĩa | Cho phép |
|---|---|---|
| `ACTIVE` | Đang hội thoại | Gửi hỏi, nhận đáp, vote |
| `ARCHIVED` | Lưu trữ (user ẩn) | Chỉ xem, không gửi mới |
| `DELETED` | Xóa mềm | Không hiển thị, giữ 30 ngày rồi purge |

- Chuyển đổi: `ACTIVE --> ARCHIVED --> ACTIVE` (unarchive); `ACTIVE/ARCHIVED --> DELETED` (một chiều).
- Guard: chỉ owner hoặc admin được đổi trạng thái; `DELETED` không revert sau 30 ngày.

## 2. ChatMessage (vòng đời 1 cặp hỏi–đáp)
| Trạng thái | Ý nghĩa |
|---|---|
| `PENDING_MODERATION` | Vừa nhận USER text, chờ lọc độc hại |
| `QUOTA_HELD` | Đã giữ 1 quota, chuẩn bị gọi RAG/LLM |
| `RETRIEVING` | Đang truy hồi `RagChunk` |
| `STREAMING` | Đang stream tokens từ LLM |
| `COMPLETED` | Lưu đủ USER + ASSISTANT + citation |
| `FAILED_RETRYABLE` | Timeout/5xx, được retry cùng key |
| `FAILED_FINAL` | Retry hết, đã rollback quota |
| `BLOCKED` | Vi phạm kiểm duyệt, không gọi LLM |

- Sơ đồ: `PENDING_MODERATION --> QUOTA_HELD --> RETRIEVING --> STREAMING --> COMPLETED`.
- Nhánh lỗi: `STREAMING --> FAILED_RETRYABLE --> RETRIEVING` (tối đa 1 vòng) hoặc `--> FAILED_FINAL`.
- Nhánh kiểm duyệt: `PENDING_MODERATION --> BLOCKED` (một chiều, log lý do).
- Nhánh quota: `PENDING_MODERATION --> FAILED_FINAL` với `reason=QUOTA_EXCEEDED` (không hold).

## 3. Trường hợp đặc biệt: LLM timeout nhưng local success
- Nếu stream đứt phía client mà server đã `COMPLETED` (đã persist ASSISTANT):
- Retry cùng `Idempotency-Key` không tạo message mới, trả lại bản cached `COMPLETED`.
- Không trừ quota lần 2; metric ghi `dedupeHit=true`.

## 4. AnswerReport
| Trạng thái | Ý nghĩa | Chuyển tiếp |
|---|---|---|
| `PENDING` | Mới tạo, chờ duyệt | → `CONFIRMED` / `REJECTED` / `DUPLICATE` |
| `CONFIRMED` | Admin xác nhận sai thật | → `FIXED` khi RAG/prompt đã vá |
| `REJECTED` | Báo cáo không đúng | Kết thúc |
| `DUPLICATE` | Trùng báo cáo trước | Link tới report gốc |
| `FIXED` | Đã khắc phục | Kết thúc, cộng uy tín reporter |

## 5. IdempotencyRecord (24h)
| Trạng thái | Ý nghĩa |
|---|---|
| `PROCESSING` | Đang xử lý request đầu |
| `SUCCEEDED` | Có kết quả, mọi retry trả cached |
| `FAILED` | Thất bại cuối, retry key mới mới được tính quota mới |

- TTL 24h; key scope `(userId, key)`; response cached gồm `messageId + quotaDeductionId`.

## 6. Quy tắc chung
- Mọi chuyển trạng thái ghi `AuditLog {from, to, actor, at}` theo giờ `Asia/Ho_Chi_Minh`.
- Không cho phép `COMPLETED --> STREAMING`; không sửa nội dung ASSISTANT sau khi hoàn tất (chỉ gắn cờ review).
- Concurrent: dùng optimistic locking `version` trên `ChatSession` khi append message.
