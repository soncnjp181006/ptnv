# 08 — Ảnh Hưởng Liên Module: Gói Dịch Vụ, Thanh Toán Và Hạn Mức

> Module 05 là cửa ngõ monetization; mọi module tốn chi phí đều phụ thuộc quota.

| Module liên quan | Chiều tác động | Nội dung | Cơ chế |
| :--- | :--- | :--- | :--- |
| 01 Xác thực & người dùng | Nhận từ 01 | Lấy `userId`, trạng thái tài khoản để gắn `Subscription` | `userId` FK, event `USER_CREATED` |
| 02 Tài liệu học tập | 05 chặn 02 | Upload vượt `maxDocs/storageMb` ➔ chặn `QUOTA_EXCEEDED` | `checkQuota(userId, DOC_UPLOAD)` đồng bộ |
| 03 Gia sư AI hỏi đáp | 05 chặn 03 | Hết `aiPerDay` ➔ chặn gọi LLM, gợi ý nâng cấp | `checkQuota(userId, AI_MESSAGE)` + `429 + upgradeUrl` |
| 04 Quiz/Flashcard | 05 chặn 04 | Hết `quizPerDay` ➔ chặn sinh Quiz AI | `checkQuota(userId, QUIZ_GEN)` |
| 06 Tiến độ & báo cáo | 05 ➔ 06 | Sự kiện `PLAN_UPGRADED/QUOTA_EXCEEDED/RENEWED` cho báo cáo | Event bus nội bộ |
| Thông báo (dùng chung) | 05 ➔ Notify | Quota 80%/100%, T-7/T-3/T-1, receipt, refund | Hàng đợi `notifyQueue` |
| Cổng thanh toán | 2 chiều | Tạo intent ➔ redirect ➔ webhook ➔ reconcile | HTTPS + webhook ký HMAC |

## Hợp đồng API nội bộ
- `GET /plans` — bảng giá công khai, cache 1h.
- `POST /orders {planId, cycle, channel, Idempotency-Key}` ➔ `{orderCode, paymentUrl, expiresAt}`.
- `POST /webhooks/{gateway}` — verify signature, ingest idempotent, trả 200 nhanh (<2s), xử lý nặng async.
- `GET /subscription/me` — gói hiện tại + `expiresAt` + `graceUntil` + quota còn lại.
- `POST /subscription/cancel-auto-renew`, `POST /refunds {orderCode, reason}`.

## Rủi ro liên module
- Quota service chậm ➔ mọi gọi AI treo: mitigations cache `Subscription` + circuit breaker fail-open đọc snapshot.
- Webhook gateway downtime ➔ đơn kẹt PENDING: mitigations job reconcile + nút "Tôi đã trả tiền" cho user kích hoạt đối soát thủ công.

## Trình tự phối hợp điển hình (mua gói ➔ dùng AI)
1. Client gọi `POST /orders` (module 05) ➔ nhận `paymentUrl` ➔ user trả tiền ở gateway.
2. Gateway bắn webhook ➔ module 05 verify + kích hoạt `ACTIVE` ➔ phát event nội bộ `PLAN_ACTIVATED`.
3. Module 03/04 nhận event ➔ nâng limit gọi AI trong cache quota của chúng.
4. Mỗi lần gọi AI: module 03 hỏi module 05 `checkQuota` (timeout 300ms, cache 60s) ➔ cho qua hoặc chặn 429.
5. Job 00:00 module 05 reset quota ➔ phát `QUOTA_RESET` để các module xóa cache.

## SLA và fallback giữa các module
| Cặp module | SLA | Fallback khi lỗi |
| :--- | :--- | :--- |
| 05 ➔ gateway | tạo intent < 3s | báo lỗi thân thiện, giữ Free, cho thử kênh khác |
| gateway ➔ 05 (webhook) | ingest < 2s | gateway retry + reconcile bù |
| 03/04 ➔ 05 (checkQuota) | < 300ms | đọc snapshot cache 60s; quá 5s fail-open có alert |
| 05 ➔ Notify | enqueue < 1s | retry queue 5 lần, rồi ghi log để gửi bù |

## Nguyên tắc chống vòng lặp phụ thuộc
- Module 05 không gọi ngược vào logic AI/Quiz; chỉ phát event một chiều.
- Module 03/04 không tự ý nâng quota; mọi nâng/hạ quota chỉ từ event của module 05.

## Ma trận trách nhiệm (RACI rút gọn)
| Công việc | Module 05 | Module 03/04 | Notify | Support |
| :--- | :--- | :--- | :--- | :--- |
| Trừ quota khi gọi AI | A (quyết định) | R (gọi check) | — | — |
| Gửi tin hết quota/hết hạn | A | — | R (gửi) | — |
| Đối soát đơn UNKNOWN | R/A | — | — | C (xử lý tay) |
| Duyệt hoàn tiền | C (cung cấp dữ liệu) | — | I (báo user) | R/A |

## Thứ tự khởi động hệ thống (boot order)
1. Module 01 (user) và 05 (gói/quota) lên trước vì là nền tảng.
2. Module 03/04 lên sau, đọc snapshot quota lúc khởi động.
3. Job reconcile/reset/notify lên cuối cùng, sau khi DB sẵn sàng.
