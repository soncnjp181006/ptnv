# 16 — Kế Hoạch Kiểm Thử: Xác Thực & Người Dùng

## 1. Phạm vi kiểm thử học tập
- Luồng: register → verify → login → hỏi AI 1 câu → nâng Paid → suspend → xóa.
- Dữ liệu test: email ảo `@test.edu.vn`, Google test account, khối 10–12, môn Toán/Lý/Anh.

## 2. Các lớp test

| Lớp | Trường hợp | Dữ liệu | Kỳ vọng |
|-----|------------|---------|---------|
| Unit | `hashPassword`, `verifyGoogleToken` | pass yếu/mạnh, token exp | Hash khác nhau, reject exp |
| Integration | `POST /auth/register → initQuota` | Email mới | Quota 20 AI/ngày, 50MB |
| Contract | Billing webhook → ACTIVE_PAID | payload 99k/990k | Mở quota Paid |
| E2E | Google login lần đầu → hỏi AI | id_token mock | 200 + quota trừ 1 |
| Security | Brute-force 6 lần, reuse refresh | script | 429 lock, revoke all khi reuse |
| Performance | 500 concurrent login khai giảng | k6 | p95 < 800ms, lỗi < 1% |
| Business | Chưa verify chỉ 5 câu AI | flag false | Câu 6 bị 403 QUOTA |

## 3. Môi trường & dữ liệu
- Env staging clone prod, DB seed 10k users Free/Paid/SUSPENDED; mail trap, Google OAuth sandbox.
- Không dùng dữ liệu học sinh thật; PII test che `***`.

## 4. Cổng chất lượng
- Pass ≥ 95% TC, không còn bug Critical; lock, suspend, purge 30 ngày đều có bằng chứng log.
