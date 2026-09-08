# 08 — Ảnh Hưởng Liên Module: Quiz & Flashcard

> Liên kết: Tài liệu, Quota/Billing, QA chat, Thông báo, Lớp học.

## 1. Ma trận phụ thuộc
| Module | Hướng | Tích hợp | Mức |
|---|---|---|---|
| Tài liệu `Document` | Inbound | Ngữ liệu RAG sinh đề; doc xóa → đề cũ giữ snapshot | Cao |
| Billing/Quota | Inbound | Ledger chung với BV-03; hết hạn hạ Free 10 câu/lần | Cao |
| Gia sư QA `BV-03` | Hai chiều | Nút "Hỏi AI câu này" từ câu sai; quota chung | Cao |
| Thông báo | Outbound | Nhắc ôn SM-2 đến hạn, báo đề đã sinh xong | Trung bình |
| Lớp học/Teacher | Outbound | Teacher phát hành `PUBLISHED` cho lớp | Trung bình |
| LLM Provider | Outbound | Sinh JSON đề/thẻ; timeout 45s | Cao |

## 2. Contract
- `RagService.getChunks(docIds, k)` timeout 5s; `BillingService.hold/commit/refund` idempotent.
- Event: `quiz.generated {quizId}`, `attempt.scored {attId, score}`, `deck.due {userId, count}`.
- `QuizSet` version bất biến sau SCORED đầu tiên (muốn sửa → clone bản mới).

## 3. Lan truyền lỗi
| Sự cố | Ảnh hưởng | Ứng phó |
|---|---|---|
| Vector DB chậm | Sinh đề timeout | Retry 1 + hoàn quota |
| Billing timeout | Không hold được | Fail-closed: 1 lượt tạm + đối soát |
| LLM JSON lỗi liên tục | Không ra đề | Fallback mẫu offline dễ + alert |
| Doc bị xóa | SourceRef gãy | Snapshot chunk theo đề 90 ngày |
| Concurrent QA+Quiz cạn quota | 1 bên 429 | Rollback bên thua, gợi ý nâng cấp |

## 4. Subscription
- `ACTIVE → EXPIRING`: nhắc gia hạn khi sinh đề; `→ EXPIRED`: hạ 20→10 câu/lần ngay.
- Attempt đang `IN_PROGRESS` khi hết hạn vẫn được nộp/chấm (không cắt giữa chừng).

## 5. Chu trình tránh
- Quiz → QA (hỏi câu sai) → Quiz (sinh lại từ giải thích): quota chung ngăn lạm dụng vòng tròn.
- Không tự động sinh đề từ lịch sử chat (tránh đốt quota ngầm); phải có nút bấm яв.

## 6. Khuyến nghị
- Pinned API version RAG/Billing; dashboard chung quota QA+Quiz theo user/ngày.

## 7. Kiểm thử tích hợp liên module
- IT-01: doc bị xóa sau khi ra đề → đề cũ vẫn mở snapshot 90 ngày.
- IT-02: Billing timeout → sinh đề fail-closed + đối soát, attempt cũ vẫn nộp được.
- IT-03: LLM JSON lỗi giả lập 100% → rollback quota + dùng đề mẫu offline.
- IT-04: hết hạn Paid giữa IN_PROGRESS → vẫn nộp/chấm, lần sinh sau hạ Free.
- Chạy đủ IT-01→IT-04 trên staging trước M3.

## 8. Chủ sở hữu contract
- RAG: team Tài liệu; Ledger: team Billing; Reminder SM-2: team Notification.
- Contract đổi version phải thông báo trước 1 sprint cho consumer QA/Quiz.
