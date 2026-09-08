# 03 — Ma Trận Kịch Bản: Gia Sư AI Hỏi Đáp

> Module: `ai-tutor-qa` | Bao phủ: quota, idempotency, hallucination, retry, concurrent, timeout.

## 1. Nguyên tắc
- Mỗi Use Case có ít nhất 1 main + 2 alternative/exception.
- Mã kịch bản: `SC-<UC>-<NN>`; mức độ: `MAIN` / `ALT` / `EXC`.

## 2. Ma trận kịch bản
| Mã SC | Use Case | Loại | Kịch bản | Kết quả mong đợi |
|---|---|---|---|---|
| SC-UC01-01 | UC-01 | MAIN | Hỏi hợp lệ, còn quota, RAG đủ ngữ cảnh | Trả lời + citation, trừ 1 quota |
| SC-UC01-02 | UC-01 | ALT | Hỏi vượt 2000 ký tự | 400 + yêu cầu rút gọn |
| SC-UC01-03 | UC-01 | EXC | Input độc hại (toxicity cao) | 422 + thông báo + log kiểm duyệt |
| SC-UC02-01 | UC-02 | MAIN | Free còn 5/20 → hold → commit | `remainingToday=4` |
| SC-UC02-02 | UC-02 | EXC | Hết quota (0/20) | 429 `QUOTA_EXCEEDED` + paywall |
| SC-UC02-03 | UC-02 | EXC | Hết quota giữa chừng (2 tab concurrent) | 1 thắng + commit, 1 thua + rollback hold + 429 |
| SC-UC02-04 | UC-02 | ALT | Paid fair-use vượt 500/ngày | 429 mềm + đề nghị liên hệ, không khóa tài khoản |
| SC-UC03-01 | UC-03 | MAIN | Top-K=5, similarity >= 0.72 | Dựng prompt đủ citation |
| SC-UC03-02 | UC-03 | ALT | Không chunk nào đạt ngưỡng | Trả lời thành thật "ngoài tài liệu", vẫn trừ quota |
| SC-UC03-03 | UC-03 | EXC | Vector DB timeout 5s | Fallback: dùng cache chunk gần nhất hoặc báo thử lại |
| SC-UC04-01 | UC-04 | MAIN | Stream SSE đầy đủ + SourceRef | Client render citation click được |
| SC-UC04-02 | UC-04 | ALT | Output LLM thiếu citation | Chặn xuất bản, yêu cầu regenerate 1 lần |
| SC-UC04-03 | UC-04 | EXC | Hallucination bị phát hiện (số liệu không có trong chunk) | Gắn cờ `NEEDS_REVIEW`, vẫn hiển thị kèm cảnh báo |
| SC-UC05-01 | UC-05 | MAIN | Vote HELPFUL lần đầu | Ghi `FeedbackVote`, cập nhật helpfulRate |
| SC-UC05-02 | UC-05 | ALT | Vote lại cùng message | 409, giữ vote cũ (muốn đổi phải PATCH) |
| SC-UC06-01 | UC-06 | MAIN | Báo sai hợp lệ, < 5/ngày | Tạo `AnswerReport PENDING` |
| SC-UC06-02 | UC-06 | EXC | Spam báo cáo (> 5/ngày) | 429 + khóa nút báo cáo đến hôm sau |
| SC-UC09-01 | UC-09 | EXC | Output LLM độc hại | Thay bằng câu từ chối mẫu, log `moderationBlock` |
| SC-UC10-01 | UC-10 | MAIN | LLM timeout 30s nhưng local đã lưu (local success) | Retry cùng key trả cached, không trừ quota thêm |
| SC-UC10-02 | UC-10 | ALT | Timeout và chưa lưu gì (partial completion) | Retry 1 lần exponential 2s; thất bại → fallback xin lỗi + hoàn quota |
| SC-UC10-03 | UC-10 | EXC | Retry với key khác | Tính là câu hỏi mới, trừ quota mới |
| SC-UC13-01 | UC-13 | EXC | 5 concurrent cùng User Free | Hàng đợi: xử lý tuần tự, request vượt bị 429 kèm `Retry-After` |
| SC-UC12-01 | UC-12 | MAIN | Xem quota còn lại | Trả `usedToday/remainingToday/resetAt` theo `Asia/Ho_Chi_Minh` |
| SC-UC11-01 | UC-11 | MAIN | Admin duyệt CONFIRMED | Gắn nhãn chunk xấu, tạo task cải thiện RAG |

## 3. Kịch bản retry sau partial completion (chi tiết)
1. Client gửi `POST /chat/messages` kèm `Idempotency-Key: k1`.
2. Server hold quota → gọi LLM → stream đứt giữa chừng, client chỉ nhận 40% tokens.
3. Client retry cùng `k1` sau 2s; server thấy `k1` ở trạng thái `PROCESSING` → chờ tối đa 25s.
4. Nếu bản đầu hoàn tất muộn (local success): trả kết quả đầy đủ, không trừ quota thêm.
5. Nếu bản đầu thất bại: thực hiện đúng 1 lần gọi LLM mới, hold cũ tái sử dụng.
6. Nếu retry cũng thất bại: rollback hold, trả `503 + fallbackMessage`, client hiển thị nút "Thử lại" (sinh key mới).

## 4. Kịch bản concurrent request
- Dùng `Redis lock per userId` TTL 35s; request thứ 2 cùng user chờ lock tối đa 5s rồi 429.
- Atomic decrement bằng transaction DB (`UPDATE ... WHERE remaining > 0`) chống race.

## 5. Độ phủ
- Tổng 24 kịch bản: 7 MAIN, 8 ALT, 9 EXC; 100% UC Must có EXC quota/timeout.
