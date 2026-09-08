# 07 — Ảnh Hưởng Dữ Liệu và Tác Động: Gia Sư AI Hỏi Đáp

> Phạm vi: entity, migration, quota ledger, RAG index, log.

## 1. Mô hình dữ liệu liên quan
| Entity | Khóa chính | Trường quan trọng | Ghi chú |
|---|---|---|---|
| `User` | `userId` | `plan FREE/PAID` | Liên kết subscription |
| `ChatSession` | `sessId` | `userId, status, version` | Optimistic locking |
| `ChatMessage` | `msgId` | `sessId, role, text, tokens, latencyMs` | USER/ASSISTANT |
| `SourceRef` | `refId` | `msgId, documentId, chunkId, page` | Citation |
| `RagChunk` | `chunkId` | `documentId, embedding, page, text` | Vector index |
| `AiQuotaLedger` | `(userId, date)` | `used, remaining, refunded` | Ngày `Asia/Ho_Chi_Minh` |
| `IdempotencyRecord` | `(userId, key)` | `status, responseRef, ttl` | TTL 24h |
| `FeedbackVote` | `(msgId, userId)` | `value HELPFUL/NOT_HELPFUL` | Unique 1 vote |
| `AnswerReport` | `repId` | `msgId, reason, status` | PENDING→CONFIRMED/... |
| `LlmCallLog` | `callId` | `tokens, latency, error, dedupeHit` | Quan trắc chi phí |

## 2. Tác động CRUD theo Use Case
| Use Case | Create | Read | Update | Delete |
|---|---|---|---|---|
| UC-01 Gửi hỏi | `ChatMessage` USER | `AiQuotaLedger` | hold quota | — |
| UC-04 Nhận đáp | `ChatMessage` ASSISTANT + `SourceRef` | `RagChunk` | commit quota | — |
| UC-05 Vote | `FeedbackVote` | `ChatMessage` | helpfulRate | — |
| UC-06 Báo sai | `AnswerReport` | `ChatMessage` | — | — |
| UC-10 Retry | — | `IdempotencyRecord` | PROCESSING→SUCCEEDED | — |

## 3. Migration dự kiến
- `M-01`: tạo `chat_session`, `chat_message`, `source_ref` (index `sessId, createdAt`).
- `M-02`: tạo `ai_quota_ledger (userId, date)` unique + check `remaining >= 0`.
- `M-03`: tạo `idempotency_record` TTL index 24h.
- `M-04`: vector index `rag_chunk.embedding` (HNSW), cột `page`, `documentId`.
- Rủi ro migration: vector index nặng → build offline rồi swap alias, không block đọc.

## 4. Tác động quota và concurrent
- Atomic decrement chống trừ âm; lock Redis per user TTL 35s.
- Hết quota giữa chừng: rollback hold, không để ledger treo `QUOTA_HELD` quá 60s (job quét).
- Reset ngày mới: job `00:00 Asia/Ho_Chi_Minh`, idempotent theo `(userId, date)`.

## 5. Tác động hallucination và kiểm duyệt
- `SourceRef` bắt buộc để truy vết câu nào từ chunk nào; thiếu → gắn `NEEDS_REVIEW`.
- `ModerationLog` lưu input/output vi phạm (che PII), phục vụ audit giáo dục.

## 6. Dung lượng và hiệu năng
- `ChatMessage.text` giới hạn 8KB; stream flush mỗi 200ms để giảm TTFB.
- Giữ `LlmCallLog` 90 ngày (hot) rồi archive; P95 truy hồi RAG mục tiêu `< 1.2s`.

## 7. Backfill và rollback
- Backfill `helpfulRate` từ `FeedbackVote` hiện có bằng job batch.
- Rollback migration theo thứ tự ngược M-04 → M-01, giữ backup ledger 7 ngày.
