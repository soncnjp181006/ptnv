# 08 — Ảnh Hưởng Liên Module: Gia Sư AI Hỏi Đáp

> Liên kết: Tài liệu, Subscription/Quota, Quiz/Flashcard, Thông báo, Admin.

## 1. Ma trận phụ thuộc
| Module liên quan | Hướng | Nội dung tích hợp | Mức độ |
|---|---|---|---|
| Quản lý tài liệu `Document` | Inbound | RAG đọc `Document + RagChunk`; tài liệu bị xóa → chunk orphan | Cao |
| Subscription `Billing` | Inbound | Đọc `plan FREE/PAID`, trạng thái `ACTIVE/EXPIRED`; hết hạn hạ quota | Cao |
| Quiz/Flashcard `BV-04` | Outbound | Câu hỏi hay có thể "Lưu thành Flashcard" 1 chạm | Trung bình |
| Thông báo `Notification` | Outbound | Báo quota sắp hết (còn 3), báo `AnswerReport` đã FIXED | Thấp |
| Admin kiểm duyệt | Outbound | Đẩy `AnswerReport PENDING` vào Review Queue | Trung bình |
| LlmProvider (ngoài) | Outbound | Gọi chat + embedding; phụ thuộc timeout/rate-limit | Cao |

## 2. Hợp đồng tích hợp (contract)
- `DocumentService.getChunks(docIds, topK)` trả `[{chunkId, text, page, score}]`, timeout 5s.
- `BillingService.getQuota(userId, date)` trả `{plan, used, remaining, resetAt}` múi giờ `Asia/Ho_Chi_Minh`.
- `BillingService.deduct/commit/refund` phải idempotent theo `deductionId`.
- Sự kiện: `qa.answered {msgId, helpful}`, `quota.exhausted {userId}`, `report.confirmed {repId}`.

## 3. Kịch bản lan truyền lỗi
| Sự cố nguồn | Ảnh hưởng tới QA | Ứng phó |
|---|---|---|
| Vector DB chậm/sập | RAG timeout → fallback cache hoặc báo thử lại | Circuit breaker + cache chunk |
| `Billing` timeout | Không xác định quota | Fail-closed: cho phép 1 lượt tạm + ghi nợ đối soát |
| LLM sập hoàn toàn | Không thể trả lời | Fallback apology + hoàn quota + alert |
| `Document` bị xóa giữa chừng | Citation gãy | Giữ snapshot chunk 30 ngày cho message đã lưu |
| Hết quota giữa chừng (concurrent) | 1 request 429 | Rollback hold, gợi ý nâng cấp |

## 4. Tác động trạng thái Subscription
- `TRIAL/FREE → PENDING_PAYMENT → ACTIVE → EXPIRING → EXPIRED/CANCELLED`.
- Khi `ACTIVE → EXPIRING`: nhắc gia hạn trong màn hình quota.
- Khi `→ EXPIRED`: hạ về Free 20/ngày ngay lập tức, session đang stream được hoàn tất (không cắt giữa chừng).

## 5. Rủi ro chu trình (cycle)
- QA → Quiz (lưu flashcard) → QA (hỏi từ flashcard): tránh vòng lặp tính quota 2 lần oan bằng cách chia quota chung `AiQuotaLedger`.
- Thống nhất 1 ledger cho cả QA và sinh Quiz để freemium minh bạch.

## 6. Khuyến nghị
- Version API nội bộ (`v1`) cho contract RAG/Billing, consumer QA pinned version.
- Dashboard liên module: quota dùng chung, tỉ lệ citation, report tồn đọng.

## 7. Kiểm thử tích hợp liên module
- IT-01: xóa `Document` giữa chừng → citation cũ vẫn mở được snapshot 30 ngày.
- IT-02: `Billing` timeout 10s → QA fail-closed 1 lượt tạm + đối soát sau.
- IT-03: LLM sập giả lập → fallback apology + hoàn quota 100%.
- IT-04: subscription hết hạn giữa stream → hoàn tất câu hiện tại rồi hạ Free.
- Mỗi IT chạy trên staging trước mỗi release minor.

## 8. Chủ sở hữu contract
- RAG: team Tài liệu; Quota: team Billing; Review Queue: team Admin.
