# 18 — Kế Hoạch Phát Hành: Gia Sư AI Hỏi Đáp

> Phát hành an toàn: feature flag, canary, rollback quota/LLM.

## 1. Phạm vi release 1.0
- Chat RAG + citation, quota Free 20/ngày, vote/report, retry/idempotency, kiểm duyệt.
- Loại trừ: xuất PDF (UC-14), numeric-check nâng cao (flag riêng).

## 2. Chiến lược triển khai
- Feature flag `qa-v1` (10% → 50% → 100% trong 5 ngày), kill-switch tắt gọi LLM giữ xem lịch sử.
- Canary theo plan: Free trước, Paid sau; theo dõi `dedupeHit`, `quotaRefund`, citation rate.
- Migration M-01→M-04 chạy trước giờ thấp điểm, vector index swap alias.

## 3. Tiêu chí Go/No-go
| Tiêu chí | Ngưỡng | Nguồn |
|---|---|---|
| Citation coverage staging | ≥ 95% / 200 câu | TC-03 |
| Race double-charge | 0 ca | TC-07 |
| Moderation bypass | 0 ca / 300 mẫu | TC-06 |
| TTFT P95 | < 4s | k6 |
| Tỉ lệ fallback thành công | ≥ 99% khi LLM lỗi giả lập | TC-09 |

## 4. Rollback
- Tắt flag `qa-v1` trong 2 phút; quota hold treo được job quét hoàn sau 60s.
- Rollback migration ngược M-04→M-01, giữ ledger 7 ngày; không xóa `ChatMessage` đã COMPLETED.
- Kịch bản provider sập: chuyển model fallback rẻ qua cấu hình, không cần deploy.

## 5. Giám sát sau release
- Alert: `QUOTA_EXCEEDED` tăng đột biến, `llmTimeoutRate > 5%`, `NOT_HELPFUL > 20%`.
- Dashboard: quota dùng/chặn/hoàn, latency, tokens, report tồn đọng.
- On-call: P1 provider sập, P2 citation drop, P3 vote spam.

## 6. Truyền thông
- Thông báo quota Free 20/ngày + giờ reset `Asia/Ho_Chi_Minh` trong app và paywall.
- Hướng dẫn retry an toàn và disclaimer AI hỗ trợ học tập.

## 7. Checklist ngày release
- [ ] Migration M-01→M-04 xong, vector alias đã swap.
- [ ] Flag `qa-v1` ở 10%, dashboard quota/citation mở sẵn.
- [ ] Runbook fallback model + job hoàn hold treo đã test.
- [ ] Paywall hiển thị đúng quota Free 20/ngày và giờ reset.
- [ ] On-call P1/P2 trực, kênh incident mở.

## 8. Sau 7 ngày canary
- Tổng kết citation, HELPFUL rate, refund, incident; quyết định mở 100%.
- Mọi P0/P1 tồn đọng phải đóng hoặc có workaround bằng văn bản.
- Lưu release note + biên bản Go/No-go vào `19-nhat-ky-thay-doi.md`.

## 9. Tiêu chí mở rộng 100%
- Không có P0 mở; citation ≥ 95%; refund/ngày < 2% tổng lượt hỏi.
