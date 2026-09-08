# 15 — Backlog & Tiêu Chí Nghiệm Thu: Xác Thực & Người Dùng

## 1. Backlog (MoSCoW)

| ID | Story (Vai trò học tập) | Ưu tiên | Acceptance Criteria |
|----|-------------------------|---------|---------------------|
| US-AUTH-01 | Là học sinh, tôi đăng ký email để học Free | Must | 201, ACTIVE_FREE, quota 20 AI/ngày, mail verify 24h |
| US-AUTH-02 | Là học sinh, tôi login Google 1-chạm | Must | Lần đầu auto-Free, lần sau login < 2s |
| US-AUTH-03 | Là learner, tôi quên pass lấy lại được | Must | Link 30p, 1 lần dùng, revoke session cũ |
| US-AUTH-04 | Là learner, tôi sửa khối lớp + môn học | Should | AI gợi ý đúng khối sau khi lưu |
| US-AUTH-05 | Là learner Free, tôi nâng Paid 99k để ôn thi | Must | ACTIVE_PAID + mở quota ngay sau webhook |
| US-AUTH-06 | Là Admin, tôi khóa nick spam AI | Must | SUSPENDED + reason_code + audit, token revoke |
| US-AUTH-07 | Là learner, tôi xóa tài khoản | Should | DELETED, ẩn 30 ngày rồi purge PII |
| US-AUTH-08 | Là Admin, tôi tra audit | Should | Lọc user/action/ngày, p95 < 1s |

## 2. Định nghĩa xong (DoD)
- API có OpenAPI + mã lỗi tiếng Việt; unit ≥ 80%, e2e register→login→quota→AI 1 câu pass.
- Audit ghi đủ 100% suspend/upgrade/delete; log timezone `Asia/Ho_Chi_Minh`.
- Không lưu password plaintext; secret JWT qua KMS/env, không commit repo.

## 3. Ước lượng
- Tổng ~21 story-point: lõi 13, OAuth+hồ sơ 5, quản trị 3. 2 dev × 3–4 tuần.
