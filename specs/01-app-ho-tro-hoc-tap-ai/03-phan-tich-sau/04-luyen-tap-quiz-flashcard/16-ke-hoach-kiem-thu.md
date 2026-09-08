# 16 — Kế Hoạch Kiểm Thử: Quiz & Flashcard

> Mock LLM JSON (đủ/thiếu/2 đáp án đúng/timeout), fake Billing, freeze giờ VN.

## 1. Chiến lược
- Fixture: 3 tài liệu mẫu (sử/địa/sinh) + bộ 300 câu duyệt tay làm oracle.
- Test state machine attempt đủ DRAFT→IN_PROGRESS→SUBMITTED→SCORED + EXPIRED/ABANDONED.
- Clock freeze `Asia/Ho_Chi_Minh` quanh reset và deadline attempt.

## 2. Test case
| Mã | Mục tiêu | Bước | Oracle |
|---|---|---|---|
| TQ-01 | Sinh Quiz happy path | Sinh 10 MEDIUM còn quota | DRAFT 10 câu chuẩn, trừ 1 |
| TQ-02 | Sinh deck | Sinh 20 thẻ | Đủ trước/sau, trừ 1 |
| TQ-03 | Hết quota sinh | Dùng cạn 20 rồi sinh | 429 + paywall, không LLM |
| TQ-04 | Làm + chấm | Start → trả lời → nộp | SCORED điểm đúng, < 1s |
| TQ-05 | Guard giải thích | Xem sớm vs sau chấm | 403 rồi 200 + SourceRef |
| TQ-06 | Làm lại/ôn sai/SM-2/history | Tạo attempt con + ôn GOOD | Không quota, streak tăng |
| TQ-07 | Quota chung QA+Quiz race | Hỏi QA + sinh Quiz đồng thời khi còn 1 | 1 thắng 1 429, ledger ≥ 0 |
| TQ-08 | Báo câu sai | Báo 6 lần/ngày | 5 pass, lần 6 429 |
| TQ-09 | Retry partial/timeout | Timeout có/không nháp, partial 7/10 | Cached / 1 retry / rollback |
| TQ-10 | Double-submit | 2 submit cùng version | 1 SCORED + 1 409 |
| TQ-11 | Validate đáp án | JSON 2 correct + số liệu bịa | Regenerate/NEEDS_REVIEW |
| TQ-12 | Deadline/EXPIRED | Quá deadline chưa nộp | Auto-submit → SCORED phần đã làm |

## 3. Phi chức năng
- k6: 50 job sinh đồng thời, P95 khởi job < 2s; 200 submit đồng thời 0 double-SCORED.
- Bảo mật: sai owner attempt → 403; thiếu key → 400; xem giải thích sớm → 403.
- Bền bỉ: LLM fail 100% 5 phút → 100% rollback + alert.

## 4. Môi trường/dữ liệu
- Staging LLM sandbox JSON mode; vector snapshot; cấm sinh production tốn phí.
- Bộ độc hại/PII riêng cho đề sinh từ tài liệu nhạy cảm.

## 5. Gate
- 12/12 pass, JSON ≥ 98%, đáp án ≥ 97%/300, race 0 lỗi.

## 6. Lịch chạy
- Unit/integration mỗi PR qua CI, chặn merge khi fail.
- TQ-01→TQ-06 nightly; TQ-07→TQ-12 trước M2/M3; k6 cuối sprint 2.
- Kết quả tải đính kèm báo cáo M2/M3.

## 7. Quản lý defect
- P0 (mất bài, trừ quota oan): fix < 24h, hotfix không chờ sprint.
- P1 (JSON lỗi, nộp đôi): fix trong sprint hiện tại.
- P2 (streak, reminder): dời tối đa 1 sprint, ghi debt.

## 8. Vai trò
- QA lead giữ bộ 300 câu + 3 tài liệu mẫu; BE bổ sung mock JSON lỗi.
