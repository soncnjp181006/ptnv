# 05 — Bộ Quy Tắc Nghiệp Vụ: Xác Thực & Quản Lý Người Dùng

> Format chuẩn: BR-XXX / WHEN / MUST / OTHERWISE / AFFECTS / SOURCE / STATUS.

## BR-AUTH-01 — Mật khẩu mạnh
- **WHEN:** Learner đăng ký / đổi mật khẩu bằng email.
- **MUST:** Password ≥ 8 ký tự, gồm chữ + số; hash `bcrypt(cost=12)`, không lưu plaintext.
- **OTHERWISE:** Từ chối 400 `WEAK_PASSWORD`, gợi ý mẫu mạnh.
- **AFFECTS:** `users.password_hash`, UC-AUTH-01/07.
- **SOURCE:** Quy ước bảo mật dự án. **STATUS:** APPROVED.

## BR-AUTH-02 — Khóa sau 5 lần sai
- **WHEN:** Login sai password 5 lần liên tiếp.
- **MUST:** Đặt `locked_until = now(Asia/Ho_Chi_Minh) + 15 phút`, trả 429.
- **OTHERWISE:** Kẻ dò mật khẩu có thể brute-force.
- **AFFECTS:** `users.failed_attempts`, UC-AUTH-02.
- **SOURCE:** Quy ước bảo mật. **STATUS:** APPROVED.

## BR-AUTH-03 — Email verify giới hạn AI
- **WHEN:** `email_verified = false` ở ACTIVE_FREE.
- **MUST:** Giới hạn 5 câu AI/ngày cho tới khi verify (token 24h).
- **OTHERWISE:** Cho full 20 câu/ngày như đã verify.
- **AFFECTS:** `QuotaService.check`, UC-AUTH-04.
- **SOURCE:** Chống spam tài khoản ảo. **STATUS:** APPROVED.

## BR-AUTH-04 — Google OAuth chuẩn
- **WHEN:** Login Google.
- **MUST:** Verify `id_token` với Google JWKS, đối chiếu `aud`, `exp`; map `sub → oauth_sub`.
- **OTHERWISE:** Từ chối 401 `OAUTH_INVALID`.
- **AFFECTS:** UC-AUTH-03/15. **SOURCE:** Google OAuth2 spec. **STATUS:** APPROVED.

## BR-AUTH-05 — Phiên ngắn + xoay refresh
- **WHEN:** Cấp phiên.
- **MUST:** `access_token` 15 phút, `refresh_token` 30 ngày, xoay vòng + thu hồi token cũ (reuse detection).
- **OTHERWISE:** Thu hồi toàn bộ session của user khi phát hiện reuse.
- **AFFECTS:** `sessions`, UC-AUTH-05/06. **SOURCE:** OWASP session. **STATUS:** APPROVED.

## BR-AUTH-06 — Suspend phải có lý do + audit
- **WHEN:** Admin khóa/mở/xóa user.
- **MUST:** Nhập `reason_code` (SPAM_AI/ABUSE/FRAUD/REQUEST) và ghi `audit_logs`.
- **OTHERWISE:** Chặn thao tác 400 `REASON_REQUIRED`.
- **AFFECTS:** UC-AUTH-11/12/13. **SOURCE:** Quy ước quản trị. **STATUS:** APPROVED.

## BR-AUTH-07 — Quota Free mặc định
- **WHEN:** Tạo ACTIVE_FREE.
- **MUST:** Cấp 20 câu AI/ngày, 10 tài liệu, 50MB, 3 bộ Quiz/ngày; reset 00:00 Asia/Ho_Chi_Minh.
- **OTHERWISE:** Không cho hỏi AI (403 `QUOTA_EXCEEDED`).
- **AFFECTS:** `quotas`, Billing. **SOURCE:** Quy ước freemium đã chốt. **STATUS:** APPROVED.

## BR-AUTH-08 — Xóa mềm 30 ngày
- **WHEN:** User yêu cầu xóa.
- **MUST:** Soft-delete, ẩn hồ sơ, purge PII sau 30 ngày, giữ audit ẩn danh 12 tháng.
- **OTHERWISE:** Không được xóa cứng ngay.
- **AFFECTS:** `users.status=DELETED`. **SOURCE:** PDPA/VN privacy. **STATUS:** APPROVED.
