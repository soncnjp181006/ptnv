# 09 — Ma Trận Truy Vết: Xác Thực & Người Dùng

> Truy vết Yêu cầu → Use case → Quy tắc → Test → Log.

| Req (Nhu cầu PHHS/học sinh) | Use Case | Quy tắc (BR) | Test case | Trạng thái |
|-----------------------------|----------|--------------|-----------|------------|
| Đăng ký nhanh bằng Gmail | UC-AUTH-03 `loginWithGoogle` | BR-AUTH-04 | TC-AUTH-03-01 verify id_token | Covered |
| Bảo vệ tài khoản học sinh | UC-AUTH-02 login | BR-AUTH-01/02 | TC-AUTH-02-02 lock 5 lần | Covered |
| Chống nick ảo spam AI | UC-AUTH-04 verify | BR-AUTH-03 | TC-AUTH-04-01 quota 5 câu khi chưa verify | Covered |
| Giữ phiên học liên tục | UC-AUTH-05 refresh | BR-AUTH-05 | TC-AUTH-05-01 reuse detection | Covered |
| Khóa nick gian lận Quiz | UC-AUTH-11 suspend | BR-AUTH-06 | TC-AUTH-11-01 suspend + audit | Covered |
| Nâng gói ôn thi 99k | UC-AUTH-09 upgrade | BR-AUTH-07 | TC-AUTH-09-01 Free→Paid mở quota | Covered |
| Quyền xóa dữ liệu | UC-AUTH-13 delete | BR-AUTH-08 | TC-AUTH-13-01 purge sau 30 ngày | Covered |
| Tra cứu vi phạm | UC-AUTH-14 audit | BR-AUTH-06 | TC-AUTH-14-01 lọc theo reason_code | Partial |

## Độ bao phủ
- 8 req → 8 UC → 8 BR → 8 TC. Covered 7/8, Partial 1 (audit filter nâng cao dời Sprint 2).
- Mọi req đều trỏ tới `audit_logs` để phục vụ kiểm tra của Admin/giáo viên.
